# [PROJECT NAME]: Multi-Agent Architecture Blueprint

<!-- GENERATION: This is Step 6 of 13. Requires outputs from NORTHSTAR_EXTRACT (and USER_JOURNEYS if --ux). See GENERATION_MANIFEST.md -->

> **Tier**: 2 — Implementation (see [INDEX.md](INDEX.md))
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 6 of 13 — Requires `extract.axioms[]`, `extract.patterns[]`, `northstar.phases[0]`
> **Modularized**: This document references focused modules in `north-star-advisor/docs/architecture/`

---

## Document Structure

This architecture blueprint is modularized for maintainability. The core document contains:
- Executive Summary and Agent Topology
- Technology Stack
- Phase 2 Extensions
- Deployment Checklist
- Appendices

Detailed implementation specifications are in `north-star-advisor/docs/architecture/`:

| Document | Content |
|----------|---------|
| [PIPELINE_ORCHESTRATION.md](architecture/PIPELINE_ORCHESTRATION.md) | Pipeline orchestrator, state schema, execution patterns, self-correction |
| [RESILIENCE_PATTERNS.md](architecture/RESILIENCE_PATTERNS.md) | Circuit breakers, fallback chains, timeout handling, idempotency |
| [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md) | System prompts for all agents with JTBD and few-shot examples |
| [IMPLEMENTATION_SCAFFOLD.md](architecture/IMPLEMENTATION_SCAFFOLD.md) | Directory structure, base classes, agent implementations, API routes |
| [OBSERVABILITY.md](architecture/OBSERVABILITY.md) | Tracing infrastructure, handlers, sanitization, North Star instrumentation |
| [TESTING_STRATEGY.md](architecture/TESTING_STRATEGY.md) | Test categories, configuration, golden datasets, integration tests |

---

## Executive Summary

[PROJECT NAME] is [brief description] requiring a multi-agent architecture to [core value proposition]. This blueprint defines a **[Pattern Name] pattern** using a **[orchestration approach]**, optimized for the **[latency target]** with streaming (TTFT <[X]s).

### Key Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Language** | [Language] | [Why this language fits your needs] |
| Orchestration Pattern | [Pattern] | [Why this pattern] |
| Framework | [Framework] | [Why this framework choice] |
| State Schema | [Validation approach] | [Why this validation approach] |
| LLM Integration | [SDK/Library] | [Why this integration] |
| State Management | [Approach] | [Why this approach] |
| Failure Strategy | [Strategy] | [Why this strategy] |
| Tracing | [Observability solution] | [Why this observability choice] |

---

## 1. Agent Topology

### 1.1 System Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          USER INPUT                                     │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                           ORCHESTRATOR                                  │
│                    ([Orchestration Type])                               │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │  State: { [key fields relevant to your domain] }                  │  │
│  │  Schema: [Validation approach] | Pattern: [Architecture pattern] │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
         │                    │                    │
         │ PARALLEL           │ PARALLEL           │ PARALLEL
         ▼                    ▼                    ▼
┌─────────────┐      ┌─────────────────┐     ┌─────────────┐
│  AGENT 1    │      │    AGENT 2      │     │   AGENT 3   │
│ ([Model])   │      │   ([Model])     │     │  ([Model])  │
│  [tokens]   │      │   [tokens]      │     │  [tokens]   │
│  <[time]    │      │   <[time]       │     │  <[time]    │
└─────────────┘      └─────────────────┘     └─────────────┘
         │                    │                      │
         └────────┬───────────┴──────────────────────┘
                  │
         [SYNCHRONIZATION BARRIER - [X]s max]
                  │
                  ▼
         ┌─────────────────┐
         │   AGENT 4       │
         │   ([Model])     │
         │   [tokens]      │
         │   <[time]       │
         └─────────────────┘
                  │
                  ▼
         ┌─────────────────┐
         │   AGENT 5       │
         │   ([Model])     │  ← Streams to client (TTFT <[X]s)
         │   [tokens]      │
         │   <[time]       │
         └─────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        FINAL OUTPUT                                     │
│         (with any conditional additions based on agent flags)           │
│                                                                         │
│  Total: <[X]s P95 | Perceived (streaming): <[X]s TTFT                  │
│  Fallback: [Model] reserved for [edge case] (condition)                 │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Agent Specifications

| Agent | Model | Token Budget | Timeout | Input | Output | Dependencies |
|-------|-------|--------------|---------|-------|--------|--------------|
| `[agent_1]` | [Model] | [tokens] | [ms] | [input fields] | [output fields] | None |
| `[agent_2]` | [Model] | [tokens] | [ms] | [input fields] | [output fields] | [dependencies] |
| `[agent_3]` | [Model] | [tokens] | [ms] | [input fields] | [output fields] | None |
| `[agent_4]` | [Model] | [tokens] | [ms] | [input fields] | [output fields] | [agent_1, agent_2, agent_3] |
| `[agent_5]` | [Model] | [tokens] | [ms] | Full state | [output fields] | [agent_4] |

> **Latency Budget**: Parallel phase ([X]s) + Sequential phase ([X]s + [X]s) = **<[X]s P95**
>
> **Streaming**: [Which agent] streams to client; TTFT <[X]s provides perceived instant response.
>
> **Fallback**: [Model] reserved for [condition] where [quality priority] is paramount.

### 1.3 Agent Roles

#### [Agent 1 Name]
- **Job**: [What this agent does]
- **Outputs**: `{ [field]: [type], [field]: [type] }`
- **Special considerations**: [Domain-specific notes]
- **Tools required**: [None | tool list]
- **Full prompt**: See [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md#[section])

#### [Agent 2 Name]
- **Job**: [What this agent does]
- **Outputs**: `{ [field]: [type], [field]: [type] }`
- **Behavior**: [Specific behaviors]
- **Tools**: [tool list]
- **Full prompt**: See [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md#[section])

#### [Agent 3 Name]
- **Job**: [What this agent does]
- **Outputs**: `{ [field]: [type], [field]: [type] }`
- **Design**: [Design considerations]
- **Full prompt**: See [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md#[section])

#### [Agent 4 Name]
- **Job**: [What this agent does]
- **Outputs**: `{ [field]: [type], [field]: [type] }`
- **Constraints**: [Constraints and rules]
- **Full prompt**: See [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md#[section])

#### [Agent 5 Name]
- **Job**: [What this agent does]
- **Outputs**: `{ [field]: [type], [field]: [type] }`
- **Capabilities**: [Key capabilities]
- **Receives**: Full orchestrated state including [key fields]
- **Full prompt**: See [AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md#[section])

### 1.4 Cross-Agent Data Flows

```
[agent_1] ──────────────────────────────────────┐
  └─► [output_field_1]                           │
  └─► [output_field_2]                           │
                                                 │
[agent_2] ─────────────────────────────────────┤
  └─► [output_field_1]                           ├──► [agent_4]
  └─► [output_field_2]                           │
                                                 │
[agent_3] ─────────────────────────────────────┤
  └─► [output_field_1]                           │
  └─► [output_field_2] ─────────────────────────┼──► [agent_5]
                                                 │
shared ──────────────────────────────────────────┘
  └─► [shared_field] (e.g., turn_number)
```

| Source Agent | Output Field | Consumer Agent | Purpose |
|--------------|--------------|----------------|---------|
| [agent_1] | [field] | [agent_4] | [purpose] |
| [agent_2] | [field] | [agent_4], [agent_5] | [purpose] |
| [agent_3] | [field] | [agent_5] | [purpose] |
| shared | [field] | [agent_4], [agent_5] | [purpose] |

---

## 2. Pipeline Orchestration

> **Extracted to**: [architecture/PIPELINE_ORCHESTRATION.md](architecture/PIPELINE_ORCHESTRATION.md)

Covers:
- State Schema (with runtime validation)
- Pipeline Orchestrator implementation
- Parallel and sequential execution
- Design patterns (Concurrent, Sequential, Handoff, Maker-Checker)
- Self-correction patterns
- Performance optimization

---

## 3. Resilience Patterns

> **Extracted to**: [architecture/RESILIENCE_PATTERNS.md](architecture/RESILIENCE_PATTERNS.md)

Covers:
- Circuit breaker configuration
- Fallback chains per agent
- Timeout handling with decorators
- Idempotency for state updates

---

## 4. Agent Contracts (System Prompts)

> **Extracted to**: [architecture/AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md)

Covers:
- Full system prompts for all agents
- JTBD framework (job_to_be_done, success_criteria)
- Few-shot examples
- Edge case handling guidance

---

## 5. Implementation Scaffold

> **Extracted to**: [architecture/IMPLEMENTATION_SCAFFOLD.md](architecture/IMPLEMENTATION_SCAFFOLD.md)

Covers:
- Directory structure
- BaseAgent class with resilience patterns
- Agent implementations
- API routes and streaming endpoints

---

## 6. Observability & Tracing

> **Extracted to**: [architecture/OBSERVABILITY.md](architecture/OBSERVABILITY.md)

Covers:
- Trace architecture and event types
- Handler interface (vendor-neutral)
- [Tracing provider] handler
- Cost aggregator
- Trace sanitization (PII protection)
- Request-scoped isolation
- North Star instrumentation

---

## 7. Technology Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| **Language** | [Language] | [Rationale] |
| **LLM Provider** | [Provider] | [Rationale] |
| **LLM Integration** | [SDK/Library] | [Rationale] |
| **Orchestration** | [Approach] | [Rationale] |
| **State Schema** | [Validation library] | [Rationale] |
| **Framework** | [Web framework] | [Rationale] |
| **Database** | [Database] | [Rationale] |
| **ORM** | [ORM] | [Rationale] |
| **Cache** | [Cache solution] | [Rationale] |
| **Validation** | [Validation library] | [Rationale] |
| **Hosting** | [Platform] | [Rationale] |
| **Auth** | [Auth solution] | [Rationale] |
| **Tracing** | [Observability solution] | [Rationale] |
| **Analytics** | [Analytics platform] | [Rationale] |
| **Secrets** | [Secrets management] | [Rationale] |
| **Testing** | [Test framework] | [Rationale] |

### Key Dependencies

```
dependencies:
  [llm-sdk]: ^X.0.0
  [framework]: ^X.0.0
  [orm]: ^X.0.0
  [validation]: ^X.0.0

optionalDependencies:
  [tracing]: ^X.0.0

devDependencies:
  [testing]: ^X.0.0
  [type-checking]: ^X.0.0
```

---

## 8. Phase 2 Extensions

> **Note**: Adapt to your chosen language and framework.

### 8.1 [Extension 1 Name]

```
// src/agents/[extension-name]

ExtensionConfig {
  // Configuration fields
}

class ExtensionAgent {
  constructor(config: ExtensionConfig):
    // Implementation

  async execute(input: InputType): OutputType:
    // Core logic
}
```

### 8.2 [Extension 2 Name]

```
// src/jobs/[extension-name]

DecisionResult {
  shouldAct: boolean
  reason: string
  action: ActionType (optional)
}

function shouldTakeAction(context: Context): DecisionResult:
  // Decision logic

async function extensionHandler(input: Input):
  // Handler implementation
```

### 8.3 [Extension 3 Name]

```
// src/api/[endpoint]/handler

async function handlePost(request):
  // API route implementation
```

---

## 9. Testing Strategy

> **Extracted to**: [architecture/TESTING_STRATEGY.md](architecture/TESTING_STRATEGY.md)

Covers:
- Test categories and coverage targets
- Test configuration
- Golden dataset for critical paths
- Agent unit tests
- Integration tests for full pipeline

---

## 10. Deployment Checklist

### Pre-Launch

- [ ] All circuit breakers tested (open/close/half-open)
- [ ] Fallback chains verified for each agent
- [ ] Critical path golden dataset: 100% recall
- [ ] Latency budget verified: <[X]s P95, TTFT <[X]s with streaming
- [ ] Tracing enabled and verified
- [ ] Error alerting configured
- [ ] Database backup automated
- [ ] Rate limiting in place

### Post-Launch

- [ ] Monitor critical path accuracy weekly
- [ ] Review fallback trigger rates daily
- [ ] A/B test variations (Phase 2)
- [ ] Track North Star metric

---

## Appendix A: [Domain-Specific Resources]

```
// src/tools/[resource-name]

RESOURCE_CONTENT = "
// Resource content that may be appended to responses
"

function appendResource(response: string): string:
  return response + "\n" + RESOURCE_CONTENT

function shouldAppendResource(condition: Condition): boolean:
  return condition.meets([criteria])
```

---

## Appendix B: Decision Log

Quick reference for key decisions. For full context, see [ADR.md](ADR.md).

| Date | Decision | ADR | Rationale |
|------|----------|-----|-----------|
| [Date] | [Decision 1] | ADR-0001 | [Brief rationale] |
| [Date] | [Decision 2] | ADR-0002 | [Brief rationale] |

---

**Document Status**: v1.0 - Initial Blueprint

**Revision History**:
- v1.0 ([Date]): Initial blueprint

**Next Review**: After Phase 1 launch

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDERS]` with your project specifics
2. Draw your agent topology diagram showing parallel vs sequential execution
3. Define each agent's role, inputs, outputs, and dependencies
4. Map cross-agent data flows
5. Choose and document your technology stack with rationale
6. Create the architecture/ module templates
7. Document key decisions in the decision log

**Agent topology patterns to consider:**

| Pattern | When to Use |
|---------|-------------|
| **Single Agent** | Simple tasks with one LLM call |
| **Sequential Pipeline** | Tasks that build on each other |
| **Parallel Pipeline** | Independent tasks that can run concurrently |
| **Supervisor-Worker** | Complex coordination with safety requirements |
| **Hierarchical** | Large systems with multiple levels of delegation |

**Key questions to answer:**

- What is your latency budget?
- Which agents can run in parallel vs must run sequentially?
- What are your fallback strategies per agent?
- How do agents communicate (shared state vs message passing)?
- What happens when an agent fails?

---

## Validation Schema (For AI Generation)

```yaml
# ARCHITECTURE_BLUEPRINT validation gate
inputs_required:
  - brand.name: from_brand_guidelines
  - northstar.metric: from_northstar
  - northstar.phases[0]: from_northstar (MVP scope)
  - extract.axioms[]: from_northstar_extract
  - extract.patterns[]: from_northstar_extract

outputs_produced:
  - arch.agent_topology[]: used_by_agent_prompts
  - arch.state_schema: used_by_all_agents
  - arch.latency_budget: used_by_observability
  - arch.tech_stack: used_by_implementation_scaffold
  - arch.resilience_patterns: used_by_resilience_patterns

validation_gate:
  required_sections:
    - "Executive Summary"
    - "Agent Topology"
    - "Technology Stack"
    - "Deployment Checklist"

  minimum_content:
    agents: 1
    agents[].model: specified
    agents[].timeout: specified
    latency_budget.p95: specified
    tech_stack_decisions: 4

  placeholders_filled:
    - "[PROJECT NAME]"
    - "[Pattern Name]"
    - "[latency target]"

cross_references:
  - agent_topology: must_not_exceed: northstar.phases[0].in_scope
  - resilience_patterns: must_align_with: extract.patterns
  - tech_stack: must_support: latency_budget

quality_checks:
  - each_agent: has_model_and_timeout
  - topology_diagram: shows_data_flow
  - decisions: have_rationale

decision_prompts:
  - "Latency budget: real-time (<1s), conversational (<3s), or batch (>10s)?"
  - "Do you need human escalation paths for safety?"
  - "Parallel or sequential agent execution preference?"
  - "What's your fallback strategy when agents fail?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] Agent topology includes all required agents with models and timeouts
- [ ] Latency budget specified with P95 target
- [ ] Technology stack has at least 4 decisions with rationale
- [ ] Resilience patterns align with NORTHSTAR_EXTRACT patterns
- [ ] Deployment checklist is actionable

---

© [YEAR] [AUTHOR/ORG]

