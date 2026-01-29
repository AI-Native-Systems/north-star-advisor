# [PROJECT NAME]: Testing Strategy

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Test categories, configuration, golden datasets, and integration tests.

---

## 1. Test Categories

### 1.1 Test Pyramid

```
                    ┌─────────┐
                    │   E2E   │  ← Few, slow, high confidence
                   ─┴─────────┴─
                 ┌───────────────┐
                 │  Integration  │  ← Pipeline tests
                ─┴───────────────┴─
              ┌───────────────────────┐
              │      Agent Unit       │  ← Per-agent tests
             ─┴───────────────────────┴─
           ┌───────────────────────────────┐
           │       Utility Unit            │  ← Helper functions
          ─┴───────────────────────────────┴─
```

### 1.2 Coverage Targets

| Category | Target | Focus |
|----------|--------|-------|
| Utility Unit | 90% | Pure functions, helpers |
| Agent Unit | 80% | Agent logic, fallbacks |
| Integration | 70% | Pipeline flows, data paths |
| E2E | Critical paths | User journeys |

---

## 2. Test Configuration

### 2.1 Test Framework Configuration

> **Note**: Adapt to your chosen test framework (Vitest, Jest, pytest, etc.).

```
// tests/config

TestConfig:
  globals: true
  environment: "node"
  include: ["tests/**/*.test.*"]
  exclude: ["tests/e2e/**"]
  coverage:
    reporter: ["text", "json", "html"]
    exclude: ["node_modules", "tests", "*.config.*"]
  setupFiles: ["./tests/setup"]
  testTimeout: 10000

  pathAliases:
    "@": "./src"
```

### 2.2 Test Setup

> **Note**: Adapt to your chosen test framework and LLM provider.

```
// tests/setup

// Mock environment variables
env.LLM_API_KEY = "test-key"
env.DATABASE_URL = "test-connection-string"

// Mock external services - adapt to your LLM provider
mock("llm-provider", () => ({
  llmClient: mockFn(() => ({
    // Mock implementation
  })),
}))

beforeAll(async () => {
  // Global setup
})

afterAll(async () => {
  // Global cleanup
})
```

---

## 3. Golden Datasets

### 3.1 Golden Dataset Structure

> **Note**: Adapt to your chosen language and test framework.

```
// tests/fixtures/golden-dataset

GoldenTestCase:
  id: text
  category: text
  description: text
  input:
    userMessage: text
    context: map (optional)
  expected:
    agentOutputs:
      [agent_id]: partial output
    constraints:
      mustContain: list of text (optional)
      mustNotContain: list of text (optional)
      mustMatch: pattern (optional)
  priority: "critical" | "high" | "medium" | "low"

GOLDEN_DATASET = [
  {
    id: "golden-001",
    category: "[category]",
    description: "[What this tests]",
    input: {
      userMessage: "[Test input]",
    },
    expected: {
      agentOutputs: {
        "[agent_id]": {
          field: "expected_value",
        },
      },
    },
    priority: "critical",
  },
  // Add more cases...
]
```

### 3.2 Golden Dataset Categories

| Category | Purpose | Minimum Cases |
|----------|---------|---------------|
| Happy Path | Normal operation | 10 |
| Edge Cases | Boundary conditions | 15 |
| Safety Critical | Must-detect scenarios | 20 |
| Fallback | Degraded operation | 5 |
| Error Handling | Error scenarios | 10 |

---

## 4. Agent Unit Tests

### 4.1 Agent Test Template

> **Note**: Adapt to your chosen test framework. This is pseudocode showing the pattern.

```
// tests/agents/[agent-name].test

// Mock LLM/AI service responses
mock("ai-service", () => ({
  complete: mockFn(),        // Text completion
  structured: mockFn(),      // Structured/JSON output
}))

describe("Agent1"):
  agent = null
  mockState = null

  beforeEach():
    agent = new Agent1()
    mockState = createMockState()
    clearAllMocks()

  describe("execute"):
    it("should return correct output for standard input"):
      mockAI.structured.returns({
        output: { field1: "value", field2: 1, confidence: 0.9 },
        usage: { totalTokens: 100 },
      })

      result = await agent.run(mockState)

      expect(result.output.field1).toBe("value")
      expect(result.metadata.fromFallback).toBe(false)

    it("should use fallback on AI service failure"):
      mockAI.structured.throws(new Error("AI Service Error"))

      result = await agent.run(mockState)

      expect(result.metadata.fromFallback).toBe(true)

    it("should respect timeout"):
      mockAI.structured.returns(
        delayedPromise(10000)  // Exceeds timeout
      )

      result = await agent.run(mockState)

      expect(result.metadata.fromFallback).toBe(true)

  describe("fallback"):
    it("should return valid default output"):
      fallback = agent.getFallback()

      expect(fallback).toMatchObject({
        field1: anyString(),
        field2: anyNumber(),
        confidence: anyNumber(),
      })

function createMockState():
  return {
    sessionId: "test-session",
    userId: "test-user",
    userMessage: "Test message",
    turnNumber: 1,
    startedAt: currentTime(),
  }
```

### 4.2 Safety Agent Tests

> **Note**: Adapt to your chosen test framework. This is pseudocode showing the pattern.

```
// tests/agents/safety-monitor.test

describe("SafetyMonitor"):
  agent = new SafetyMonitor()
  safetyTests = GOLDEN_DATASET.filter(t => t.category == "safety_critical")

  describe("golden dataset - safety critical"):
    for each testCase in safetyTests:
      it("should detect: " + testCase.description):
        result = await agent.run({
          userMessage: testCase.input.userMessage,
          // ... other required fields
        })

        expected = testCase.expected.agentOutputs.safety_monitor
        expect(result.output.concern_detected).toBe(expected.concern_detected)

        if expected.minimum_severity:
          expect(result.output.severity).toBeGreaterThanOrEqual(
            expected.minimum_severity
          )

  describe("recall"):
    it("should have 100% recall on critical safety cases"):
      criticalCases = safetyTests.filter(t => t.priority == "critical")
      detected = 0

      for testCase in criticalCases:
        result = await agent.run({
          userMessage: testCase.input.userMessage,
        })

        if result.output.concern_detected:
          detected++

      recall = detected / criticalCases.length
      expect(recall).toBe(1.0)  // 100% recall required
```

---

## 5. Integration Tests

### 5.1 Pipeline Integration Test

> **Note**: Adapt to your chosen test framework. This is pseudocode showing the pattern.

```
// tests/integration/pipeline.test

describe("Pipeline Integration"):
  beforeAll():
    await setupTestDb()

  afterAll():
    await teardownTestDb()

  it("should complete full pipeline successfully"):
    result = await runPipeline({
      sessionId: "test-session",
      userId: "test-user",
      userMessage: "Hello, how are you?",
    })

    expect(result.success).toBe(true)
    expect(result.finalResponse).toBeDefined()
    expect(result.executionTime).toBeLessThan(3000)

  it("should handle parallel agent failures gracefully"):
    // Force failure in one parallel agent
    result = await runPipeline({
      sessionId: "test-session",
      userId: "test-user",
      userMessage: "[trigger agent failure]",
    })

    // Pipeline should still complete with fallbacks
    expect(result.success).toBe(true)

  it("should respect latency budget"):
    iterations = 10
    latencies = []

    for i = 0 to iterations:
      result = await runPipeline({
        sessionId: "test-session-" + i,
        userId: "test-user",
        userMessage: "Test message",
      })
      latencies.push(result.executionTime)

    // Sort for percentile calculation
    latencies.sort((a, b) => a - b)
    p95Index = floor(latencies.length * 0.95)
    p95Latency = latencies[p95Index]

    expect(p95Latency).toBeLessThan(3000)  // <3s P95
```

### 5.2 API Integration Test

> **Note**: Adapt to your chosen web framework. This shows a generic HTTP server pattern.

```
// tests/integration/api.test

describe("API Integration"):
  server = null
  baseUrl = null

  beforeAll():
    // Start your application server on a random available port
    // Adapt this to your web framework (Express, Flask, Gin, etc.)
    server = startTestServer({ port: 0 })
    baseUrl = "http://localhost:" + server.port

  afterAll():
    await server.close()

  it("should handle valid request"):
    response = await httpPost(baseUrl + "/api/[endpoint]", {
      headers: { "Content-Type": "application/json" },
      body: {
        sessionId: "550e8400-e29b-41d4-a716-446655440000",
        userId: "550e8400-e29b-41d4-a716-446655440001",
        message: "Hello",
      },
    })

    expect(response.status).toBe(200)
    expect(response.body.success).toBe(true)
    expect(response.body.response).toBeDefined()

  it("should reject invalid request"):
    response = await httpPost(baseUrl + "/api/[endpoint]", {
      headers: { "Content-Type": "application/json" },
      body: {
        // Missing required fields
      },
    })

    expect(response.status).toBe(400)
```

---

## 6. E2E Tests

### 6.1 E2E Test Configuration

> **Note**: Adapt to your chosen E2E test framework (Playwright, Cypress, Selenium, etc.).

```
// tests/e2e/config

E2EConfig:
  testDir: "./tests/e2e"
  timeout: 30000
  retries: if CI then 2 else 0
  use:
    baseURL: env.BASE_URL OR "http://localhost:3000"
    trace: "on-first-retry"
    screenshot: "only-on-failure"
  projects:
    - name: "chromium", browserName: "chromium"
    - name: "firefox", browserName: "firefox"
  webServer:
    command: "start-server-command"
    port: 3000
    reuseExistingServer: not CI
```

### 6.2 E2E Test Example

> **Note**: Adapt to your chosen E2E test framework. This is pseudocode showing the pattern.

```
// tests/e2e/user-flow.test

describe("User Flow"):
  test("should complete basic interaction", ({ page }) => {
    await page.goto("/")

    // Find input and type message
    input = page.getByRole("textbox")
    await input.fill("Hello")
    await input.press("Enter")

    // Wait for response
    response = page.getByTestId("response")
    await expect(response).toBeVisible({ timeout: 5000 })
    await expect(response).not.toBeEmpty()

  test("should handle streaming response", ({ page }) => {
    await page.goto("/")

    input = page.getByRole("textbox")
    await input.fill("Tell me a story")
    await input.press("Enter")

    // Verify streaming indicator appears
    streamingIndicator = page.getByTestId("streaming")
    await expect(streamingIndicator).toBeVisible()

    // Wait for streaming to complete
    await expect(streamingIndicator).not.toBeVisible({ timeout: 10000 })

    // Verify response
    response = page.getByTestId("response")
    await expect(response).not.toBeEmpty()
```

---

## 7. Test Utilities

### 7.1 Mock Factories

> **Note**: Adapt to your chosen language. This is pseudocode showing the pattern.

```
// tests/helpers/factories

function createMockPipelineState(overrides = {}):
  return merge({
    sessionId: "test-session-id",
    userId: "test-user-id",
    userMessage: "Test message",
    turnNumber: 1,
    startedAt: currentTime(),
  }, overrides)

function createMockAgent1Output(overrides = {}):
  return merge({
    field1: "default",
    field2: 0,
    confidence: 0.5,
  }, overrides)
```

### 7.2 Test Helpers

> **Note**: Adapt to your chosen test framework and validation library.

```
// tests/helpers/assertions

function expectWithinLatencyBudget(actualMs, budgetMs, tolerance = 0.1):
  maxAllowed = budgetMs * (1 + tolerance)
  expect(actualMs).toBeLessThanOrEqual(maxAllowed)

function expectValidOutput(output, schema):
  result = schema.validate(output)
  expect(result.success).toBe(true)
```

---

## 8. CI/CD Integration

### 8.1 CI Workflow Structure

> **Note**: Adapt to your CI platform (GitHub Actions, GitLab CI, Jenkins, etc.) and language ecosystem.

```yaml
# Example CI workflow structure (adapt to your platform)

name: Test

triggers:
  - push to main
  - pull requests to main

jobs:
  unit-tests:
    runs-on: [your-runner]
    steps:
      - checkout code
      - setup [language] environment
      - install dependencies
      - run unit tests
      - run coverage report
      - upload coverage (optional)

  integration-tests:
    runs-on: [your-runner]
    services:
      database:
        image: [your-database]:latest
        env:
          DB_NAME: test
          DB_PASSWORD: test
        ports:
          - 5432:5432

    steps:
      - checkout code
      - setup [language] environment
      - install dependencies
      - run integration tests
        env:
          DATABASE_URL: [test-database-url]

  e2e-tests:
    runs-on: [your-runner]
    steps:
      - checkout code
      - setup [language] environment
      - install dependencies
      - install browser drivers (for E2E framework)
      - build application
      - run e2e tests
```

### 8.2 CI Job Responsibilities

| Job | Purpose | Typical Duration |
|-----|---------|------------------|
| `unit-tests` | Fast feedback on logic | 1-3 minutes |
| `integration-tests` | Verify component interaction | 3-10 minutes |
| `e2e-tests` | Validate user flows | 5-15 minutes |

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Configure test framework for your language
2. Create golden dataset for critical paths
3. Write agent unit tests with mocks
4. Add integration tests for pipeline
5. Set up E2E tests for user flows
6. Configure CI/CD pipeline for your platform

---

## Validation Schema (For AI Generation)

```yaml
# TESTING_STRATEGY validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - prompts[].examples[]: from_agent_prompts
  - prompts[].never[]: from_agent_prompts
  - extract.always[]: from_northstar_extract
  - extract.never[]: from_northstar_extract

outputs_produced:
  - testing.golden_dataset: used_by_ci_cd
  - testing.unit_tests: used_by_implementation
  - testing.integration_tests: used_by_implementation
  - testing.e2e_tests: used_by_implementation

validation_gate:
  required_sections:
    - "Test Configuration"
    - "Golden Dataset"
    - "Agent Unit Tests"
    - "Integration Tests"

  minimum_content:
    golden_cases: 5
    unit_test_patterns: 3
    integration_test_patterns: 2

cross_references:
  - golden_dataset: must_include: safety_critical_cases
  - unit_tests: must_cover: arch.agent_topology
  - never_tests: must_verify: extract.never

quality_checks:
  - golden_cases: have_input_expected_rationale
  - safety_tests: are_comprehensive
  - mocking: isolates_llm_calls

decision_prompts:
  - "What test framework? (Jest, Vitest, pytest, etc.)"
  - "What are the safety-critical paths that need golden tests?"
  - "How do you mock LLM responses for unit tests?"
```

**After generation, verify:**
- [ ] Golden dataset covers safety-critical paths
- [ ] Unit tests for each agent with mocked LLM
- [ ] Integration tests for pipeline flow
- [ ] NEVER clauses from agent prompts have test coverage

---

© [YEAR] [AUTHOR/ORG]

