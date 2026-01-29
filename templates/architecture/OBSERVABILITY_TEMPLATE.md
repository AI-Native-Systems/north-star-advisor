# [PROJECT NAME]: Observability

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Tracing infrastructure, handlers, sanitization, and North Star instrumentation.

---

## 1. Trace Architecture

### 1.1 Trace Hierarchy

```
Request Trace (root span)
├── Pipeline Span
│   ├── Parallel Phase Span
│   │   ├── Agent 1 Span
│   │   ├── Agent 2 Span
│   │   └── Agent 3 Span
│   │
│   └── Sequential Phase Span
│       ├── Agent 4 Span
│       └── Agent 5 Span (streaming)
│
└── Response Span
    └── Persistence Span
```

### 1.2 Event Types

| Event | Description | Attributes |
|-------|-------------|------------|
| `pipeline.start` | Pipeline execution begins | session_id, turn_number |
| `pipeline.end` | Pipeline execution completes | duration_ms, success |
| `agent.start` | Agent execution begins | agent_id, model |
| `agent.end` | Agent execution completes | duration_ms, tokens_used, from_fallback |
| `llm.request` | LLM API call | model, prompt_tokens |
| `llm.response` | LLM response received | completion_tokens, latency_ms |
| `fallback.triggered` | Fallback used | agent_id, reason |
| `circuit.opened` | Circuit breaker opened | agent_id, failure_count |

---

## 2. Handler Interface

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 2.1 Trace Handler Protocol

```
// src/observability/handler

TraceEvent {
  type: string
  timestamp: number
  traceId: string
  spanId: string
  parentSpanId: string (optional)
  attributes: Map<string, any>
}

interface TraceHandler {
  name: string
  onEvent(event: TraceEvent): void
  onFlush(): void (optional)
  onShutdown(): void (optional)
}

class TraceManager {
  private handlers: TraceHandler[] = []

  registerHandler(handler: TraceHandler):
    this.handlers.push(handler)

  async emit(event: TraceEvent):
    await parallel(this.handlers.map(h => h.onEvent(event)))

  async flush():
    await parallel(
      this.handlers
        .filter(h => h.onFlush)
        .map(h => h.onFlush())
    )
}
```

---

## 3. OpenTelemetry Handler

> **Note**: OpenTelemetry is an open observability standard. Adapt this implementation to your language's OpenTelemetry SDK.

### 3.1 Implementation

```
// src/observability/handlers/otel

class OpenTelemetryHandler implements TraceHandler {
  name = "opentelemetry"
  private tracer = otel.getTracer("[project-name]")
  private activeSpans = Map<string, Span>()

  onEvent(event: TraceEvent):
    if event.type.endsWith(".start"):
      this.startSpan(event)
    else if event.type.endsWith(".end"):
      this.endSpan(event)
    else:
      this.addEvent(event)

  private startSpan(event: TraceEvent):
    parent = event.parentSpanId
      ? this.activeSpans.get(event.parentSpanId)
      : null

    span = this.tracer.startSpan(
      event.type.replace(".start", ""),
      {
        kind: SPAN_KIND_INTERNAL,
        attributes: this.sanitizeAttributes(event.attributes),
      },
      parentContext: parent
    )

    this.activeSpans.set(event.spanId, span)

  private endSpan(event: TraceEvent):
    span = this.activeSpans.get(event.spanId)
    if span:
      span.setAttributes(this.sanitizeAttributes(event.attributes))

      if event.attributes.error:
        span.setStatus(ERROR, toString(event.attributes.error))

      span.end()
      this.activeSpans.delete(event.spanId)

  private addEvent(event: TraceEvent):
    span = this.activeSpans.get(event.spanId)
    if span:
      span.addEvent(event.type, this.sanitizeAttributes(event.attributes))

  private sanitizeAttributes(attrs: Map<string, any>): Map<string, primitive>:
    // Convert to OpenTelemetry-compatible types (string, number, boolean)
    result = {}
    for [key, value] in attrs:
      if isPrimitive(value):
        result[key] = value
      else:
        result[key] = toJson(value)
    return result
}
```

### 3.2 Bootstrap

```
// src/observability/otel-bootstrap

// Initialize OpenTelemetry SDK with your exporter
sdk = createOtelSDK({
  serviceName: env.OTEL_SERVICE_NAME OR "[project-name]",
  traceExporter: createOtlpExporter({
    endpoint: env.OTEL_EXPORTER_OTLP_ENDPOINT,
  }),
})

function initTelemetry():
  sdk.start()
  log("[otel] Telemetry initialized")

async function shutdownTelemetry():
  await sdk.shutdown()
  log("[otel] Telemetry shutdown")
```

---

## 4. Cost Aggregation

### 4.1 Token Cost Tracking

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// src/observability/costs

TokenUsage:
  promptTokens (number)
  completionTokens (number)
  model (text)

CostConfig:
  map of model (text) to:
    promptCostPer1k (number)
    completionCostPer1k (number)

COST_CONFIG = {
  "[fast-model]": {
    promptCostPer1k: 0.00025,
    completionCostPer1k: 0.00125,
  },
  "[standard-model]": {
    promptCostPer1k: 0.003,
    completionCostPer1k: 0.015,
  },
  "[premium-model]": {
    promptCostPer1k: 0.015,
    completionCostPer1k: 0.075,
  },
}

function calculateCost(usage):
  config = COST_CONFIG[usage.model]
  if not config:
    return 0

  return (
    (usage.promptTokens / 1000) * config.promptCostPer1k +
    (usage.completionTokens / 1000) * config.completionCostPer1k
  )

class CostAggregator:
  private requestCosts = map<text, number>()

  addUsage(requestId, usage):
    cost = calculateCost(usage)
    current = this.requestCosts.get(requestId) or 0
    this.requestCosts.set(requestId, current + cost)

  getRequestCost(requestId):
    return this.requestCosts.get(requestId) or 0

  clearRequest(requestId):
    this.requestCosts.delete(requestId)
```

---

## 5. PII Sanitization

### 5.1 Sanitization Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| `strict` | Remove all PII, hash user content | Production |
| `moderate` | Hash PII, preserve structure | Staging |
| `permissive` | Log everything | Development |

### 5.2 Implementation

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// src/observability/sanitization

SanitizationMode:
  "strict" | "moderate" | "permissive"

SanitizationConfig:
  mode
  piiFields
  hashFields

DEFAULT_CONFIG = {
  mode: env.SANITIZATION_MODE OR "strict",
  piiFields: ["user_message", "response", "email", "name", "phone"],
  hashFields: ["session_id", "user_id"],
}

function sanitize(data, config = DEFAULT_CONFIG):
  if config.mode == "permissive":
    return data

  result = {}

  for [key, value] in data.entries():
    if config.piiFields.includes(key):
      if config.mode == "strict":
        result[key] = "[REDACTED]"
      else:
        result[key] = hashValue(toString(value))
    else if config.hashFields.includes(key):
      result[key] = hashValue(toString(value))
    else if isObject(value) AND value != null:
      result[key] = sanitize(value, config)
    else:
      result[key] = value

  return result

function hashValue(value):
  return sha256(value).substring(0, 16)
```

---

## 6. Request-Scoped Isolation

### 6.1 Context Management

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// src/observability/context

// Use your language's async context propagation mechanism
// (e.g., AsyncLocalStorage in Node.js, Context in Go, ThreadLocal in Java)

RequestContext:
  requestId
  traceId
  sessionId (optional)
  userId (optional)
  startTime

requestStorage = createAsyncLocalStorage()

function createRequestContext(sessionId, userId):
  return {
    requestId: generateUUIDv7(),
    traceId: generateUUIDv7(),
    sessionId: sessionId,
    userId: userId,
    startTime: currentTimeMillis(),
  }

function runWithContext(context, fn):
  return requestStorage.run(context, fn)

function getContext():
  return requestStorage.getStore()

function getRequestId():
  context = getContext()
  if context:
    return context.requestId
  return "unknown"
```

---

## 7. North Star Instrumentation

### 7.1 Metric Collection

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// src/observability/north-star

NorthStarEvent:
  type: "session_start" | "session_end" | "outcome_signal"
  sessionId
  userId
  timestamp
  data: Map<string, any>

class NorthStarTracker:
  private events = []

  trackSessionStart(sessionId, userId):
    this.events.push({
      type: "session_start",
      sessionId,
      userId,
      timestamp: Date.now(),
      data: {},
    })

  trackSessionEnd(sessionId, userId, outcomes):
    this.events.push({
      type: "session_end",
      sessionId,
      userId,
      timestamp: Date.now(),
      data: outcomes,
    })

  trackOutcomeSignal(sessionId, userId, signal, value):
    this.events.push({
      type: "outcome_signal",
      sessionId,
      userId,
      timestamp: Date.now(),
      data: { signal, value },
    })

  async flush():
    if this.events.length == 0:
      return

    // Send to analytics backend
    await this.sendToAnalytics(this.events)
    this.events = []

  private async sendToAnalytics(events):
    // Implementation depends on analytics provider
```

### 7.2 North Star Metric Calculation

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// src/observability/metrics

NorthStarMetrics:
  // Replace with your actual North Star metric
  primaryMetric
  inputMetrics:
    [key]: value
  healthMetrics:
    errorRate
    latencyP95

function calculateNorthStarMetrics(timeRange):
  // timeRange has start and end properties
  // Implement based on your North Star definition
  // This is a placeholder structure
  return {
    primaryMetric: 0,
    inputMetrics: {},
    healthMetrics: {
      errorRate: 0,
      latencyP95: 0,
    },
  }
```

---

## 8. Dashboards & Alerts

### 8.1 Key Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `pipeline.latency.p95` | 95th percentile latency | > [target]ms |
| `pipeline.error_rate` | Error rate | > 1% |
| `agent.fallback_rate` | Fallback usage rate | > 5% |
| `circuit.open_count` | Open circuit breakers | > 0 |
| `llm.cost.daily` | Daily LLM costs | > $[threshold] |

### 8.2 Dashboard Panels

```yaml
# Example dashboard configuration

panels:
  - name: Pipeline Health
    metrics:
      - pipeline.success_rate
      - pipeline.latency.p50
      - pipeline.latency.p95
      - pipeline.latency.p99

  - name: Agent Performance
    metrics:
      - agent.execution_time (by agent_id)
      - agent.fallback_rate (by agent_id)
      - agent.error_rate (by agent_id)

  - name: LLM Usage
    metrics:
      - llm.tokens.prompt (by model)
      - llm.tokens.completion (by model)
      - llm.cost.total (by model)

  - name: Circuit Breakers
    metrics:
      - circuit.state (by agent_id)
      - circuit.failure_count (by agent_id)

  - name: North Star
    metrics:
      - north_star.primary_metric
      - north_star.input_metrics.*
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Define your trace hierarchy
2. Implement handlers for your observability backend
3. Configure PII sanitization for your domain
4. Set up North Star metric tracking
5. Create dashboards and alerts

---

## Validation Schema (For AI Generation)

```yaml
# OBSERVABILITY validation gate
inputs_required:
  - northstar.metric: from_northstar
  - northstar.input_metrics[]: from_northstar
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.latency_budget: from_architecture_blueprint

outputs_produced:
  - observability.trace_schema: used_by_implementation
  - observability.metrics[]: used_by_post_deployment
  - observability.dashboards: used_by_operations
  - observability.alerts: used_by_operations

validation_gate:
  required_sections:
    - "Trace Architecture"
    - "Tracing Handlers"
    - "PII Sanitization"
    - "North Star Instrumentation"

  minimum_content:
    trace_fields: 5
    pii_rules: 2
    dashboard_panels: 3

cross_references:
  - metrics: must_include: northstar.metric
  - metrics: must_include: northstar.input_metrics
  - latency_tracking: must_align_with: arch.latency_budget

quality_checks:
  - traces: capture_agent_boundaries
  - pii_sanitization: covers_user_input
  - dashboards: show_north_star_prominently

decision_prompts:
  - "What observability backend? (OpenTelemetry, Datadog, custom)"
  - "What PII fields must be sanitized?"
  - "What alert thresholds for latency and errors?"
```

**After generation, verify:**
- [ ] Trace hierarchy covers all agents
- [ ] North Star metric is prominently tracked
- [ ] PII sanitization rules are comprehensive
- [ ] Dashboards show North Star and input metrics

---

© [YEAR] [AUTHOR/ORG]

