# [PROJECT NAME]: Implementation Scaffold

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Directory structure, base classes, agent implementations, and API routes.

---

## 1. Directory Structure

> **Note**: Adapt this structure to your chosen framework. The key principle is separation of concerns.

```
/
├── src/                          # Source code (or app/, lib/, etc.)
│   ├── api/                      # API routes/handlers
│   │   ├── [endpoint]/           # Main endpoint
│   │   └── health/               # Health check
│   │
│   ├── agents/                   # Agent implementations
│   │   ├── base.[ext]            # BaseAgent class
│   │   ├── [agent-1].[ext]
│   │   ├── [agent-2].[ext]
│   │   └── index.[ext]           # Agent exports
│   │
│   ├── ai/                       # LLM configuration
│   │   ├── models.[ext]          # Model definitions
│   │   ├── prompts.[ext]         # Prompt templates
│   │   └── client.[ext]          # LLM client
│   │
│   ├── pipeline/                 # Orchestration
│   │   ├── orchestrator.[ext]    # Pipeline orchestrator
│   │   ├── state.[ext]           # State schema
│   │   └── index.[ext]
│   │
│   ├── resilience/               # Resilience patterns
│   │   ├── circuit-breaker.[ext]
│   │   ├── fallback.[ext]
│   │   ├── retry.[ext]
│   │   └── timeout.[ext]
│   │
│   ├── db/                       # Database
│   │   ├── client.[ext]          # DB client
│   │   ├── queries.[ext]         # Query functions
│   │   └── schema.[ext]          # DB schema
│   │
│   ├── observability/            # Tracing & metrics
│   │   ├── tracer.[ext]
│   │   ├── metrics.[ext]
│   │   └── handlers/
│   │
│   └── utils/                    # Utilities
│       ├── constants.[ext]       # All magic values
│       ├── errors.[ext]          # Error types
│       └── validation.[ext]
│
├── types/                        # Type definitions (if applicable)
│   ├── agents.[ext]
│   ├── pipeline.[ext]
│   └── api.[ext]
│
├── tests/                        # Test files
│   ├── agents/
│   ├── pipeline/
│   └── integration/
│
└── north-star-advisor/           # Strategic documentation
    └── docs/
        ├── NORTHSTAR.md
        ├── ARCHITECTURE_BLUEPRINT.md
        └── architecture/
```

---

## 2. BaseAgent Class

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

### 2.1 Interface Definition

```
// src/agents/base

// Agent configuration structure
AgentConfig {
  agentId: string
  model: string
  tokenBudget: number
  timeoutMs: number
  circuitBreaker: CircuitBreakerConfig (optional)
}

// Result returned by agent execution
AgentResult<T> {
  output: T
  metadata: {
    agentId: string
    executionTimeMs: number
    tokensUsed: number
    fromFallback: boolean
  }
}

// Base class all agents extend
abstract class BaseAgent<TOutput> {
  protected config: AgentConfig
  protected circuitBreaker: CircuitBreaker
  protected fallback: function() -> TOutput

  constructor(config: AgentConfig, fallback: function() -> TOutput):
    this.config = config
    this.fallback = fallback
    this.circuitBreaker = new CircuitBreaker(
      config.circuitBreaker OR DEFAULT_CIRCUIT_BREAKER
    )

  get id(): string:
    return this.config.agentId

  async run(state: PipelineState): AgentResult<TOutput>:
    start = currentTimestamp()

    result = await this.circuitBreaker.execute(
      () => withTimeout(
        this.execute(state),
        this.config.timeoutMs,
        this.fallback()
      ),
      this.fallback
    )

    return {
      output: result,
      metadata: {
        agentId: this.config.agentId,
        executionTimeMs: currentTimestamp() - start,
        tokensUsed: this.getTokensUsed(),
        fromFallback: result === this.fallback(),
      },
    }

  getFallback(): TOutput:
    return this.fallback()

  // Subclasses must implement these
  protected abstract execute(state: PipelineState): TOutput
  protected abstract getTokensUsed(): number
}
```

### 2.2 Default Constants

```
// src/utils/constants

AGENT_IDS = {
  AGENT_1: "agent_1",
  AGENT_2: "agent_2",
  AGENT_3: "agent_3",
  AGENT_4: "agent_4",
  AGENT_5: "agent_5",
}

TOKEN_BUDGETS = {
  agent_1: 500,
  agent_2: 600,
  agent_3: 450,
  agent_4: 550,
  agent_5: 1000,
}

TIMEOUTS = {
  agent_1: 500,
  agent_2: 500,
  agent_3: 300,
  agent_4: 500,
  agent_5: 1500,
  PIPELINE_TOTAL: 3000,
  PARALLEL_PHASE: 600,
}

MODELS = {
  FAST: "[fast-model-id]",
  STANDARD: "[standard-model-id]",
  PREMIUM: "[premium-model-id]",
}
```

---

## 3. Agent Implementations

### 3.1 Example Agent Implementation

> **Note**: Adapt to your chosen language and LLM SDK. This is pseudocode showing the pattern.

```
// src/agents/[agent-1]

// Output schema - define expected structure
Agent1Output {
  field1: string
  field2: number
  confidence: number (0-1)
}

// Default fallback when agent fails
DEFAULT_OUTPUT = {
  field1: "default",
  field2: 0,
  confidence: 0,
}

// Agent configuration from constants
config = {
  agentId: AGENT_IDS.AGENT_1,
  model: MODELS.FAST,
  tokenBudget: TOKEN_BUDGETS[AGENT_IDS.AGENT_1],
  timeoutMs: TIMEOUTS[AGENT_IDS.AGENT_1],
}

class Agent1 extends BaseAgent<Agent1Output> {

  constructor() {
    super(config, fallback: () => DEFAULT_OUTPUT)
  }

  async execute(state: PipelineState): Agent1Output {
    // Call LLM with structured output
    result = await llmClient.generateStructured({
      model: this.config.model,
      schema: Agent1OutputSchema,
      systemPrompt: AGENT_1_PROMPT,
      userPrompt: this.buildPrompt(state),
      maxTokens: this.config.tokenBudget,
    })

    this.tokensUsed = result.usage.totalTokens
    return result.output
  }

  private buildPrompt(state: PipelineState): string {
    return "Input: " + state.userMessage
  }
}
```

### 3.2 Agent with Streaming

```
// src/agents/[response-generator]

class ResponseGenerator extends BaseAgent<string> {

  constructor() {
    super(
      config: {
        agentId: AGENT_IDS.RESPONSE_GENERATOR,
        model: MODELS.STANDARD,
        tokenBudget: TOKEN_BUDGETS[AGENT_IDS.RESPONSE_GENERATOR],
        timeoutMs: TIMEOUTS[AGENT_IDS.RESPONSE_GENERATOR],
      },
      fallback: () => "I'm sorry, I couldn't generate a response. Please try again."
    )
  }

  // Streaming method for real-time response
  async stream(state: PipelineState): StreamResult {
    result = llmClient.streamText({
      model: this.config.model,
      systemPrompt: this.buildSystemPrompt(state),
      userPrompt: state.userMessage,
      maxTokens: this.config.tokenBudget,
      onFinish: (usage) => {
        this.tokensUsed = usage.totalTokens
      },
    })

    return result
  }

  async execute(state: PipelineState): string {
    result = await this.stream(state)
    return result.text
  }

  private buildSystemPrompt(state: PipelineState): string {
    return RESPONSE_PROMPT
      .replace("{{agent1Output}}", serialize(state.agent1Output))
      .replace("{{agent2Output}}", serialize(state.agent2Output))
  }
}
```

---

## 4. API Routes

> **Note**: Adapt to your chosen web framework. This is pseudocode showing the pattern.

### 4.1 Main Endpoint

```
// src/api/[endpoint]/handler

// Request schema - validate incoming requests
RequestSchema {
  sessionId: uuid (required)
  userId: uuid (required)
  message: string (1-10000 chars)
}

async function handlePost(request):
  traceCtx = createTraceContext()

  try:
    body = parseJson(request.body)
    validated = validate(body, RequestSchema)

    if validation fails:
      return jsonResponse({
        success: false,
        error: "Invalid request",
        details: validationErrors
      }, status: 400)

    result = await runPipeline({
      sessionId: validated.sessionId,
      userId: validated.userId,
      userMessage: validated.message,
    })

    return jsonResponse({
      success: true,
      response: result.finalResponse,
      metadata: {
        turnNumber: result.turnNumber,
        executionTimeMs: result.executionTime,
      },
    })

  catch error:
    log("[api] Pipeline error:", error)
    return jsonResponse({
      success: false,
      error: "Internal server error"
    }, status: 500)
```

### 4.2 Streaming Endpoint

```
// src/api/[endpoint]/stream/handler

async function handlePost(request):
  body = parseJson(request.body)
  { sessionId, userId, message } = body

  // Run non-streaming agents first
  preState = await runPreResponsePipeline({
    sessionId,
    userId,
    userMessage: message,
  })

  // Stream the response
  responseGenerator = getResponseGenerator()
  streamResult = await responseGenerator.stream(preState)

  return toStreamResponse(streamResult)
```

### 4.3 Health Check

```
// src/api/health/handler

async function handleGet():
  agents = await parallel([
    getAgentHealth("agent_1"),
    getAgentHealth("agent_2"),
    // ... other agents
  ])

  db = await getDbHealth()

  allHealthy = agents.every(a => a.status === "healthy") AND db.healthy

  return jsonResponse(
    {
      status: allHealthy ? "healthy" : "degraded",
      agents: agents,
      database: db,
      timestamp: currentTimestamp(),
    },
    status: allHealthy ? 200 : 503
  )
```

---

## 5. Configuration

### 5.1 Environment Variables

```bash
# .env.local

# LLM Provider
LLM_API_KEY=...
LLM_PROVIDER=[provider-name]

# Database
DATABASE_URL=...

# Redis (optional)
REDIS_URL=...

# Observability
OTEL_EXPORTER_OTLP_ENDPOINT=https://...
OTEL_SERVICE_NAME=[project-name]

# Feature Flags
ENABLE_STREAMING=true
DEBUG_MODE=false
```

### 5.2 Configuration Validation

```
// src/config

// Configuration schema - validate at startup
ConfigSchema {
  llmApiKey: string (required, min 1 char)
  llmProvider: string (required)
  databaseUrl: url (required)
  redisUrl: url (optional)
  otelEndpoint: url (optional)
  serviceName: string (default: "[project-name]")
  enableStreaming: boolean (default: true)
  debugMode: boolean (default: false)
}

// Load and validate configuration from environment
config = validateConfig({
  llmApiKey: env.LLM_API_KEY,
  llmProvider: env.LLM_PROVIDER,
  databaseUrl: env.DATABASE_URL,
  redisUrl: env.REDIS_URL,
  otelEndpoint: env.OTEL_EXPORTER_OTLP_ENDPOINT,
  serviceName: env.OTEL_SERVICE_NAME,
  enableStreaming: env.ENABLE_STREAMING === "true",
  debugMode: env.DEBUG_MODE === "true",
})

// Fail fast if configuration is invalid
if config.errors:
  throw ConfigurationError(config.errors)
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Adapt directory structure to your framework
2. Implement BaseAgent with your resilience patterns
3. Create agent implementations for each agent
4. Set up API routes for your endpoints
5. Configure environment variables

---

## Validation Schema (For AI Generation)

```yaml
# IMPLEMENTATION_SCAFFOLD validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.tech_stack: from_architecture_blueprint
  - pipeline.state_schema: from_pipeline_orchestration
  - resilience.circuit_breakers[]: from_resilience_patterns

outputs_produced:
  - scaffold.directory_structure: used_by_developers
  - scaffold.base_agent: used_by_agent_implementations
  - scaffold.agent_implementations[]: used_by_developers
  - scaffold.api_routes: used_by_developers
  - scaffold.config: used_by_deployment

validation_gate:
  required_sections:
    - "Directory Structure"
    - "BaseAgent Class"
    - "Agent Implementations"
    - "API Routes"
    - "Configuration"

  minimum_content:
    directories: 5
    agent_implementations: arch.agent_topology.length
    api_routes: 1
    env_vars: 3

cross_references:
  - agent_implementations: must_match: arch.agent_topology
  - base_agent: must_include: resilience.circuit_breakers
  - config: must_include: arch.tech_stack.requirements

quality_checks:
  - directory_structure: follows_convention
  - base_agent: handles_errors_and_timeouts
  - config: has_validation

decision_prompts:
  - "What framework conventions to follow? (Next.js, FastAPI, etc.)"
  - "How to organize agent code? (one file per agent, grouped)"
  - "What environment variables are required?"
```

**After generation, verify:**
- [ ] Directory structure matches framework conventions
- [ ] BaseAgent includes circuit breaker and timeout handling
- [ ] One implementation per agent in topology
- [ ] API routes are defined
- [ ] Environment variables are documented

---

© [YEAR] [AUTHOR/ORG]

