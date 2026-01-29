# [PROJECT NAME]: Architecture Decision Records

<!-- GENERATION: This is Step 9 of 13. Accumulates decisions from all prior steps. See GENERATION_MANIFEST.md -->

> **Parent**: [INDEX.md](INDEX.md)
> **Format**: ADR-NNNN (sequential, never reused)
> **Generation Step**: 9 of 13 — Record decisions made during generation of Steps 1-8

Architecture decisions that shape your agentic system.

---

## How to Use

Record decisions when you:
- Choose between competing approaches
- Reject a popular pattern for good reasons
- Make trade-offs that future developers need to understand

**Keep it brief.** A good ADR fits on one screen.

---

## Template

```
================================================================================
ADR-NNNN: [Decision Title]
================================================================================
Date: [YYYY-MM-DD]
Status: [Proposed | Accepted | Deprecated | Superseded by ADR-NNNN]

## Context
What situation prompted this decision?

## Decision
What did we decide to do?

## Alternatives Rejected
What else did we consider? Why didn't we choose it?

## Consequences
### Positive
- [Benefit 1]
- [Benefit 2]

### Trade-offs
- [What we're accepting]
```

---

## Example

```
================================================================================
ADR-0001: Safety Agent Receives Raw Input Only
================================================================================
Date: 2025-01-15
Status: Accepted

## Context
Our safety monitor could receive either raw user input or filtered/summarized
input from other agents. We needed to decide which approach ensures reliable
crisis detection.

## Decision
Safety agent receives raw user input directly, not filtered outputs from
other agents.

## Alternatives Rejected
1. **Filtered input from mood sensor** — Rejected: creates confirmation bias
2. **Summarized context from memory agent** — Rejected: loses critical signals

## Consequences
### Positive
- Independent safety assessment (no upstream filtering)
- Higher recall on crisis signals
- Simpler debugging (safety sees exactly what user said)

### Trade-offs
- Slight duplication (safety re-analyzes what others also see)
- Can't benefit from other agents' context
```

---

## ADR Index

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| 0001 | [Title] | [Status] | [Date] |

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Create ADR entries as you make significant decisions
2. Use consistent numbering (0001, 0002, etc.)
3. Include rejected alternatives with rationale
4. Update status when decisions are superseded
5. Link to related docs and code

---

## Validation Schema (For AI Generation)

```yaml
# ADR validation gate
inputs_required:
  - all_previous_docs: decisions_made_during_generation
  - arch.agent_topology[]: from_architecture_blueprint
  - extract.axioms[]: from_northstar_extract

outputs_produced:
  - adr.decisions[]: used_by_future_planning
  - adr.index: used_by_documentation

validation_gate:
  required_sections:
    - "ADR Index"
    - at_least_one_adr_entry

  minimum_content:
    adrs: 1
    adrs[].context: not_empty
    adrs[].decision: not_empty
    adrs[].consequences: not_empty

  placeholders_filled:
    - "[Title]"
    - "[Status]"
    - "[Date]"

quality_checks:
  - each_adr: has_context_decision_alternatives_consequences
  - alternatives: explain_why_rejected
  - consequences: include_tradeoffs

decision_prompts:
  - "What significant decisions were made during planning?"
  - "What alternatives were considered and rejected?"
  - "What are the trade-offs of each major decision?"
```

**After generation, verify:**
- [ ] At least one ADR entry exists
- [ ] Each ADR has Context, Decision, Alternatives, Consequences
- [ ] ADR index is up to date
- [ ] Significant architecture and strategy decisions are captured

---

© [YEAR] [AUTHOR/ORG]
