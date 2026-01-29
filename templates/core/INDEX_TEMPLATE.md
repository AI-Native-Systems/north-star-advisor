# [PROJECT NAME]: Documentation Index

<!-- GENERATION: This is Step 13 of 13. Generate LAST after all other documents. See GENERATION_MANIFEST.md -->

> **Last Updated**: [DATE]
> **Purpose**: Single source of truth for documentation structure and hierarchy
> **Generation Step**: 13 of 13 — Generate LAST, references all other documents

---

## Document Hierarchy

```
┌───────────────────────────────────────────────────────────────────────────┐
│                      [PROJECT NAME] DOCUMENTATION                         │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  TIER 1: STRATEGIC AUTHORITY                                              │
│  ───────────────────────────                                              │
│  The source of truth for product direction. All other docs defer here.   │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐      │
│  │  NORTHSTAR.md                                                   │      │
│  │  • North Star metric                                            │      │
│  │  • Target users & personas                                      │      │
│  │  • Phase boundaries (MVP → Growth)                              │      │
│  │  • What we build / explicitly don't build                       │      │
│  │  • Success metrics & validation gates                           │      │
│  └─────────────────────────────────────────────────────────────────┘      │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐      │
│  │  COMPETITIVE_LANDSCAPE.md                                       │      │
│  │  • Market shifts & timing windows                               │      │
│  │  • Direct & indirect competitors                                │      │
│  │  • Strategic whitespace                                         │      │
│  │  • Offensive & defensive moves                                  │      │
│  └─────────────────────────────────────────────────────────────────┘      │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐      │
│  │  NORTHSTAR_EXTRACT.md (Design DNA)                              │      │
│  │  • Core axioms (immutable constraints)                          │      │
│  │  • Explicit non-goals (kill list)                               │      │
│  │  • Structural patterns (reusable motifs)                        │      │
│  │  • Language invariants (what to always/never do)                │      │
│  │  • Re-evaluation triggers                                       │      │
│  └─────────────────────────────────────────────────────────────────┘      │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐      │
│  │  STRATEGIC_RECOMMENDATION.md                                    │      │
│  │  • Strategic paths with trade-offs                              │      │
│  │  • The recommendation (with reasoning)                          │      │
│  │  • Focus areas & avoid list                                     │      │
│  │  • Confidence assessment & review triggers                      │      │
│  └─────────────────────────────────────────────────────────────────┘      │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐      │
│  │  ACTION_ROADMAP.md                                              │      │
│  │  • 30-day focus areas & avoid list                              │      │
│  │  • 90-day milestones                                            │      │
│  │  • Success criteria & risks                                     │      │
│  │  • Review triggers                                              │      │
│  └─────────────────────────────────────────────────────────────────┘      │
│                              │                                            │
│                              ▼                                            │
│  TIER 2: IMPLEMENTATION                                                   │
│  ──────────────────────                                                   │
│  How we build what the North Star defines.                                │
│                                                                           │
│  ┌───────────────────────────────────┐  ┌──────────────────────────────┐  │
│  │  ARCHITECTURE_BLUEPRINT.md        │  │  SECURITY_ARCHITECTURE.md    │  │
│  │  (with architecture/ modules)     │  │                              │  │
│  │  • Agent topology                 │  │  • Threat model              │  │
│  │  • Pipeline orchestration         │  │  • Authentication/authz      │  │
│  │  • State schema                   │  │  • Trust boundaries          │  │
│  │  • Resilience patterns            │  │  • Audit & incident response │  │
│  │  • Observability                  │  │  • Security checklist        │  │
│  └───────────────────────────────────┘  └──────────────────────────────┘  │
│                                                                           │
│  ┌───────────────────────────────────────────────────────────────────┐    │
│  │  Design Patterns (design/)                                        │    │
│  │                                                                   │    │
│  │  • UI_DESIGN_SYSTEM.md — Design tokens, components, colors        │    │
│  │  • USER_JOURNEYS.md — Emotional journey maps, friction points     │    │
│  │  • ACCESSIBILITY.md — WCAG 2.1 AA, screen readers, testing        │    │
│  └───────────────────────────────────────────────────────────────────┘    │
│                              │                                            │
│                              ▼                                            │
│  TIER 3: SUPPORTING                                                       │
│  ──────────────────                                                       │
│  Brand, methodology, and optimization.                                    │
│                                                                           │
│  ┌──────────────────────────────┐  ┌──────────────────────────────┐       │
│  │  BRAND_GUIDELINES.md         │  │  [OTHER_DOCS].md             │       │
│  │                              │  │                              │       │
│  │  • Brand identity            │  │  • [Additional docs as       │       │
│  │  • Voice and tone            │  │    needed for your project]  │       │
│  │  • Visual standards          │  │                              │       │
│  │  • Anti-patterns             │  │                              │       │
│  └──────────────────────────────┘  └──────────────────────────────┘       │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

---

## Architecture Blueprint Modules

The ARCHITECTURE_BLUEPRINT.md references focused modules in `north-star-advisor/docs/architecture/`:

```
ARCHITECTURE_BLUEPRINT.md (Core)
    │
    ├── architecture/PIPELINE_ORCHESTRATION.md
    │   • State schema
    │   • Pipeline orchestrator implementation
    │   • Execution patterns
    │   • Self-correction patterns
    │
    ├── architecture/RESILIENCE_PATTERNS.md
    │   • Circuit breaker configuration
    │   • Fallback chains per agent
    │   • Timeout handling
    │   • Idempotency patterns
    │
    ├── architecture/AGENT_PROMPTS.md
    │   • System prompts for all agents
    │   • JTBD framework integration
    │   • Few-shot examples
    │   • Edge case handling
    │
    ├── architecture/IMPLEMENTATION_SCAFFOLD.md
    │   • Directory structure
    │   • BaseAgent class
    │   • Agent implementations
    │   • API routes
    │
    ├── architecture/OBSERVABILITY.md
    │   • Trace architecture
    │   • Tracing handlers
    │   • PII sanitization
    │   • North Star instrumentation
    │
    └── architecture/TESTING_STRATEGY.md
        • Test categories and coverage
        • Test configuration
        • Golden datasets
        • Integration tests
```

---

## Design Documents (--ux flag)

Human-facing design patterns in `north-star-advisor/docs/design/`:

```
BRAND_GUIDELINES.md (Identity)
    │
    ├── design/UI_DESIGN_SYSTEM.md
    │   • Design tokens (CSS variables)
    │   • Component specifications
    │   • Color system (WCAG AA)
    │   • Typography scale
    │
    ├── design/USER_JOURNEYS.md
    │   • First-time user journey
    │   • Returning user patterns
    │   • Error recovery flows
    │   • Emotional state mapping
    │
    └── design/ACCESSIBILITY.md
        • WCAG 2.1 AA compliance checklist
        • Screen reader patterns for streaming UI
        • Keyboard navigation & focus management
        • Cognitive load mitigation
        • Accessibility testing protocol
```

---

## Quick Reference

| Document | Tier | Purpose | When to Use |
|----------|------|---------|-------------|
| [NORTHSTAR.md](NORTHSTAR.md) | 1 | Strategic direction | Product decisions, prioritization, metrics |
| [COMPETITIVE_LANDSCAPE.md](COMPETITIVE_LANDSCAPE.md) | 1 | Market intelligence | Competitive positioning, strategic moves |
| [NORTHSTAR_EXTRACT.md](NORTHSTAR_EXTRACT.md) | 1 | Design DNA | Start of planning sessions, prevent re-litigating |
| [STRATEGIC_RECOMMENDATION.md](STRATEGIC_RECOMMENDATION.md) | 1 | Strategic decision | Path selection, trade-off evaluation |
| [ACTION_ROADMAP.md](ACTION_ROADMAP.md) | 1 | 30-90 day plan | Sprint planning, focus alignment |
| [ARCHITECTURE_BLUEPRINT.md](ARCHITECTURE_BLUEPRINT.md) | 2 | Technical implementation | Building agents, orchestration, resilience |
| ↳ [architecture/PIPELINE_ORCHESTRATION.md](architecture/PIPELINE_ORCHESTRATION.md) | 2 | Pipeline details | State schema, orchestrator, patterns |
| ↳ [architecture/RESILIENCE_PATTERNS.md](architecture/RESILIENCE_PATTERNS.md) | 2 | Failure handling | Circuit breakers, fallbacks, timeouts |
| ↳ [architecture/AGENT_PROMPTS.md](architecture/AGENT_PROMPTS.md) | 2 | Agent contracts | System prompts, examples |
| ↳ [architecture/IMPLEMENTATION_SCAFFOLD.md](architecture/IMPLEMENTATION_SCAFFOLD.md) | 2 | Code structure | Directory layout, base classes |
| ↳ [architecture/OBSERVABILITY.md](architecture/OBSERVABILITY.md) | 2 | Tracing & metrics | Instrumentation, North Star |
| ↳ [architecture/TESTING_STRATEGY.md](architecture/TESTING_STRATEGY.md) | 2 | Test patterns | Test config, golden datasets |
| [SECURITY_ARCHITECTURE.md](SECURITY_ARCHITECTURE.md) | 2 | Security patterns | Auth, threats, compliance |
| [BRAND_GUIDELINES.md](BRAND_GUIDELINES.md) | 3 | Brand identity | Voice, tone, visual standards |
| [ADR.md](ADR.md) | 2 | Decision records | Architecture decisions, trade-offs |
| [OPERATIONS_RUNBOOK.md](OPERATIONS_RUNBOOK.md) | 2 | Operations | Health checks, incidents, scaling |
| [POST_DEPLOYMENT.md](POST_DEPLOYMENT.md) | 2 | Post-deployment | Monitoring, iteration, maintenance |
| [design/UI_DESIGN_SYSTEM.md](design/UI_DESIGN_SYSTEM.md) | 2 | Design tokens | CSS variables, components, colors |
| [design/USER_JOURNEYS.md](design/USER_JOURNEYS.md) | 2 | User experience | Journey mapping, friction points |
| [design/ACCESSIBILITY.md](design/ACCESSIBILITY.md) | 2 | Inclusive design | WCAG 2.1 AA, screen readers, testing |

---

## Decision Authority

When documents conflict, defer to lower tier number:

```
Tier 1 (NORTHSTAR) > Tier 2 (Blueprint/Security) > Tier 3 (Brand/Supporting)
```

**Example**: If NORTHSTAR says "optimize for outcomes over engagement" and another doc suggests engagement metrics, NORTHSTAR wins.

---

## Document Dependencies

```
BRAND_GUIDELINES.md (Identity - Step 1)
    │
    └──► NORTHSTAR.md (Strategic Authority - Step 2)
         │
         ├──► COMPETITIVE_LANDSCAPE.md (Market Intelligence - Step 3)
         │
         ├──► NORTHSTAR_EXTRACT.md (Design DNA - Step 4)
         │
         ├──► design/*.md (User Experience - Step 5, with --ux flag)
         │    USER_JOURNEYS → UI_DESIGN_SYSTEM, ACCESSIBILITY
         │
         ├──► ARCHITECTURE_BLUEPRINT.md (Implementation - Step 6)
         │    │
         │    └──► architecture/*.md (modular implementation details)
         │
         ├──► SECURITY_ARCHITECTURE.md (Security - Step 8)
         │
         ├──► POST_DEPLOYMENT.md (Operations - Step 10)
         │
         ├──► STRATEGIC_RECOMMENDATION.md (Decision Synthesis - Step 11)
         │    │
         │    └──► ACTION_ROADMAP.md (30-90 Day Plan - Step 12)
         │
         └──► INDEX.md (Documentation Hub - Step 13)
```

---

## Current State

| Document | Status | Last Updated |
|----------|--------|--------------|
| BRAND_GUIDELINES.md | [Draft/Active] | [Date] |
| NORTHSTAR.md | [Draft/Active] | [Date] |
| COMPETITIVE_LANDSCAPE.md | [Draft/Active] | [Date] |
| NORTHSTAR_EXTRACT.md | [Draft/Active] | [Date] |
| ARCHITECTURE_BLUEPRINT.md | [Draft/Active] | [Date] |
| ↳ architecture/PIPELINE_ORCHESTRATION.md | [Draft/Active] | [Date] |
| ↳ architecture/RESILIENCE_PATTERNS.md | [Draft/Active] | [Date] |
| ↳ architecture/AGENT_PROMPTS.md | [Draft/Active] | [Date] |
| ↳ architecture/IMPLEMENTATION_SCAFFOLD.md | [Draft/Active] | [Date] |
| ↳ architecture/OBSERVABILITY.md | [Draft/Active] | [Date] |
| ↳ architecture/TESTING_STRATEGY.md | [Draft/Active] | [Date] |
| SECURITY_ARCHITECTURE.md | [Draft/Active] | [Date] |
| ADR.md | [Draft/Active] | [Date] |
| POST_DEPLOYMENT.md | [Draft/Active] | [Date] |
| STRATEGIC_RECOMMENDATION.md | [Draft/Active] | [Date] |
| ACTION_ROADMAP.md | [Draft/Active] | [Date] |
| OPERATIONS_RUNBOOK.md | [Draft/Active] | [Date] |
| design/UI_DESIGN_SYSTEM.md | [Draft/Active] | [Date] |
| design/USER_JOURNEYS.md | [Draft/Active] | [Date] |
| design/ACCESSIBILITY.md | [Draft/Active] | [Date] |

---

## Contributing to Documentation

When adding or modifying documentation:

1. **Check tier placement** — Does this belong in an existing doc or need a new one?
2. **Maintain hierarchy** — Strategic docs shouldn't contain implementation details
3. **Use architecture/ modules** — For implementation details, add to appropriate module
4. **Cross-reference** — Link to related docs using relative paths
5. **Update this index** — Keep the hierarchy current

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace `[PROJECT NAME]` with your project name
2. Update the document list to match your actual files
3. Add any project-specific docs to the hierarchy
4. Set status for each document (Draft/Active/Archived)
5. Update dates as you modify documents

**Why an index matters:**

- New contributors know where to find information
- Prevents duplicate documentation
- Establishes clear authority hierarchy
- Makes cross-referencing consistent

---

*[PROJECT NAME] Documentation Structure*

---

© [YEAR] [AUTHOR/ORG]
