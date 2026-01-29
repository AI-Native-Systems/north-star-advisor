# [PROJECT NAME]: Resilience Patterns

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Circuit breakers, fallback chains, timeout handling, and idempotency patterns.

---

## 1. Circuit Breaker Pattern

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 1.1 Circuit Breaker Configuration

```
// src/resilience/circuit-breaker

CircuitBreakerConfig:
  failureThreshold      // Failures before opening
  successThreshold      // Successes before closing
  timeout               // Time in OPEN state before HALF_OPEN
  resetTimeout          // Time to reset failure count

CircuitState:
  CLOSED = "CLOSED"       // Normal operation
  OPEN = "OPEN"           // Failing, reject calls
  HALF_OPEN = "HALF_OPEN" // Testing recovery
```

### 1.2 Per-Agent Configuration

| Agent | Failure Threshold | Success Threshold | Timeout | Reset |
|-------|-------------------|-------------------|---------|-------|
| `[agent_1]` | 3 | 2 | 15s | 60s |
| `[agent_2]` | 5 | 3 | 30s | 120s |
| `[agent_3]` | 2 | 1 | 10s | 30s |
| `[agent_4]` | 3 | 2 | 20s | 60s |
| `[agent_5]` | 5 | 3 | 30s | 120s |

### 1.3 Implementation

```
// src/resilience/circuit-breaker

class CircuitBreaker:
  state = CircuitState.CLOSED
  failureCount = 0
  successCount = 0
  lastFailureTime = 0
  config

  constructor(config):
    this.config = config

  execute(fn, fallback):
    if this.state == CircuitState.OPEN:
      if currentTime() - this.lastFailureTime > this.config.timeout:
        this.state = CircuitState.HALF_OPEN
      else:
        return fallback()

    try:
      result = await fn()
      this.onSuccess()
      return result
    catch error:
      this.onFailure()
      return fallback()

  onSuccess():
    this.failureCount = 0
    if this.state == CircuitState.HALF_OPEN:
      this.successCount++
      if this.successCount >= this.config.successThreshold:
        this.state = CircuitState.CLOSED
        this.successCount = 0

  onFailure():
    this.lastFailureTime = currentTime()
    this.failureCount++
    if this.failureCount >= this.config.failureThreshold:
      this.state = CircuitState.OPEN

  getState():
    return this.state
```

---

## 2. Fallback Chains

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 2.1 Fallback Strategy

```
Primary Model -> Simpler Model -> Template -> Cached Response -> Apologetic Fallback
```

### 2.2 Per-Agent Fallback Configuration

| Agent | Level 1 | Level 2 | Level 3 | Level 4 |
|-------|---------|---------|---------|---------|
| `[agent_1]` | [Fast model] | [Template] | [Default] | - |
| `[agent_2]` | [Simpler model] | [Cache] | [Template] | [Apologetic] |
| `[agent_3]` | [Same model retry] | [Template] | [Safe default] | - |
| `[agent_4]` | [Simpler model] | [Template] | [Default] | - |
| `[agent_5]` | [Simpler model] | [Template] | [Apologetic] | - |

### 2.3 Fallback Implementation

```
// src/resilience/fallback-chain

FallbackLevel:
  name
  execute      // function returning result
  timeout

class FallbackChain:
  levels
  finalFallback

  constructor(levels, finalFallback):
    this.levels = levels
    this.finalFallback = finalFallback

  execute():
    for i = 0 to this.levels.length:
      level = this.levels[i]
      try:
        result = await withTimeout(
          level.execute(),
          level.timeout,
          null
        )
        if result != null:
          return { result: result, level: i, levelName: level.name }
      catch:
        continue

    return {
      result: this.finalFallback,
      level: this.levels.length,
      levelName: "final_fallback"
    }
```

### 2.4 Agent Fallback Example

```
// src/agents/[agent-name]

agent1Fallback = new FallbackChain(
  [
    {
      name: "primary",
      execute: () => callPrimaryModel(state),
      timeout: 1000
    },
    {
      name: "fast_model",
      execute: () => callFastModel(state),
      timeout: 500
    },
    {
      name: "template",
      execute: () => resolve(getTemplate(state)),
      timeout: 100
    }
  ],
  DEFAULT_AGENT1_OUTPUT
)
```

---

## 3. Timeout Handling

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 3.1 Timeout Configuration

| Component | Timeout | Rationale |
|-----------|---------|-----------|
| Pipeline Total | [X]s | End-to-end latency budget |
| Parallel Phase | [X]s | Sync barrier for parallel agents |
| [Agent 1] | [X]ms | [Rationale] |
| [Agent 2] | [X]ms | [Rationale] |
| [Agent 3] | [X]ms | [Rationale] |
| [Agent 4] | [X]ms | [Rationale] |
| [Agent 5] | [X]ms | [Rationale] |

### 3.2 Timeout Decorator

```
// src/resilience/timeout

function withTimeoutDecorator(fn, timeoutMs, fallback):
  return async function(...args):
    timeout = createTimeoutPromise(timeoutMs)

    try:
      return await race([fn(...args), timeout])
    catch error:
      if error.message == "Timeout":
        log("[timeout] Function timed out after " + timeoutMs + "ms")
        return fallback
      throw error
```

### 3.3 Graceful Degradation

```
// Pattern: Return partial results on timeout

function executeWithPartialResults(agents, state, totalTimeout):
  startTime = currentTime()
  results = {}

  for agent in agents:
    elapsed = currentTime() - startTime
    remaining = totalTimeout - elapsed

    if remaining <= 0:
      // No time left - use fallbacks for remaining agents
      results[agent.outputKey] = agent.getFallback()
      continue

    try:
      output = await withTimeout(
        agent.execute(state),
        min(remaining, agent.timeout),
        agent.getFallback()
      )
      results[agent.outputKey] = output
    catch:
      results[agent.outputKey] = agent.getFallback()

  return merge(state, results)
```

---

## 4. Idempotency Patterns

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 4.1 Idempotency Key Generation

```
// src/resilience/idempotency

function generateIdempotencyKey(sessionId, turnNumber, agentId):
  input = sessionId + ":" + turnNumber + ":" + agentId
  return sha256(input).substring(0, 32)
```

### 4.2 Idempotent Operation Wrapper

```
// src/resilience/idempotency

IdempotencyStore:
  get(key)                    // returns stored value or null
  set(key, value, ttl)        // stores value with time-to-live

class IdempotentExecutor:
  store
  ttl

  constructor(store, ttl = 3600):
    this.store = store
    this.ttl = ttl

  execute(key, operation):
    // Check for existing result
    cached = await this.store.get(key)
    if cached != null:
      return { result: cached, cached: true }

    // Execute operation
    result = await operation()

    // Store result
    await this.store.set(key, result, this.ttl)

    return { result: result, cached: false }
```

### 4.3 Memory Update Idempotency

```
// src/agents/memory-agent

function updateMemory(sessionId, turnNumber, content):
  idempotencyKey = generateIdempotencyKey(
    sessionId,
    turnNumber,
    AGENT_IDS.MEMORY_AGENT
  )

  await idempotentExecutor.execute(idempotencyKey, async function():
    // Check if this exact update was already applied
    existing = await db.memories.findOne({
      sessionId: sessionId,
      turnNumber: turnNumber,
      contentHash: hashContent(content)
    })

    if existing:
      return existing

    return db.memories.insert({
      sessionId: sessionId,
      turnNumber: turnNumber,
      content: content,
      contentHash: hashContent(content),
      createdAt: currentTime()
    })
  )
```

---

## 5. Retry Strategies

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 5.1 Exponential Backoff

```
// src/resilience/retry

RetryConfig:
  maxRetries
  initialDelay
  maxDelay
  backoffMultiplier

function retryWithBackoff(fn, config):
  lastError = null
  delay = config.initialDelay

  for attempt = 0 to config.maxRetries:
    try:
      return await fn()
    catch error:
      lastError = error

      if attempt < config.maxRetries:
        await sleep(delay)
        delay = min(delay * config.backoffMultiplier, config.maxDelay)

  throw lastError
```

### 5.2 Retry with Jitter

```
// src/resilience/retry

function retryWithJitter(fn, config):
  lastError = null
  delay = config.initialDelay

  for attempt = 0 to config.maxRetries:
    try:
      return await fn()
    catch error:
      lastError = error

      if attempt < config.maxRetries:
        // Add random jitter (0-50% of delay)
        jitter = delay * random() * 0.5
        await sleep(delay + jitter)
        delay = min(delay * config.backoffMultiplier, config.maxDelay)

  throw lastError
```

---

## 6. Health Checks

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 6.1 Agent Health Check

```
// src/resilience/health

HealthStatus:
  agentId
  status           // "healthy" | "degraded" | "unhealthy"
  circuitState
  lastSuccess
  lastFailure
  successRate

function getAgentHealth(agentId, circuitBreaker, metrics):
  successRate = metrics.successCount /
    (metrics.successCount + metrics.failureCount) or 1

  status = "healthy"
  if circuitBreaker.getState() == CircuitState.OPEN:
    status = "unhealthy"
  else if successRate < 0.9:
    status = "degraded"

  return {
    agentId: agentId,
    status: status,
    circuitState: circuitBreaker.getState(),
    lastSuccess: metrics.lastSuccessTime,
    lastFailure: metrics.lastFailureTime,
    successRate: successRate
  }
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Configure circuit breakers for each agent
2. Define fallback chains per agent
3. Set timeout budgets within latency constraints
4. Implement idempotency for stateful operations
5. Add health check endpoints

---

## Validation Schema (For AI Generation)

```yaml
# RESILIENCE_PATTERNS validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.latency_budget: from_architecture_blueprint
  - arch.resilience_patterns: from_architecture_blueprint
  - extract.patterns[]: from_northstar_extract

outputs_produced:
  - resilience.circuit_breakers[]: used_by_implementation
  - resilience.fallback_chains[]: used_by_implementation
  - resilience.timeout_config: used_by_implementation
  - resilience.health_checks: used_by_operations

validation_gate:
  required_sections:
    - "Circuit Breaker Configuration"
    - "Fallback Chains"
    - "Timeout Handling"
    - "Health Checks"

  minimum_content:
    circuit_breakers: arch.agent_topology.length
    fallback_chains: arch.agent_topology.length
    timeout_config: specified

cross_references:
  - fallback_chains: must_align_with: extract.patterns
  - total_timeout: must_fit_within: arch.latency_budget

quality_checks:
  - circuit_breakers: have_thresholds_and_recovery
  - fallback_chains: end_in_safe_default
  - timeouts: leave_buffer_for_response

decision_prompts:
  - "What's the failure threshold before circuit opens? (3-5 failures typical)"
  - "What's the minimum safe fallback for each agent?"
  - "How much timeout buffer for final response generation?"
```

**After generation, verify:**
- [ ] Circuit breaker for each agent in topology
- [ ] Fallback chain for each agent ending in safe default
- [ ] Timeouts sum to less than total latency budget
- [ ] Health check endpoints defined

---

© [YEAR] [AUTHOR/ORG]
