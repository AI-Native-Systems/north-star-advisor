# [PROJECT_NAME]: Handoff Protocol

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Formalized agent-to-agent handoff schema, validation, and recovery patterns.

---

## 1. Handoff Protocol Overview

The handoff protocol enables **dynamic delegation between specialized agents** when:
- An agent encounters missing required inputs
- An agent determines another agent is better suited for a subtask
- Validation fails and remediation requires different expertise

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      HANDOFF PROTOCOL FLOW                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Agent A                    Orchestrator                    Agent B    │
│   ────────                   ────────────                   ────────    │
│      │                            │                            │        │
│      │  1. HandoffRequest         │                            │        │
│      │ ─────────────────────────▶ │                            │        │
│      │                            │                            │        │
│      │                            │  2. Validate & Route       │        │
│      │                            │ ──────────────────────────▶│        │
│      │                            │                            │        │
│      │                            │  3. HandoffResponse        │        │
│      │                            │ ◀──────────────────────────│        │
│      │                            │                            │        │
│      │  4. Resume with context    │                            │        │
│      │ ◀───────────────────────── │                            │        │
│      │                            │                            │        │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Handoff Schema

> **Note**: Adapt schemas to your chosen language and validation library.

### 2.1 HandoffRequest Schema

```
// src/pipeline/handoff/schema

HANDOFF_SCHEMA_VERSION = '1.0.0'

HandoffRequestSchema:
  // Metadata
  schemaVersion: literal(HANDOFF_SCHEMA_VERSION)
  traceId: uuid
  timestamp: datetime

  // Source agent
  sourceAgent: enum([AGENT_IDS])
  sourceContext:
    documentType: string (optional)
    currentStep: number
    attemptNumber: number

  // Handoff decision
  canHandle: literal(false)
  handoffTo: enum([AGENT_IDS] + ['human'])

  // Reason and requirements
  reason: enum([
    'missing_required_input',
    'validation_failure',
    'expertise_mismatch',
    'resource_exhausted',
    'requires_human_decision',
  ])
  reasonDetail: string

  // What the target agent needs to provide
  missingInputs: array (optional):
    key: string
    expectedType: string
    description: string
    source: string (optional)  // Which doc/phase should provide this

  // Context to pass to target agent
  contextForTarget:
    summary: string
    relevantOutputs: record (optional)
    priorAttempts: array (optional):
      attempt: number
      action: string
      result: string

  // Urgency and timeout
  priority: enum(['low', 'medium', 'high', 'critical'])
  timeoutMs: number (optional)
```

### 2.2 HandoffResponse Schema

```
HandoffResponseSchema:
  // Metadata
  schemaVersion: literal(HANDOFF_SCHEMA_VERSION)
  traceId: uuid  // Same as request
  timestamp: datetime

  // Response agent
  respondingAgent: enum([AGENT_IDS] + ['human'])

  // Resolution
  status: enum(['resolved', 'partial', 'failed', 'escalated'])

  // Provided data
  providedInputs: record (optional)

  // For partial/failed
  unresolved: array of strings (optional)
  failureReason: string (optional)

  // Recommendations for source agent
  recommendations: array of strings (optional)

  // Execution metadata
  executionTimeMs: number
  tokensUsed: number (optional)
```

### 2.3 HandoffDecision Schema

```
HandoffDecisionSchema:
  canHandle: boolean
  confidence: number (0 to 1)

  // If canHandle is false:
  handoffTo: enum([AGENT_IDS] + ['human']) (optional)
  reason: string (optional)
  missingInputs: array of strings (optional)
```

---

## 3. Handoff Routes

### 3.1 Valid Handoff Paths

> **Note**: Customize based on your agent topology from ARCHITECTURE_BLUEPRINT.md

| Source Agent | Can Handoff To | Typical Reason |
|--------------|----------------|----------------|
| `[agent_1]` | `[agent_3]` | Missing [DATA_TYPE_1] data |
| `[agent_1]` | `[agent_2]` | Pre-validation check needed |
| `[agent_1]` | `orchestrator` | Missing user input |
| `[agent_2]` | `[agent_1]` | Regeneration needed |
| `[agent_2]` | `orchestrator` | Validation threshold not met |
| `[agent_3]` | `orchestrator` | Query clarification needed |
| **Any Agent** | `human` | Critical decision or low confidence |

### 3.2 Invalid Handoff Paths (Anti-patterns)

| Invalid Path | Why Prohibited |
|--------------|----------------|
| `[agent_3]` → `[agent_1]` | [REASON: e.g., Research doesn't trigger generation] |
| `[agent_2]` → `[agent_3]` | [REASON: e.g., Validation doesn't trigger research] |
| Circular: A → B → A (same request) | Infinite loop risk |

### 3.3 Handoff Limits

```
// src/pipeline/handoff/limits

HANDOFF_LIMITS:
  // Maximum handoffs per [ITEM] processing
  maxHandoffsPerItem: 3

  // Maximum total handoffs per pipeline run
  maxHandoffsPerPipeline: 10

  // Timeout for handoff resolution
  handoffTimeoutMs: 30_000

  // Cooldown before same handoff path can be used again
  handoffCooldownMs: 5_000
```

---

## 4. Handoff Implementation

### 4.1 Handoff Manager

```
// src/pipeline/handoff/manager

class HandoffManager:
  handoffCount: Map<string, number>
  handoffHistory: HandoffRequest[]

  // Process a handoff request from an agent
  processHandoff(request: HandoffRequest): HandoffResponse:
    // 1. Validate request schema
    validated = HandoffRequestSchema.parse(request)

    // 2. Emit trace event
    emitTraceEvent:
      type: 'handoff.request'
      attributes:
        traceId: validated.traceId
        source: validated.sourceAgent
        target: validated.handoffTo
        reason: validated.reason

    // 3. Check handoff limits
    itemKey = validated.sourceContext.documentType OR 'global'
    currentCount = this.handoffCount.get(itemKey) OR 0

    if currentCount >= HANDOFF_LIMITS.maxHandoffsPerItem:
      return this.createFailedResponse(validated, 'Handoff limit exceeded')

    // 4. Check for circular handoffs
    if this.detectCircularHandoff(validated):
      return this.createFailedResponse(validated, 'Circular handoff detected')

    // 5. Route to appropriate handler
    response = this.routeHandoff(validated)

    // 6. Update tracking
    this.handoffCount.set(itemKey, currentCount + 1)
    this.handoffHistory.push(validated)

    // 7. Emit response trace
    emitTraceEvent:
      type: 'handoff.response'
      attributes:
        traceId: validated.traceId
        status: response.status
        executionTimeMs: response.executionTimeMs

    return response

  routeHandoff(request: HandoffRequest): HandoffResponse:
    startTime = currentTime()

    switch request.handoffTo:
      case '[agent_3]':  // e.g., research_agent
        return this.handleResearchHandoff(request, startTime)

      case '[agent_1]':  // e.g., document_generator
        return this.handleRegenerationHandoff(request, startTime)

      case '[agent_2]':  // e.g., validator
        return this.handleValidationHandoff(request, startTime)

      case 'orchestrator':
        return this.handleOrchestratorHandoff(request, startTime)

      case 'human':
        return this.handleHumanEscalation(request, startTime)

      default:
        return this.createFailedResponse(request, 'Unknown target')

  handleHumanEscalation(request, startTime): HandoffResponse:
    // Emit escalation event for UI to handle
    emitTraceEvent:
      type: 'handoff.human_escalation'
      attributes:
        traceId: request.traceId
        reason: request.reason
        reasonDetail: request.reasonDetail
        priority: request.priority

    // Return escalated status - UI will handle
    return:
      schemaVersion: HANDOFF_SCHEMA_VERSION
      traceId: request.traceId
      timestamp: now()
      respondingAgent: 'human'
      status: 'escalated'
      recommendations:
        - 'Review the processing context'
        - 'Provide missing inputs via the UI'
        - 'Resume processing when ready'
      executionTimeMs: currentTime() - startTime

  detectCircularHandoff(request): boolean:
    // Check last 3 handoffs for same source-target pair
    recentHandoffs = this.handoffHistory.slice(-3)
    samePathCount = recentHandoffs.filter(
      h => h.sourceAgent == request.sourceAgent AND
           h.handoffTo == request.handoffTo AND
           h.reason == request.reason
    ).length

    return samePathCount >= 2

  createFailedResponse(request, reason): HandoffResponse:
    return:
      schemaVersion: HANDOFF_SCHEMA_VERSION
      traceId: request.traceId
      timestamp: now()
      respondingAgent: 'orchestrator'
      status: 'failed'
      failureReason: reason
      executionTimeMs: 0
```

### 4.2 Agent Handoff Integration

```
// src/pipeline/agents/[agent_1] (partial example)

class [Agent1]:
  handoffManager: HandoffManager

  // Check if we can handle this or need to handoff
  assessHandoff(config, state): HandoffDecision:
    missingInputs = this.findMissingInputs(config, state)

    if missingInputs.length == 0:
      return { canHandle: true, confidence: 0.9 }

    // Determine if missing inputs are researchable or need user input
    researchable = missingInputs.filter(i =>
      i.source == 'external' OR i.source == 'market'
    )
    needsUser = missingInputs.filter(i =>
      i.source == 'user' OR i.source == 'decision'
    )

    if researchable.length > 0 AND needsUser.length == 0:
      return:
        canHandle: false
        confidence: 0.7
        handoffTo: '[agent_3]'  // research agent
        reason: 'Missing research data'
        missingInputs: researchable.map(i => i.key)

    if needsUser.length > 0:
      return:
        canHandle: false
        confidence: 0.5
        handoffTo: 'orchestrator'
        reason: 'Missing user decision'
        missingInputs: needsUser.map(i => i.key)

    return { canHandle: true, confidence: 0.6 }

  process(config, state): Result:
    // Step 1: Assess if handoff needed
    decision = this.assessHandoff(config, state)

    if NOT decision.canHandle:
      // Step 2: Create and send handoff request
      request = HandoffRequest:
        schemaVersion: HANDOFF_SCHEMA_VERSION
        traceId: state.generationId
        timestamp: now()
        sourceAgent: '[agent_1]'
        sourceContext:
          documentType: config.type
          currentStep: state.currentStep
          attemptNumber: 1
        canHandle: false
        handoffTo: decision.handoffTo
        reason: 'missing_required_input'
        reasonDetail: decision.reason
        missingInputs: decision.missingInputs.map(key =>
          { key, expectedType: 'string', description: 'Required: ' + key }
        )
        contextForTarget:
          summary: 'Processing ' + config.type + ', missing: ' + decision.missingInputs.join(', ')
        priority: 'medium'

      response = this.handoffManager.processHandoff(request)

      // Step 3: Handle response
      if response.status == 'resolved':
        // Merge provided inputs into state and retry
        enrichedState = { ...state, ...response.providedInputs }
        return this.processInternal(config, enrichedState)

      if response.status == 'escalated':
        throw HandoffEscalationError(
          config.type + ' requires human input',
          response
        )

      // Partial or failed - proceed with what we have
      warn('[handoff] Proceeding with partial data for ' + config.type)

    // Step 4: Normal processing
    return this.processInternal(config, state)
```

---

## 5. Handoff Observability

### 5.1 Trace Attributes

```
// Handoff-specific span attributes
handoffSpanAttributes:
  'handoff.trace_id': uuid
  'handoff.source_agent': '[agent_1]'
  'handoff.target_agent': '[agent_3]'
  'handoff.reason': 'missing_required_input'
  'handoff.status': 'resolved'
  'handoff.execution_time_ms': number
  'handoff.inputs_provided': number
  'handoff.inputs_missing': number
```

### 5.2 Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `handoff.total` | Counter | Total handoffs attempted |
| `handoff.success` | Counter | Handoffs resolved successfully |
| `handoff.failed` | Counter | Handoffs that failed |
| `handoff.escalated` | Counter | Handoffs escalated to human |
| `handoff.duration_ms` | Histogram | Time to resolve handoffs |
| `handoff.circular_blocked` | Counter | Circular handoffs prevented |

### 5.3 Alerting Thresholds

| Condition | Severity | Action |
|-----------|----------|--------|
| `handoff.failed` > [N] in 5min | Warning | Investigate agent health |
| `handoff.escalated` > [N] in 1hr | Info | Review escalation patterns |
| `handoff.circular_blocked` > 0 | Warning | Review handoff logic |
| `handoff.duration_ms` p95 > [N]ms | Warning | Optimize handoff handlers |

---

## 6. Error Recovery

### 6.1 Handoff Failure Handling

```
// src/pipeline/handoff/recovery

handleHandoffFailure(request, response, state): RecoveryAction:
  // Strategy 1: Try alternative agent
  if response.status == 'failed' AND request.handoffTo != 'orchestrator':
    return:
      action: 'retry_different_target'
      newTarget: 'orchestrator'
      reason: 'Primary handoff target failed'

  // Strategy 2: Proceed with partial data
  if response.status == 'partial':
    criticalMissing = (response.unresolved OR []).filter(
      key => CRITICAL_INPUTS.includes(key)
    )

    if criticalMissing.length == 0:
      return:
        action: 'proceed_partial'
        reason: 'Non-critical inputs missing, proceeding'

  // Strategy 3: Escalate to human
  return:
    action: 'escalate'
    reason: 'Cannot recover from handoff failure'
    escalationType: 'human_input_required'
```

### 6.2 Recovery Strategies

| Strategy | When to Use | Trade-off |
|----------|-------------|-----------|
| **Retry Different Target** | Primary target failed | May increase latency |
| **Proceed Partial** | Non-critical data missing | Lower quality output |
| **Escalate to Human** | Critical decision needed | Blocks pipeline |
| **Skip and Continue** | Optional enhancement failed | Missing enhancement |
| **Fallback Default** | All else fails | Generic response |

---

## Validation Schema (For AI Generation)

```yaml
# HANDOFF_PROTOCOL validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - pipeline.state_schema: from_pipeline_orchestration

outputs_produced:
  - handoff.request_schema: Validation schema defined
  - handoff.response_schema: Validation schema defined
  - handoff.decision_schema: Validation schema defined
  - handoff.routes[]: Valid handoff paths defined
  - handoff.invalid_routes[]: Anti-patterns documented
  - handoff.limits: Max handoffs, timeouts defined
  - handoff.manager: Implementation with routing logic
  - handoff.observability: Trace attributes and metrics

validation_gate:
  required_sections:
    - "Handoff Protocol Overview"
    - "Handoff Schema"
    - "Handoff Routes"
    - "Handoff Implementation"
    - "Handoff Observability"
    - "Error Recovery"

  minimum_content:
    schemas: 3
    valid_routes: 4
    invalid_routes: 2
    recovery_strategies: 3

cross_references:
  - agents: must_match: arch.agent_topology
  - schema_version: must_be: semver
  - limits: must_align_with: arch.latency_budget

quality_checks:
  - circular_detection: implemented
  - human_escalation: path_defined
  - observability: traces_and_metrics_defined
```

**After generation, verify:**
- [ ] HandoffRequest, HandoffResponse, and HandoffDecision schemas defined
- [ ] Valid handoff routes cover all agent combinations
- [ ] Invalid routes (anti-patterns) documented
- [ ] Circular handoff detection implemented
- [ ] Human escalation path defined
- [ ] Observability integration included
- [ ] Error recovery strategies documented

---

*[PROJECT_NAME] — Handoff Protocol*
*Last Updated: [DATE]*

© [YEAR] [PROJECT_NAME]™
