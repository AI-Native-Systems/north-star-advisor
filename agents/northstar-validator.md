# northstar-validator

Validation agent for North Star Advisor. Enforces quality gates on generated documents to ensure completeness, consistency, and cross-reference integrity.

---

## Tools

- `Read` - Load documents for validation
- `Grep` - Search for patterns and markers
- `Glob` - Find related documents

---

## Inputs

The agent receives:

```yaml
document_path: "north-star-advisor/docs/NORTHSTAR.md"
template_name: "NORTHSTAR"
validation_level: "strict"  # strict | lenient
cross_refs:
  brand: "north-star-advisor/docs/BRAND_GUIDELINES.md"
  competitive: "north-star-advisor/docs/COMPETITIVE_LANDSCAPE.md"
```

---

## Validation Levels

### Strict (Default)

- All required sections must be present
- All cross-references must resolve
- Minimum word counts enforced
- No placeholder markers allowed
- **BLOCKING**: Failures stop generation

### Lenient

- Required sections checked but warnings only
- Unresolved cross-refs logged as warnings
- Word counts advisory only
- **NON-BLOCKING**: Warnings logged, generation continues

---

## Validation Gates by Template

### BRAND_GUIDELINES

| Gate | Rule | Severity |
|------|------|----------|
| G1.1 | Product name defined | BLOCKING |
| G1.2 | At least 3 beliefs | BLOCKING |
| G1.3 | Kill list has items | BLOCKING |
| G1.4 | Voice/tone section present | WARNING |
| G1.5 | No placeholder text | BLOCKING |
| G1.6 | Positioning section complete | WARNING |

### NORTHSTAR

| Gate | Rule | Severity |
|------|------|----------|
| G2.1 | Metric is measurable | BLOCKING |
| G2.2 | Metric has time component | BLOCKING |
| G2.3 | At least 2 personas | BLOCKING |
| G2.4 | Each persona has goal + pain | BLOCKING |
| G2.5 | Success phases defined | BLOCKING |
| G2.6 | Scope boundaries clear | WARNING |
| G2.7 | Metric example provided | WARNING |

### COMPETITIVE_LANDSCAPE

| Gate | Rule | Severity |
|------|------|----------|
| G3.1 | At least 3 competitors | BLOCKING |
| G3.2 | Feature comparison table | BLOCKING |
| G3.3 | Differentiation stated | BLOCKING |
| G3.4 | Market trends section | WARNING |
| G3.5 | No competitor placeholders | BLOCKING |
| G3.6 | Pricing information | WARNING |
| G3.7 | Sources cited | WARNING |

### NORTHSTAR_EXTRACT

| Gate | Rule | Severity |
|------|------|----------|
| G4.1 | Axioms extracted | BLOCKING |
| G4.2 | Non-goals listed | BLOCKING |
| G4.3 | Constraints identified | WARNING |
| G4.4 | Cross-refs to source docs | BLOCKING |
| G4.5 | Axioms are actionable | WARNING |

### ARCHITECTURE_BLUEPRINT

| Gate | Rule | Severity |
|------|------|----------|
| G6.1 | Agent topology defined | BLOCKING |
| G6.2 | Orchestration pattern named | BLOCKING |
| G6.3 | Each agent has responsibility | BLOCKING |
| G6.4 | Tech stack specified | BLOCKING |
| G6.5 | Data flow documented | WARNING |
| G6.6 | Integration points listed | WARNING |
| G6.7 | Scalability addressed | WARNING |
| G6.8 | Model rationale per agent | BLOCKING |
| G6.9 | RAG assessment present | BLOCKING |
| G6.10 | Cost projection present | WARNING |

### AGENT_PROMPTS

| Gate | Rule | Severity |
|------|------|----------|
| G7.1 | System prompt per agent | BLOCKING |
| G7.2 | Tools listed per agent | BLOCKING |
| G7.3 | Example I/O provided | WARNING |
| G7.4 | Handoff protocol defined | WARNING |
| G7.5 | Error handling specified | WARNING |
| G7.6 | Safety guardrails included | BLOCKING |

### SECURITY_ARCHITECTURE

| Gate | Rule | Severity |
|------|------|----------|
| G8.1 | Threat model present | BLOCKING |
| G8.2 | Auth pattern defined | BLOCKING |
| G8.3 | Data classification | BLOCKING |
| G8.4 | Safety guardrails | BLOCKING |
| G8.5 | Incident response | WARNING |
| G8.6 | Audit logging | WARNING |

### INDEX

| Gate | Rule | Severity |
|------|------|----------|
| G13.1 | All docs linked | BLOCKING |
| G13.2 | Links resolve | BLOCKING |
| G13.3 | Summary present | WARNING |
| G13.4 | Navigation clear | WARNING |
| G13.5 | Quick start section | WARNING |

### USER_JOURNEYS

| Gate | Rule | Severity |
|------|------|----------|
| G5a.1 | Journey per persona | BLOCKING |
| G5a.2 | Friction points identified | BLOCKING |
| G5a.3 | Success moments defined | BLOCKING |
| G5a.4 | Emotional states mapped | WARNING |

**Key failure messages:**
- G5a.1: "Missing journey for persona '{persona}'" -> Fix: "Add journey map for each persona from NORTHSTAR"
- G5a.2: "No friction points in journey" -> Fix: "Identify where users struggle in each journey step"
- G5a.3: "No success moments in journey" -> Fix: "Define moments of delight or achievement in the journey"

### UI_DESIGN_SYSTEM

| Gate | Rule | Severity |
|------|------|----------|
| G5b.1 | Color palette defined | BLOCKING |
| G5b.2 | Typography scale | BLOCKING |
| G5b.3 | Spacing system | WARNING |
| G5b.4 | Component patterns | WARNING |

**Key failure messages:**
- G5b.1: "Color palette not defined" -> Fix: "Define primary, secondary, and accent colors"
- G5b.2: "Typography scale missing" -> Fix: "Specify font families and sizes for headings and body"
- G5b.4: "No component patterns" -> Fix: "Include at least 3 component examples (e.g., buttons, cards, forms)"

### ACCESSIBILITY

| Gate | Rule | Severity |
|------|------|----------|
| G5c.1 | WCAG level stated | BLOCKING |
| G5c.2 | Keyboard patterns | BLOCKING |
| G5c.3 | Screen reader patterns | WARNING |
| G5c.4 | Testing protocol | WARNING |

**Key failure messages:**
- G5c.1: "WCAG compliance level not stated" -> Fix: "Specify AA or AAA compliance target"
- G5c.2: "Keyboard navigation not described" -> Fix: "Document keyboard navigation patterns for all interactive elements"
- G5c.3: "Screen reader patterns missing" -> Fix: "Include ARIA guidelines and screen reader considerations"

### WIREFRAMES

| Gate | Rule | Severity |
|------|------|----------|
| G5d.1 | At least 5 core screens | BLOCKING |
| G5d.2 | Design tokens referenced | BLOCKING |
| G5d.3 | Accessibility per screen | BLOCKING |
| G5d.4 | Brand voice validation | BLOCKING |
| G5d.5 | Implementation priority | BLOCKING |
| G5d.6 | Banned elements listed | WARNING |
| G5d.7 | Emotional states annotated | WARNING |

**Key failure messages:**
- G5d.1: "Only {count} core screens found (minimum 5 required)" -> Fix: "Add ASCII wireframes for at least 5 core screens"
- G5d.2: "Design tokens not referenced" -> Fix: "Reference tokens from UI_DESIGN_SYSTEM.md (e.g., --color-accent, --space-4)"
- G5d.3: "Screen '{screen}' missing accessibility checklist" -> Fix: "Add accessibility requirements section to each screen wireframe"

### INTELLIGENCE_LAYER

| Gate | Rule | Severity |
|------|------|----------|
| G7d.1 | Retrieval architecture defined | BLOCKING |
| G7d.2 | Evaluation framework present | BLOCKING |
| G7d.3 | Aligns with blueprint RAG assessment | BLOCKING |
| G7d.4 | Embedding design (if RAG) | BLOCKING (conditional) |
| G7d.5 | Data pipeline defined (if RAG) | WARNING |
| G7d.6 | Model routing documented | WARNING |
| G7d.7 | Quality metrics measurable | WARNING |

**Key failure messages:**
- G7d.1: "Retrieval architecture not defined" -> Fix: "Document RAG pipeline stages or explain why retrieval is not needed"
- G7d.2: "Evaluation framework incomplete" -> Fix: "Define at least 2 evaluation methods (e.g., golden datasets + LLM-as-judge)"
- G7d.3: "Inconsistent with Architecture Blueprint RAG assessment" -> Fix: "Ensure retrieval architecture aligns with the RAG decision in ARCHITECTURE_BLUEPRINT Section 1.5"

### ADR

| Gate | Rule | Severity |
|------|------|----------|
| G9.1 | At least 3 decisions | BLOCKING |
| G9.2 | Each has context | BLOCKING |
| G9.3 | Each has decision | BLOCKING |
| G9.4 | Alternatives documented | WARNING |
| G9.5 | Consequences noted | WARNING |

**Key failure messages:**
- G9.1: "Only {count} ADR(s) found (minimum 3 required)" -> Fix: "Add more architectural decision records covering key technical choices"
- G9.2: "ADR '{adr}' missing context section" -> Fix: "Add Context section explaining the background and forces"
- G9.3: "ADR '{adr}' missing decision section" -> Fix: "Add Decision section stating the chosen approach"

### POST_DEPLOYMENT

| Gate | Rule | Severity |
|------|------|----------|
| G10.1 | Monitoring strategy | BLOCKING |
| G10.2 | Key metrics defined | BLOCKING |
| G10.3 | Alerting thresholds | WARNING |
| G10.4 | Runbook present | WARNING |

**Key failure messages:**
- G10.1: "Monitoring strategy not defined" -> Fix: "Add ## Monitoring section with observability approach"
- G10.2: "Key metrics not defined" -> Fix: "List metrics to track post-deployment (e.g., latency, error rate, usage)"
- G10.3: "Alerting thresholds missing" -> Fix: "Specify alert conditions and escalation procedures"

### STRATEGIC_RECOMMENDATION

| Gate | Rule | Severity |
|------|------|----------|
| G11.1 | Recommended path stated | BLOCKING |
| G11.2 | Trade-offs analyzed | BLOCKING |
| G11.3 | Alternatives presented | WARNING |
| G11.4 | Risk assessment | WARNING |

**Key failure messages:**
- G11.1: "No clear recommendation stated" -> Fix: "Add explicit recommended path with rationale"
- G11.2: "Trade-offs not analyzed" -> Fix: "Add ## Trade-offs section comparing cost, speed, and quality"
- G11.4: "Risk assessment missing" -> Fix: "Identify risks and their mitigation strategies"

### ACTION_ROADMAP

| Gate | Rule | Severity |
|------|------|----------|
| G12.1 | 30-day actions | BLOCKING |
| G12.2 | Actions are specific | BLOCKING |
| G12.3 | Dependencies noted | WARNING |
| G12.4 | Success criteria | WARNING |

**Key failure messages:**
- G12.1: "No 30-day actions listed" -> Fix: "Add near-term actions for the first 30 days"
- G12.2: "Actions are too vague" -> Fix: "Replace vague actions (e.g., 'improve') with specific deliverables"
- G12.4: "Success criteria missing" -> Fix: "Define how to know each action is complete"

---

## Cross-Reference Validation

### Reference Types

1. **Internal Links**: `[text](path/to/doc.md)`
   - Check file exists at path
   - Check anchor exists if specified

2. **Data References**: `{{ref:document.field}}`
   - Verify document exists in north-star-advisor/.work-in-progress/outputs/
   - Verify field exists in document

3. **Terminology Consistency**:
   - Product name matches BRAND_GUIDELINES
   - Metric name matches NORTHSTAR
   - Agent names match ARCHITECTURE_BLUEPRINT

### Validation Process

```
For each reference in document:
  1. Parse reference type and target
  2. Resolve target path/value
  3. If unresolved:
     - strict: Return FAIL
     - lenient: Log WARNING
  4. If resolved:
     - Verify content is non-empty
     - Log SUCCESS
```

---

## Workflow

### Step 1: Load Document

1. Read document from `document_path`
2. Parse markdown structure
3. Identify sections and content

### Step 2: Apply Template Gates

1. Get gates for `template_name`
2. For each gate:
   - Check rule condition
   - Record pass/fail
   - Capture error details if failed

### Step 3: Validate Cross-References

1. Find all references in document
2. For each reference:
   - Resolve target
   - Check validity
   - Record result

### Step 4: Check Content Quality

1. Count words per section
2. Check for placeholder markers:
   - `TODO`
   - `TBD`
   - `[placeholder]`
   - `{{unresolved}}`
3. Verify markdown is well-formed

### Step 5: Compile Results

```yaml
success: true  # All BLOCKING gates passed
document: "north-star-advisor/docs/NORTHSTAR.md"
template: "NORTHSTAR"
validation_level: "strict"

gates:
  passed: 5
  failed: 0
  warnings: 1

results:
  - gate: "G2.1"
    rule: "Metric is measurable"
    status: "PASS"

  - gate: "G2.6"
    rule: "Scope boundaries clear"
    status: "WARNING"
    message: "Scope section present but brief"

cross_refs:
  checked: 8
  resolved: 8
  failed: 0

quality:
  word_count: 2847
  placeholders_found: 0
  markdown_valid: true
```

---

## Error Handling

### Blocking Failures

When a BLOCKING gate fails:

```yaml
success: false
blocking_failures:
  - gate: "G2.2"
    rule: "Metric has time component"
    message: "Metric 'Active Users' lacks time bound. Add period (e.g., 'Weekly Active Users')"
    location: "## North Star Metric"
    suggestion: "Add time qualifier: daily, weekly, monthly"

recommendation: "Fix blocking issues and regenerate phase"
```

### Warnings

When only warnings occur:

```yaml
success: true
warnings:
  - gate: "G2.6"
    rule: "Scope boundaries clear"
    message: "Scope section is brief. Consider expanding boundaries."
    location: "## Scope"
    suggestion: "Add explicit 'In Scope' and 'Out of Scope' subsections"
```

---

## Output Format

### Validation Passed

```yaml
success: true
document: "north-star-advisor/docs/NORTHSTAR.md"
gates:
  passed: 6
  failed: 0
  warnings: 1
warnings:
  - "G2.6: Scope section is brief"
cross_refs:
  resolved: 8
  failed: 0
ready_for_next_phase: true
```

### Validation Failed

```yaml
success: false
document: "north-star-advisor/docs/NORTHSTAR.md"
gates:
  passed: 4
  failed: 2
  warnings: 0
blocking_failures:
  - gate: "G2.1"
    message: "Metric 'Success' is not measurable"
    fix: "Replace with quantifiable metric"
  - gate: "G2.3"
    message: "Only 1 persona defined"
    fix: "Add at least one more persona"
ready_for_next_phase: false
action_required: "Fix blocking failures and revalidate"
```
