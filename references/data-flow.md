# Data Flow Reference

Cross-document dependencies and data flow for North Star Advisor generation.

---

## Overview

Documents in the North Star methodology are interconnected. Each document builds on outputs from previous phases, creating a coherent strategic narrative. This reference defines the data contracts between documents.

---

## Dependency Graph

```
                    INPUTS
                      │
                      ▼
              ┌───────────────┐
              │ 1. BRAND      │
              │   GUIDELINES  │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │ 2. NORTHSTAR  │
              └───────┬───────┘
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
┌─────────────────┐      ┌───────────────┐
│ 3. COMPETITIVE  │      │ [Research]    │
│    LANDSCAPE    │◄─────│               │
└────────┬────────┘      └───────────────┘
         │
         ▼
┌─────────────────┐
│ 4. NORTHSTAR    │
│    EXTRACT      │
└────────┬────────┘
         │
    ┌────┴────┐ (--ux flag)
    │         │
    ▼         ▼
┌────────┐ ┌────────┐ ┌────────┐
│ 5a.    │ │ 5b. UI │ │ 5c.    │
│ USER   │→│ DESIGN │→│ ACCESS │
│ JRNYS  │ │ SYSTEM │ │ IBILITY│
└────┬───┘ └───┬────┘ └───┬────┘
     │         │          │
     └─────────┴──────────┘
               │
               ▼
      ┌─────────────────┐
      │ 6. ARCHITECTURE │
      │    BLUEPRINT    │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 7. AGENT        │
      │    PROMPTS      │
      └────────┬────────┘
               │
    ┌──────────┴──────────┐ (--deep flag)
    │                     │
    ▼                     ▼
┌────────┐ ┌────────┐ ┌────────┐
│PIPELINE│ │RESILI- │ │ IMPL   │
│  ORCH  │ │  ENCE  │ │SCAFFOLD│
└────────┘ └────────┘ └────────┘
┌────────┐ ┌────────┐ ┌────────┐
│OBSERVE │ │TESTING │ │HANDOFF │
│ ILITY  │ │STRATEGY│ │PROTOCOL│
└────────┘ └────────┘ └────────┘
               │
               ▼
      ┌─────────────────┐
      │ 8. SECURITY     │
      │    ARCHITECTURE │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 9. ADR          │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 10. POST        │
      │     DEPLOYMENT  │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 11. STRATEGIC   │
      │     RECOMMEND   │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 12. ACTION      │
      │     ROADMAP     │
      └────────┬────────┘
               │
               ▼
      ┌─────────────────┐
      │ 13. INDEX       │
      └─────────────────┘
```

---

## Data Contracts

### INPUTS → BRAND_GUIDELINES

| Input Field | Output Field |
|-------------|--------------|
| `application_name` | `product_name` |
| `one_line_description` | `positioning.tagline` |
| `target_users` | `positioning.target_audience` |
| `core_problem` | `beliefs[]` (derived) |
| `differentiator` | `positioning.differentiator` |
| `anti_goals` | `kill_list[]` |

### BRAND_GUIDELINES → NORTHSTAR

| Source | Target |
|--------|--------|
| `product_name` | Used throughout |
| `beliefs[]` | Informs `axioms[]` |
| `kill_list[]` | Informs `non_goals[]` |
| `positioning.target_audience` | Informs `personas[]` |

### BRAND + NORTHSTAR → COMPETITIVE_LANDSCAPE

| Source | Target |
|--------|--------|
| `brand.positioning` | Research queries |
| `northstar.personas` | Competitor user analysis |
| `northstar.scope` | Feature comparison scope |

### Phases 1-3 → NORTHSTAR_EXTRACT

| Source | Target |
|--------|--------|
| `brand.beliefs[]` | `axioms[]` (synthesized) |
| `brand.kill_list[]` | `non_goals[]` |
| `northstar.scope.out_of_scope` | `non_goals[]` |
| `competitive.differentiation` | `constraints[]` |

### Phase 4 (+ Phase 5 if --ux) → ARCHITECTURE_BLUEPRINT

| Source | Target |
|--------|--------|
| `extract.axioms[]` | Design principles |
| `extract.constraints[]` | Tech constraints |
| `journeys.metrics[]` | `latency_budget` (--ux) |
| `journeys.error_recovery` | `error_handling` (--ux) |
| `journeys.first_time_user` | `activation_flow` (--ux) |

### ARCHITECTURE → AGENT_PROMPTS

| Source | Target |
|--------|--------|
| `architecture.agents[]` | Agent list |
| `architecture.orchestration_pattern` | Coordination style |
| `architecture.tools_per_agent` | Tool access |
| `architecture.data_flow` | Handoff protocols |

### ARCHITECTURE + AGENTS → SECURITY

| Source | Target |
|--------|--------|
| `architecture.tech_stack` | Attack surface |
| `agents[].tools` | Permission model |
| `architecture.data_flow` | Data classification |
| `extract.constraints[]` | Regulatory requirements |

### Phases 6-8 → ADR

| Source | Target |
|--------|--------|
| `architecture.*` | Decision records |
| `security.*` | Security decisions |
| `agents.*` | Agent design decisions |

### Phases 6-9 → POST_DEPLOYMENT

| Source | Target |
|--------|--------|
| `architecture.tech_stack` | Monitoring tools |
| `security.incident_response` | Runbook |
| `agents[].observability` | Agent metrics |

### All Phases → STRATEGIC_RECOMMENDATION

| Source | Target |
|--------|--------|
| `brand.*` | Strategic context |
| `northstar.metric` | Success criteria |
| `competitive.*` | Market position |
| `architecture.*` | Technical feasibility |
| `security.*` | Risk assessment |

### STRATEGIC → ACTION_ROADMAP

| Source | Target |
|--------|--------|
| `strategic.recommended_path` | Roadmap structure |
| `strategic.priorities` | Action sequence |
| `strategic.dependencies` | Milestone deps |

### All → INDEX

| Source | Target |
|--------|--------|
| All documents | Document index |
| All cross-refs | Navigation |
| All summaries | Quick reference |

---

## Cross-Reference Syntax

### In Templates

```markdown
<!-- Reference a specific field -->
The product name is {{ref:brand.product_name}}.

<!-- Reference a list -->
Our core beliefs:
{{ref:brand.beliefs[]}}

<!-- Reference with fallback -->
Target users: {{ref:northstar.personas[0].name|"Primary User"}}
```

### In Outputs (YAML)

```yaml
# outputs/brand.yml
product_name: "North Star Advisor"
beliefs:
  - "Strategy before code"
  - "Documents are executable context"
kill_list:
  - "Real-time collaboration (v1)"
  - "Custom template editor (v1)"
```

### Resolution Priority

1. `inputs.yml` (user inputs)
2. `outputs/{document}.yml` (generated outputs)
3. Source document (full parse)
4. Default/fallback value
5. Error if required

---

## Cascading Updates

When an upstream document changes, downstream documents may need regeneration.

### Change Impact Matrix

| Changed Document | Regenerate |
|------------------|------------|
| BRAND_GUIDELINES | All downstream |
| NORTHSTAR | Phases 3+ |
| COMPETITIVE_LANDSCAPE | Phases 4+ |
| ARCHITECTURE_BLUEPRINT | Phases 7+ |
| AGENT_PROMPTS | Phases 8+ |

### Update Strategy

1. Detect change in upstream document
2. Invalidate downstream outputs
3. Mark affected phases as "stale"
4. Prompt user to regenerate
5. Or auto-regenerate with `--cascade` flag

---

## Output Extraction

Each phase extracts key data to `outputs/` for efficient cross-referencing.

### Extraction Rules

| Document | Extracted Fields |
|----------|------------------|
| BRAND_GUIDELINES | `product_name`, `beliefs[]`, `kill_list[]` |
| NORTHSTAR | `metric`, `measurement`, `personas[]`, `phases[]` |
| COMPETITIVE_LANDSCAPE | `competitors[]`, `differentiation[]` |
| ARCHITECTURE_BLUEPRINT | `pattern`, `agents[]`, `tech_stack` |
| AGENT_PROMPTS | `agents[].system_prompt`, `agents[].tools[]` |
| SECURITY_ARCHITECTURE | `auth_pattern`, `threats[]`, `guardrails[]` |

---

## Validation Points

Cross-references are validated at:

1. **Generation time:** Resolve all refs before writing
2. **Validation gate:** Check refs exist and are non-empty
3. **Export time:** Verify all links in INDEX resolve
4. **Sync time:** Ensure exported data matches source
