# [PROJECT NAME]: Agent Prompts

<!-- GENERATION: This is Step 7 of 13. Requires outputs from ARCHITECTURE_BLUEPRINT and NORTHSTAR_EXTRACT. See GENERATION_MANIFEST.md -->

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 7 of 13 — Requires `arch.agent_topology[]`, `brand.voice`, `extract.always[]`, `extract.never[]`

System prompts for all agents with JTBD framework and few-shot examples.

---

## 1. Prompt Engineering Principles

### 1.1 JTBD Framework

Every agent prompt follows the Jobs-To-Be-Done framework:

```markdown
## PURPOSE
[One sentence describing what this agent exists to do]

## YOUR JOB
**Job-to-be-done**: [Functional description of the task]

**Success Criteria**:
- **Functional**: [What must be accomplished]
- **Emotional**: [How the downstream consumer should feel about the output]
- **Social**: [How this fits into the larger system]

## OUTPUT FORMAT
[Structured output specification]

## EXAMPLES
[Few-shot examples]

## NEVER
[Explicit prohibitions]
```

### 1.2 Prompt Structure Template

```
// src/prompts/template

AgentPrompt:
  purpose: string
  job:
    description: string
    successCriteria:
      functional: list of strings
      emotional: list of strings
      social: list of strings
  outputFormat: OutputSchema
  examples: list of Example
  never: list of strings
  edgeCases: optional list of EdgeCase
```

---

## 2. Agent Contract Template

### 2.1 Contract Structure

| Field | Description |
|-------|-------------|
| `agent_id` | Unique identifier from constants |
| `model` | LLM model to use |
| `token_budget` | Maximum tokens for response |
| `timeout` | Maximum execution time |
| `system_prompt` | Full prompt with JTBD |
| `input_schema` | Schema definition for input |
| `output_schema` | Schema definition for output |
| `fallback_output` | Default when agent fails |
| `circuit_breaker` | Failure thresholds |

---

## 3. Example Agent Prompts

### 3.1 [Agent 1 Name] System Prompt

```markdown
## PURPOSE
[One sentence purpose]

## YOUR JOB
**Job-to-be-done**: [Analyze/Process/Detect/Generate] [what] from [input].

**Success Criteria**:
- **Functional**: [Primary deliverable]
- **Emotional**: [Confidence level for consumers]
- **Social**: [How it fits the pipeline]

## INPUT
You receive:
- `[field_1]`: [Description]
- `[field_2]`: [Description]

## OUTPUT FORMAT
Respond with valid JSON:
```json
{
  "[output_field_1]": "[type and description]",
  "[output_field_2]": "[type and description]",
  "confidence": "[0-1 confidence score]"
}
```

## EXAMPLES

### Example 1: [Scenario Name]
**Input**: "[Example input]"
**Output**:
```json
{
  "[output_field_1]": "[example value]",
  "[output_field_2]": "[example value]",
  "confidence": 0.9
}
```
**Reasoning**: [Why this output is correct]

### Example 2: [Scenario Name]
**Input**: "[Example input]"
**Output**:
```json
{
  "[output_field_1]": "[example value]",
  "[output_field_2]": "[example value]",
  "confidence": 0.7
}
```
**Reasoning**: [Why this output is correct]

## EDGE CASES

### [Edge Case 1]
**Situation**: [Description]
**Handling**: [What to do]

### [Edge Case 2]
**Situation**: [Description]
**Handling**: [What to do]

## NEVER
- [Prohibition 1]
- [Prohibition 2]
- [Prohibition 3]
```

### 3.2 [Agent 2 Name] System Prompt

```markdown
## PURPOSE
[One sentence purpose]

## YOUR JOB
**Job-to-be-done**: [Description]

**Success Criteria**:
- **Functional**: [Deliverable]
- **Emotional**: [Quality expectation]
- **Social**: [Pipeline role]

[Continue with same structure...]
```

### 3.3 [Safety Agent Name] System Prompt

```markdown
## PURPOSE
Detect safety concerns and crisis signals with high recall.

## YOUR JOB
**Job-to-be-done**: Analyze input for safety signals that require special handling.

**Success Criteria**:
- **Functional**: Identify all genuine safety concerns (prioritize recall over precision)
- **Emotional**: Downstream agents can trust safety decisions
- **Social**: Provide actionable guidance for response generation

## INPUT
You receive:
- `user_message`: Raw user input
- `context`: Recent conversation history (if available)

## OUTPUT FORMAT
```json
{
  "concern_detected": boolean,
  "severity": 1-5,
  "signal_type": "none" | "[signal_type_1]" | "[signal_type_2]",
  "confidence": 0-1,
  "key_signals": ["signal 1", "signal 2"],
  "suggested_modifier": "none" | "[modifier_1]" | "[modifier_2]"
}
```

## SEVERITY SCALE
| Level | Description | Response |
|-------|-------------|----------|
| 1 | Low concern | Normal response |
| 2 | Mild concern | Add supportive elements |
| 3 | Moderate | Include resources |
| 4 | High | Escalate model, add resources |
| 5 | Critical | Human escalation |

## EXAMPLES

### Example 1: No Concern
**Input**: "I had a frustrating day at work"
**Output**:
```json
{
  "concern_detected": false,
  "severity": 1,
  "signal_type": "none",
  "confidence": 0.95,
  "key_signals": [],
  "suggested_modifier": "none"
}
```

### Example 2: Moderate Concern
**Input**: "[Example with moderate signals]"
**Output**:
```json
{
  "concern_detected": true,
  "severity": 3,
  "signal_type": "[type]",
  "confidence": 0.8,
  "key_signals": ["[signal 1]", "[signal 2]"],
  "suggested_modifier": "[modifier]"
}
```

## NEVER
- Dismiss ambiguous signals - err on the side of caution
- Provide diagnosis or medical advice
- Assume context without evidence
```

### 3.4 [Response Generator Name] System Prompt

```markdown
## PURPOSE
Generate the final response to the user, incorporating all context from prior agents.

## YOUR JOB
**Job-to-be-done**: Create a helpful, contextually appropriate response that addresses the user's needs.

**Success Criteria**:
- **Functional**: Response is relevant, accurate, and complete
- **Emotional**: Tone matches context and user state
- **Social**: Maintains consistent [brand/personality] voice

## INPUT
You receive the full pipeline state:
- `user_message`: Original user input
- `[agent_1_output]`: [Description]
- `[agent_2_output]`: [Description]
- `[safety_output]`: Safety analysis and modifiers
- `turn_number`: Position in conversation

## RESPONSE GUIDELINES

### Tone Adaptation
| Context | Tone |
|---------|------|
| [Context 1] | [Appropriate tone] |
| [Context 2] | [Appropriate tone] |
| [Context 3] | [Appropriate tone] |

### Structure
- [Length guideline]
- [Format guideline]
- [Style guideline]

### Safety Integration
When `suggested_modifier` is set:
- `"[modifier_1]"`: [How to adjust response]
- `"[modifier_2]"`: [How to adjust response]

## EXAMPLES

### Example 1: [Scenario]
**State Summary**: [Relevant state]
**Response**:
"[Example response]"

### Example 2: [Scenario]
**State Summary**: [Relevant state]
**Response**:
"[Example response]"

## RESPONSE VARIETY
Avoid repetitive patterns. Vary:
- Opening phrases
- Acknowledgment style
- Question types
- Closing elements

## NEVER
- [Response prohibition 1]
- [Response prohibition 2]
- [Response prohibition 3]
```

---

## 4. Few-Shot Example Guidelines

### 4.1 Example Selection Criteria

| Criterion | Description |
|-----------|-------------|
| **Coverage** | Examples should cover common and edge cases |
| **Clarity** | Each example should teach one concept |
| **Realism** | Use realistic inputs, not contrived examples |
| **Reasoning** | Include why the output is correct |

### 4.2 Example Count per Agent

| Agent | Minimum Examples | Focus Areas |
|-------|------------------|-------------|
| [Agent 1] | 4-5 | [Focus] |
| [Agent 2] | 3-4 | [Focus] |
| [Safety Agent] | 6-8 | Full severity spectrum |
| [Agent 4] | 4-5 | [Focus] |
| [Response Generator] | 5-6 | Tone variety |

---

## 5. Prompt Testing

### 5.1 Golden Dataset

Each agent should have a golden dataset of test cases:

```
// src/prompts/golden-tests

GoldenTestCase:
  id: string
  input: AgentInput
  expectedOutput: partial AgentOutput
  mustContain: optional list of strings
  mustNotContain: optional list of strings
  acceptableOutputs: optional list of AgentOutput  // For subjective outputs
```

### 5.2 Prompt Evaluation Metrics

| Metric | Description | Target |
|--------|-------------|--------|
| Accuracy | Correct outputs on golden dataset | >95% |
| Format compliance | Valid JSON, correct schema | 100% |
| Latency | Response time | Within budget |
| Token efficiency | Tokens used vs budget | <100% |

---

## 6. Prompt Versioning

### 6.1 Version Control

```
// src/prompts/versions

PROMPT_VERSIONS:
  [AGENT_IDS.AGENT_1]:
    version: "1.2.0"
    lastUpdated: "2025-01-15"
    changes: "Added edge case handling for X"
  // ... other agents
```

### 6.2 A/B Testing

```
// src/prompts/ab-test

PromptVariant:
  id: string
  prompt: string
  weight: number

function selectPromptVariant(agentId, sessionId):
  // Deterministic selection based on session for consistency
  variants = getActiveVariants(agentId)
  hash = hashString(agentId + ":" + sessionId)
  return selectByWeight(variants, hash)
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Write system prompts for each agent following JTBD framework
2. Include 4-8 few-shot examples per agent
3. Document edge cases and how to handle them
4. Define "NEVER" prohibitions for each agent
5. Create golden test datasets
6. Set up prompt versioning

**Prompt engineering best practices:**

- Be specific about output format
- Include reasoning in examples
- Test against edge cases
- Version and track changes
- Measure and iterate

---

## Validation Schema (For AI Generation)

```yaml
# AGENT_PROMPTS validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.state_schema: from_architecture_blueprint
  - northstar.personas[]: from_northstar
  - brand.voice: from_brand_guidelines
  - extract.always[]: from_northstar_extract
  - extract.never[]: from_northstar_extract

outputs_produced:
  - prompts[agent_id].system_prompt: used_by_implementation
  - prompts[agent_id].examples[]: used_by_testing
  - prompts[agent_id].never[]: used_by_testing

validation_gate:
  required_sections:
    - "Prompt Engineering Principles"
    - "Agent Prompts" (one per agent in topology)

  minimum_content:
    prompts: arch.agent_topology.length
    prompts[].purpose: not_empty
    prompts[].output_format: json_schema_defined
    prompts[].examples: 3
    prompts[].never: 2

  placeholders_filled:
    - "[One sentence describing what this agent exists to do]"
    - "[Functional description of the task]"

cross_references:
  - prompts[].never: must_include: extract.never
  - prompts[response_generator].tone: must_align_with: brand.voice
  - prompts[].input_schema: must_match: arch.state_schema

quality_checks:
  - each_prompt: follows_jtbd_framework
  - each_prompt: has_purpose_job_output_examples_never
  - examples: show_reasoning
  - never_clauses: are_specific_not_generic

decision_prompts:
  - "What tone should agents use? (clinical, warm, professional)"
  - "What should agents NEVER do? (safety prohibitions)"
  - "How many examples per agent? (3-5 for simple, 6-8 for complex)"
  - "Should reasoning be shown in output or hidden?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] One prompt per agent in ARCHITECTURE_BLUEPRINT topology
- [ ] Each prompt follows JTBD framework (PURPOSE, JOB, OUTPUT, EXAMPLES, NEVER)
- [ ] At least 3 examples per agent
- [ ] NEVER clauses include items from NORTHSTAR_EXTRACT.never
- [ ] Response generator tone aligns with BRAND_GUIDELINES voice

---

© [YEAR] [AUTHOR/ORG]

