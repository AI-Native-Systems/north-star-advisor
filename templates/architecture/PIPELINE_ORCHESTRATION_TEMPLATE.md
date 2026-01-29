# [PROJECT NAME]: Pipeline Orchestration

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Pipeline orchestrator implementation, state schema, and execution patterns.

---

## 1. State Schema

> **Note**: Adapt to your chosen validation library. This is pseudocode showing the pattern.

### 1.1 Core State Definition

```
// src/pipeline/state

// Define state schema with runtime validation
PipelineStateSchema {
  // Session identification
  sessionId: uuid (required)
  userId: uuid (required)
  turnNumber: integer >= 0

  // Input
  userMessage: string (min 1 char)

  // Agent outputs - populated by pipeline
  [agent1Output]: {
    // Define agent 1 output schema
  } (optional)

  [agent2Output]: {
    // Define agent 2 output schema
  } (optional)

  // ... additional agent outputs

  // Final output
  finalResponse: string (optional)

  // Metadata
  startedAt: timestamp
  completedAt: timestamp (optional)
  error: string (optional)
}

// Type derived from schema
type PipelineState = infer(PipelineStateSchema)
```

### 1.2 Agent-Specific Schemas

```
// src/pipeline/schemas/[agent-name]

Agent1OutputSchema {
  field1: string
  field2: number
  confidence: number (0-1)
}

type Agent1Output = infer(Agent1OutputSchema)
```

---

## 2. Pipeline Orchestrator

### 2.1 Orchestrator Interface

```
// src/pipeline/orchestrator

PipelineConfig {
  timeout: number
  parallelAgents: string[]
  sequentialAgents: string[]
  fallbackEnabled: boolean
}

PipelineResult {
  state: PipelineState
  success: boolean
  executionTime: number
  agentResults: Map<string, AgentResult>
}

AgentResult {
  agentId: string
  success: boolean
  output: any
  executionTime: number
  error: string (optional)
}
```

### 2.2 Orchestrator Implementation

```
// src/pipeline/orchestrator

class PipelineOrchestrator {
  private agents: Map<string, BaseAgent>
  private config: PipelineConfig

  constructor(agents: BaseAgent[], config: PipelineConfig):
    this.agents = mapById(agents)
    this.config = config

  async execute(initialState: Partial<PipelineState>): PipelineResult:
    state = {
      ...initialState,
      startedAt: currentTimestamp(),
    }

    // Phase 1: Run parallel agents
    parallelResults = await this.runParallel(
      this.config.parallelAgents,
      state
    )

    // Merge parallel results into state
    for result in parallelResults:
      state = merge(state, result.output)

    // Phase 2: Run sequential agents
    for agentId in this.config.sequentialAgents:
      result = await this.runAgent(agentId, state)
      state = merge(state, result.output)

    state.completedAt = currentTimestamp()

    return {
      state,
      success: NOT state.error,
      executionTime: state.completedAt - state.startedAt,
      agentResults: this.collectResults(),
    }

  private async runParallel(agentIds: string[], state: PipelineState): AgentResult[]:
    promises = agentIds.map(id => this.runAgent(id, state))
    return awaitAll(promises)

  private async runAgent(agentId: string, state: PipelineState): AgentResult:
    agent = this.agents.get(agentId)
    if NOT agent:
      throw Error("Unknown agent: " + agentId)

    start = currentTimestamp()
    try:
      output = await agent.execute(state)
      return {
        agentId,
        success: true,
        output,
        executionTime: currentTimestamp() - start,
      }
    catch error:
      return {
        agentId,
        success: false,
        output: agent.getFallback(),
        executionTime: currentTimestamp() - start,
        error: toString(error),
      }
}
```

---

## 3. Execution Patterns

### 3.1 Parallel Execution

```
// Pattern: Run independent agents concurrently

async function runParallelModules(agents: BaseAgent[], state: PipelineState): PipelineState:
  results = await awaitAllSettled(
    agents.map(agent => agent.execute(state))
  )

  // Merge successful results, use fallbacks for failures
  return results.reduce((acc, result, index) => {
    if result.status === "fulfilled":
      return merge(acc, result.value)
    return merge(acc, agents[index].getFallback())
  }, state)
```

### 3.2 Sequential Execution

```
// Pattern: Run agents in order, each receiving previous output

async function runSequentialPipeline(agents: BaseAgent[], initialState: PipelineState): PipelineState:
  state = initialState

  for agent in agents:
    output = await agent.execute(state)
    state = merge(state, output)

  return state
```

### 3.3 Conditional Execution

```
// Pattern: Choose agent based on state

async function runConditionalPipeline(
  state: PipelineState,
  router: function(state) -> string,
  agents: Map<string, BaseAgent>
): PipelineState:
  agentId = router(state)
  agent = agents.get(agentId)

  if NOT agent:
    throw Error("Unknown agent: " + agentId)

  output = await agent.execute(state)
  return merge(state, output)
```

---

## 4. Self-Correction Patterns

### 4.1 Quality-Gated Execution

```
QualityGate {
  evaluate(output): { pass: boolean, feedback: string (optional) }
  maxRetries: number
}

async function runWithQualityGate(
  agent: BaseAgent,
  state: PipelineState,
  gate: QualityGate
): AgentResult:
  attempts = 0
  lastOutput = null
  lastFeedback = null

  while attempts < gate.maxRetries:
    attempts++

    stateWithFeedback = lastFeedback
      ? merge(state, { retryFeedback: lastFeedback })
      : state

    lastOutput = await agent.execute(stateWithFeedback)
    { pass, feedback } = gate.evaluate(lastOutput)

    if pass:
      return { agentId: agent.id, success: true, output: lastOutput, attempts }

    lastFeedback = feedback

  // Max retries exceeded - use fallback
  return {
    agentId: agent.id,
    success: false,
    output: agent.getFallback(),
    attempts,
    error: "Quality gate failed after max retries",
  }
```

### 4.2 Confidence-Based Retry

```
ConfidenceOutput {
  result: any
  confidence: number
}

async function runWithConfidenceThreshold(
  agent: BaseAgent,
  state: PipelineState,
  threshold: number,
  maxRetries: number = 2
): ConfidenceOutput:
  bestResult = { result: null, confidence: 0 }

  for i = 0 to maxRetries:
    output = await agent.execute(state)

    if output.confidence >= threshold:
      return output

    if output.confidence > bestResult.confidence:
      bestResult = output

  return bestResult
```

---

## 5. Performance Optimization

### 5.1 Timeout Management

```
async function withTimeout<T>(
  promise: Promise<T>,
  timeoutMs: number,
  fallback: T
): T:
  timeout = createTimeoutPromise(timeoutMs)

  try:
    return await race([promise, timeout])
  catch:
    return fallback
```

### 5.2 Early Exit on Critical Failure

```
async function runWithEarlyExit(
  agents: BaseAgent[],
  state: PipelineState,
  criticalAgentIds: Set<string>
): PipelineState:
  for agent in agents:
    try:
      output = await agent.execute(state)
      state = merge(state, output)
    catch error:
      if criticalAgentIds.has(agent.id):
        // Critical agent failed - abort pipeline
        throw Error("Critical agent " + agent.id + " failed: " + error)
      // Non-critical - use fallback and continue
      state = merge(state, agent.getFallback())

  return state
```

---

## 6. Integration Example

```
// src/pipeline/index

async function runConversationPipeline(
  sessionId: string,
  userId: string,
  message: string
): PipelineResult:
  orchestrator = new PipelineOrchestrator(
    [
      new Agent1(),
      new Agent2(),
      new Agent3(),
      new Agent4(),
      new Agent5(),
    ],
    {
      timeout: TIMEOUTS.PIPELINE_TOTAL,
      parallelAgents: [
        AGENT_IDS.AGENT_1,
        AGENT_IDS.AGENT_2,
        AGENT_IDS.AGENT_3,
      ],
      sequentialAgents: [
        AGENT_IDS.AGENT_4,
        AGENT_IDS.AGENT_5,
      ],
      fallbackEnabled: true,
    }
  )

  return orchestrator.execute({
    sessionId,
    userId,
    userMessage: message,
    turnNumber: await getTurnNumber(sessionId),
  })
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Define your state schema with all agent outputs
2. Implement the orchestrator for your execution pattern
3. Choose appropriate execution patterns (parallel, sequential, conditional)
4. Add self-correction for quality-critical agents
5. Configure timeouts and fallbacks

---

## Validation Schema (For AI Generation)

```yaml
# PIPELINE_ORCHESTRATION validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.latency_budget: from_architecture_blueprint
  - extract.patterns[]: from_northstar_extract

outputs_produced:
  - pipeline.state_schema: used_by_all_agents
  - pipeline.orchestrator: used_by_implementation
  - pipeline.execution_patterns: used_by_implementation

validation_gate:
  required_sections:
    - "State Schema"
    - "Pipeline Orchestrator"
    - "Execution Patterns"

  minimum_content:
    state_fields: arch.agent_topology.length + 3 (input fields)
    execution_patterns: 1

cross_references:
  - state_schema: must_include_output_for: arch.agent_topology
  - timeout_budget: must_fit_within: arch.latency_budget

quality_checks:
  - state_schema: has_validation
  - orchestrator: handles_errors
  - patterns: show_parallel_vs_sequential

decision_prompts:
  - "What validation library will you use for schema validation?"
  - "Which agents can run in parallel?"
  - "What triggers self-correction?"
```

**After generation, verify:**
- [ ] State schema includes all agent outputs
- [ ] Orchestrator handles all agents in topology
- [ ] Timeout budget fits within latency constraints
- [ ] Error handling is explicit

---

© [YEAR] [AUTHOR/ORG]

