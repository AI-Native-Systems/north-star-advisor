# [PROJECT NAME]: North Star Extract

<!-- GENERATION: This is Step 4 of 13. Requires outputs from BRAND_GUIDELINES, NORTHSTAR, and COMPETITIVE_LANDSCAPE. See GENERATION_MANIFEST.md -->

> Your project's design DNA — the decisions that should NOT be re-litigated.
> **Generation Step**: 4 of 13 — Requires `brand.beliefs[]`, `northstar.metric`, `northstar.kill_list[]`, `competitive.rejected_moves[]`

**Project:** [Your Project Name]
**Created:** [Date]
**Last Updated:** [Date]

---

## How to Use This Document

**Read this first** when:
- Starting a planning session
- Onboarding new team members
- Proposing new features
- Making architecture decisions
- Responding to incidents
- Planning maintenance work

**This document captures:**
- Decisions that are settled
- Paths we've explicitly rejected
- Patterns that repeat across the system
- Behaviors that must remain consistent

**For the full story** of how these decisions were made, see [ADR.md](ADR.md).

If a proposal conflicts with this document, it needs overwhelming evidence to proceed.

---

## Core Axioms

These constraints govern every major decision. They are non-negotiable.

| Axiom | What It Means |
|-------|---------------|
| **[Value A] > [Value B]** | When these conflict, choose A. |
| **[Axiom 2]** | [Implication for design decisions] |
| **[Axiom 3]** | [Implication for design decisions] |

**How to write good axioms:**
- Make them actionable: "User safety > feature velocity" tells you what to do when they conflict
- Make them specific to your project, not generic
- 3-5 axioms is enough; more becomes noise

**Questions that surface axioms:**
- What metric do we optimize for? What metric do we reject?
- What happens when the system fails?
- What does success look like for the user, not the product?
- What are we honest about?

---

## Explicit Non-Goals

These paths are explicitly rejected. This prevents scope creep and stops reopening closed doors.

### Features We Will Never Build

| Feature | Why We Reject It |
|---------|------------------|
| [Feature 1] | [How it conflicts with core axioms] |
| [Feature 2] | [How it conflicts with core axioms] |
| [Feature 3] | [How it conflicts with core axioms] |

### Technical Approaches We Rejected

| Approach | Why Rejected | What We Do Instead |
|----------|--------------|---------------------|
| [Approach 1] | [Rationale] | [Alternative] |
| [Approach 2] | [Rationale] | [Alternative] |

**Why this matters:** Every "no" in this list is a "yes" to focus. Features on this list don't get discussed again without new evidence.

---

## Structural Patterns

These patterns repeat across the architecture. They are reusable motifs.

### [Pattern Name]

```
[Diagram or description]
```

**When to use:** [Context]
**Why it exists:** [Rationale]

### Fallback Chain

When things fail, degrade gracefully:

```
Primary Model → Simpler Model → Template → Cached Response → Apologetic Fallback
```

- Partial response is better than timeout
- Template fallback is better than failure
- Something is better than nothing

### Conflict Resolution Hierarchy

When parallel components disagree:

```
Safety > Severity > [Domain-specific] > Default
```

Always resolve in favor of the safer option.

---

## What We Always Do

Behaviors that must remain consistent.

| Behavior | Example |
|----------|---------|
| [Invariant 1] | [Concrete example] |
| [Invariant 2] | [Concrete example] |
| [Invariant 3] | [Concrete example] |

---

## What We Never Do

Behaviors that are explicitly prohibited.

| Behavior | Why |
|----------|-----|
| [Anti-behavior 1] | [Rationale] |
| [Anti-behavior 2] | [Rationale] |
| [Anti-behavior 3] | [Rationale] |

---

## When to Re-evaluate

These signals justify revisiting core assumptions.

### Metric Triggers

| Signal | Threshold | What to Do |
|--------|-----------|------------|
| [Core metric] drops | Below X% for 3 cohorts | Reposition or pivot |
| [Feature] rated unhelpful | Above X% for 4 weeks | Simplify or remove |
| Safety incident | Any critical | Pause development |

### External Triggers

| Event | What to Reconsider |
|-------|-------------------|
| [External change 1] | [Scope of re-evaluation] |
| [External change 2] | [Scope of re-evaluation] |
| Regulatory changes | Compliance and ethics |

### Strategic Triggers

| Trigger | Questions to Ask |
|---------|------------------|
| Scale exceeds [threshold] | Do we need different infrastructure? |
| Cost becomes prohibitive | What can we optimize? |
| Community requests rejected feature | Re-examine with evidence, but default to "no" |

---

## Document Governance

| Aspect | Policy |
|--------|--------|
| **Who can modify** | Core Axioms require team consensus; other sections need maintainer approval |
| **Review cadence** | Quarterly, or after major pivots |
| **Conflict resolution** | This document wins over implementation docs |

---

## Template Notes

Delete this section after filling out the template.

**To complete this document:**

1. Fill in your project name and dates
2. Write 3-5 core axioms based on your Phase 1 and 2 work
3. List features and approaches you've explicitly rejected
4. Document patterns that repeat in your architecture
5. Define behaviors that must remain consistent
6. Set thresholds for re-evaluation

**Common mistakes:**
- Too many axioms (stick to 3-5)
- Axioms that are too generic ("build good software")
- Empty non-goals section (if you haven't rejected anything, you haven't made real decisions)
- Forgetting to update when assumptions change

---

## Validation Schema (For AI Generation)

```yaml
# NORTHSTAR_EXTRACT validation gate
inputs_required:
  - brand.beliefs[]: from_brand_guidelines
  - northstar.metric: from_northstar
  - northstar.kill_list[]: from_northstar
  - northstar.phases[]: from_northstar

outputs_produced:
  - extract.axioms[]: used_by_all_agents_and_decisions
  - extract.non_goals[]: used_by_security_architecture
  - extract.patterns[]: used_by_resilience_patterns
  - extract.always[]: used_by_agent_prompts
  - extract.never[]: used_by_agent_prompts

validation_gate:
  required_sections:
    - "Core Axioms"
    - "Explicit Non-Goals"
    - "Structural Patterns"
    - "What We Always Do"
    - "What We Never Do"
    - "When to Re-evaluate"

  minimum_content:
    axioms: 3
    axioms_max: 5
    non_goals.features: 3
    non_goals.approaches: 2
    patterns: 1
    always: 2
    never: 2
    re_evaluation_triggers: 2

  placeholders_filled:
    - "[Your Project Name]"
    - "[Value A]"
    - "[Value B]"

cross_references:
  - axioms: must_derive_from: brand.beliefs
  - non_goals.features: must_include: northstar.kill_list
  - patterns: must_include: "Fallback Chain"

quality_checks:
  - each_axiom: actionable_when_conflict
  - each_non_goal: has_rationale
  - each_pattern: has_when_to_use
  - each_trigger: has_threshold_and_action

decision_prompts:
  - "When two good ideas conflict, which value wins? (e.g., safety vs speed)"
  - "What re-evaluation triggers should pause development?"
  - "What patterns repeat in your mental model of this system?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] 3-5 axioms (not more, not less)
- [ ] Each axiom is actionable (tells you what to do when values conflict)
- [ ] Non-goals include all items from NORTHSTAR kill list
- [ ] At least one structural pattern with "when to use"
- [ ] "Always" and "Never" sections have concrete examples
- [ ] Re-evaluation triggers have specific thresholds

---

© [YEAR] [AUTHOR/ORG]
