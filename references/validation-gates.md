# Validation Gates Reference

Complete reference for all validation gates in North Star Advisor.

---

## Overview

Validation gates ensure generated documents meet quality standards and maintain cross-reference integrity. Gates are either **BLOCKING** (must pass to continue) or **WARNING** (logged but generation continues).

---

## Gate Naming Convention

```
G{phase}.{number}: {description}

G = Gate
{phase} = Phase number (1-13)
{number} = Gate number within phase
```

Example: `G2.1` = Phase 2, Gate 1

---

## Phase 1: BRAND_GUIDELINES

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G1.1 | Product name defined | BLOCKING | `brand.product_name` is non-empty string |
| G1.2 | At least 3 beliefs | BLOCKING | `brand.beliefs.length >= 3` |
| G1.3 | Kill list has items | BLOCKING | `brand.kill_list.length >= 1` |
| G1.4 | Voice/tone section present | WARNING | `## Voice` or `## Tone` section exists |
| G1.5 | No placeholder text | BLOCKING | No `TODO`, `TBD`, `[placeholder]` |
| G1.6 | Positioning section complete | WARNING | Has target audience and differentiator |

### G1 Failure Messages

```yaml
G1.1:
  message: "Product name is not defined"
  fix: "Add product name in ## Product Name section"

G1.2:
  message: "Only {count} beliefs found (minimum 3 required)"
  fix: "Add more beliefs that guide product decisions"

G1.3:
  message: "Kill list is empty"
  fix: "Add items you explicitly won't build in ## Kill List"
```

---

## Phase 2: NORTHSTAR

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G2.1 | Metric is measurable | BLOCKING | Contains number, count, rate, or percentage |
| G2.2 | Metric has time component | BLOCKING | Contains daily, weekly, monthly, or period |
| G2.3 | At least 2 personas | BLOCKING | `northstar.personas.length >= 2` |
| G2.4 | Personas have goal + pain | BLOCKING | Each persona has `goal` and `pain` fields |
| G2.5 | Success phases defined | BLOCKING | `## Success Phases` section exists with items |
| G2.6 | Scope boundaries clear | WARNING | Has `## In Scope` and `## Out of Scope` |
| G2.7 | Metric example provided | WARNING | Includes concrete example of metric |

### G2 Failure Messages

```yaml
G2.1:
  message: "Metric '{metric}' is not measurable"
  fix: "Add quantifiable component (e.g., 'Weekly Active Users' not just 'User Engagement')"

G2.2:
  message: "Metric lacks time component"
  fix: "Add time qualifier (e.g., 'Daily', 'Weekly', 'Monthly')"

G2.3:
  message: "Only {count} persona(s) defined (minimum 2 required)"
  fix: "Add another persona representing a different user type"
```

---

## Phase 3: COMPETITIVE_LANDSCAPE

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G3.1 | At least 3 competitors | BLOCKING | `competitive.competitors.length >= 3` |
| G3.2 | Feature comparison table | BLOCKING | Markdown table with features |
| G3.3 | Differentiation stated | BLOCKING | `## Differentiation` section non-empty |
| G3.4 | Market trends section | WARNING | `## Market Trends` section exists |
| G3.5 | No competitor placeholders | BLOCKING | No `Competitor A`, `Company X` |
| G3.6 | Pricing information | WARNING | At least 2 competitors have pricing |
| G3.7 | Sources cited | WARNING | URLs or references for claims |

### G3 Failure Messages

```yaml
G3.1:
  message: "Only {count} competitor(s) analyzed (minimum 3 required)"
  fix: "Research and add more competitors using /northstar:generate --research"

G3.2:
  message: "Feature comparison table missing"
  fix: "Add markdown table comparing features across competitors"

G3.5:
  message: "Placeholder competitor names found: {names}"
  fix: "Replace with actual competitor names from research"
```

---

## Phase 4: NORTHSTAR_EXTRACT

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G4.1 | Axioms extracted | BLOCKING | `## Axioms` section with items |
| G4.2 | Non-goals listed | BLOCKING | `## Non-Goals` section with items |
| G4.3 | Constraints identified | WARNING | `## Constraints` section exists |
| G4.4 | Cross-refs to source docs | BLOCKING | Contains refs to phases 1-3 |
| G4.5 | Axioms are actionable | WARNING | Axioms use active verbs |

---

## Phase 5: Design Templates (--ux)

### 5a: USER_JOURNEYS

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G5a.1 | Journey per persona | BLOCKING | Journey exists for each persona |
| G5a.2 | Friction points identified | BLOCKING | Each journey has friction section |
| G5a.3 | Success moments defined | BLOCKING | Each journey has success moments |
| G5a.4 | Emotional states mapped | WARNING | Includes emotional annotations |

### 5b: UI_DESIGN_SYSTEM

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G5b.1 | Color palette defined | BLOCKING | Primary, secondary, accent colors |
| G5b.2 | Typography scale | BLOCKING | Font families and sizes |
| G5b.3 | Spacing system | WARNING | Consistent spacing units |
| G5b.4 | Component patterns | WARNING | At least 3 component examples |

### 5c: ACCESSIBILITY

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G5c.1 | WCAG level stated | BLOCKING | AA or AAA compliance target |
| G5c.2 | Keyboard patterns | BLOCKING | Keyboard navigation described |
| G5c.3 | Screen reader patterns | WARNING | ARIA guidelines included |
| G5c.4 | Testing protocol | WARNING | Accessibility testing plan |

---

## Phase 6: ARCHITECTURE_BLUEPRINT

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G6.1 | Agent topology defined | BLOCKING | `## Agent Topology` with diagram/list |
| G6.2 | Orchestration pattern named | BLOCKING | One of: centralized, distributed, hierarchical, hybrid |
| G6.3 | Each agent has responsibility | BLOCKING | Every agent has role description |
| G6.4 | Tech stack specified | BLOCKING | Runtime, framework, database listed |
| G6.5 | Data flow documented | WARNING | Data flow diagram or description |
| G6.6 | Integration points listed | WARNING | External integrations identified |
| G6.7 | Scalability addressed | WARNING | Scaling considerations noted |

### G6 Failure Messages

```yaml
G6.1:
  message: "Agent topology not defined"
  fix: "Add ## Agent Topology section with agent list and relationships"

G6.2:
  message: "Orchestration pattern not specified"
  fix: "Choose from: centralized, distributed, hierarchical, hybrid"

G6.4:
  message: "Tech stack incomplete"
  fix: "Specify runtime, framework, and database choices"
```

---

## Phase 7: AGENT_PROMPTS

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G7.1 | System prompt per agent | BLOCKING | Each agent has `## System Prompt` |
| G7.2 | Tools listed per agent | BLOCKING | Each agent has `## Tools` |
| G7.3 | Example I/O provided | WARNING | At least one example per agent |
| G7.4 | Handoff protocol defined | WARNING | Agent-to-agent handoff described |
| G7.5 | Error handling specified | WARNING | How agents handle failures |

---

## Phase 8: SECURITY_ARCHITECTURE

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G8.1 | Threat model present | BLOCKING | `## Threat Model` with threats |
| G8.2 | Auth pattern defined | BLOCKING | Authentication method specified |
| G8.3 | Data classification | BLOCKING | Data types and sensitivity levels |
| G8.4 | Safety guardrails | BLOCKING | AI safety measures documented |
| G8.5 | Incident response | WARNING | Response procedures outlined |
| G8.6 | Audit logging | WARNING | What is logged for security |

---

## Phase 9: ADR

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G9.1 | At least 3 decisions | BLOCKING | `## ADR-` sections count >= 3 |
| G9.2 | Each has context | BLOCKING | All ADRs have Context section |
| G9.3 | Each has decision | BLOCKING | All ADRs have Decision section |
| G9.4 | Alternatives documented | WARNING | Alternatives Rejected section |
| G9.5 | Consequences noted | WARNING | Consequences section present |

---

## Phase 10: POST_DEPLOYMENT

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G10.1 | Monitoring strategy | BLOCKING | `## Monitoring` section exists |
| G10.2 | Key metrics defined | BLOCKING | Metrics to track listed |
| G10.3 | Alerting thresholds | WARNING | Alert conditions specified |
| G10.4 | Runbook present | WARNING | Operational procedures documented |

---

## Phase 11: STRATEGIC_RECOMMENDATION

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G11.1 | Recommended path stated | BLOCKING | Clear recommendation present |
| G11.2 | Trade-offs analyzed | BLOCKING | `## Trade-offs` section |
| G11.3 | Alternatives presented | WARNING | Alternative paths discussed |
| G11.4 | Risk assessment | WARNING | Risks identified |

---

## Phase 12: ACTION_ROADMAP

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G12.1 | 30-day actions | BLOCKING | Near-term actions listed |
| G12.2 | Actions are specific | BLOCKING | No vague "improve" or "research" |
| G12.3 | Dependencies noted | WARNING | Action dependencies shown |
| G12.4 | Success criteria | WARNING | How to know actions complete |

---

## Phase 13: INDEX

| Gate | Rule | Severity | Check |
|------|------|----------|-------|
| G13.1 | All docs linked | BLOCKING | Every generated doc has link |
| G13.2 | Links resolve | BLOCKING | All `[text](path)` links valid |
| G13.3 | Summary present | WARNING | Each doc has brief summary |
| G13.4 | Navigation clear | WARNING | Logical grouping of links |
| G13.5 | Quick start section | WARNING | Getting started guidance |

---

## Validation Levels

### Strict (Default)

- All BLOCKING gates must pass
- Warnings are logged
- Generation stops on failure
- Used for: final generation, export

### Lenient

- BLOCKING gates downgraded to WARNING
- All warnings logged
- Generation continues
- Used for: draft iterations, partial generation

### Skip (Not Recommended)

- All validation bypassed
- Used only for: debugging, recovery

---

## Validation API

### Requesting Validation

```yaml
# Input to validator agent
document_path: "north-star-advisor/docs/NORTHSTAR.md"
template_name: "NORTHSTAR"
validation_level: "strict"
```

### Validation Result

```yaml
# Output from validator agent
success: false
gates:
  total: 7
  passed: 5
  failed: 2
  warnings: 1

failures:
  - gate: "G2.1"
    rule: "Metric is measurable"
    severity: "BLOCKING"
    message: "Metric 'User Success' is not measurable"
    location: "line 23"
    suggestion: "Add quantifiable component"

warnings:
  - gate: "G2.6"
    rule: "Scope boundaries clear"
    severity: "WARNING"
    message: "Scope section brief"

action_required: "Fix 2 blocking failures to continue"
```

---

## Custom Gates

Projects can define custom validation in `north-star-advisor/.work-in-progress/custom-gates.yml`:

```yaml
# Custom gates for this project
custom_gates:
  - id: "GC.1"
    phase: 6
    rule: "Must use PostgreSQL"
    check: "tech_stack.database == 'PostgreSQL'"
    severity: "BLOCKING"
    message: "Project requires PostgreSQL for compliance"
```
