# [PROJECT NAME]: North Star Specification

<!-- GENERATION: This is Step 2 of 13. Requires outputs from BRAND_GUIDELINES. See GENERATION_MANIFEST.md -->

> **Tier**: 1 — Strategic Authority
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 2 of 13 — Requires `brand.beliefs[]` and `brand.kill_list[]` from Step 1

---

## Document Purpose

This specification defines **what [PROJECT NAME] must achieve** and **how we measure success**. Implementation details exist in supporting documents; this document answers:

1. What is our North Star metric?
2. Who are we building for?
3. What differentiates us?
4. What is in scope for each phase?
5. How do we know we're winning?

**Decision Rule**: If a feature, architecture choice, or initiative doesn't measurably improve the North Star metric, it doesn't ship.

---

# Part 1: Strategic Foundation

## 1.1 North Star Metric

### The Metric

> **[Your North Star Metric Name]**

**Definition**: [One sentence definition of what this metric measures]

### Why This Metric

| Criterion | How Your Metric Satisfies It |
|-----------|------------------------------|
| **Leading** | [How it predicts future outcomes] |
| **Actionable** | [How teams can directly influence it] |
| **Customer-centric** | [How it measures value delivered, not extracted] |
| **Understandable** | [How anyone can explain it] |

### What This Metric Rejects

| Anti-Metric | Why We Reject It |
|-------------|------------------|
| [Metric 1] | [Why it's harmful or misleading] |
| [Metric 2] | [Why it's harmful or misleading] |
| [Metric 3] | [Why it's harmful or misleading] |

---

## 1.2 Input Metrics Hierarchy

The North Star decomposes into input metrics that teams can directly influence:

```
                         NORTH STAR
                    [Your North Star Metric]
                        Target: [X]%
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
         ▼                   ▼                   ▼
   ┌───────────┐      ┌───────────┐      ┌───────────┐
   │ [INPUT 1] │      │ [INPUT 2] │      │ [INPUT 3] │
   │           │      │           │      │           │
   │ Target: X │      │ Target: X │      │ Target: X │
   └───────────┘      └───────────┘      └───────────┘
```

### Input Metrics Definitions

| Metric | Definition | Target | Owner |
|--------|------------|--------|-------|
| [Metric 1] | [What it measures] | [Target] | [Team] |
| [Metric 2] | [What it measures] | [Target] | [Team] |
| [Metric 3] | [What it measures] | [Target] | [Team] |

### Metrics We Track But Don't Optimize

| Metric | Why Track | Why Not Optimize |
|--------|-----------|------------------|
| [Metric] | [Diagnostic value] | [Potential harm if optimized] |

---

## 1.3 Positioning

### Positioning Statement

> **[PROJECT NAME] is a [category]** that [key benefit] for [target customers] who [situation/need]. Unlike [alternatives], we [unique differentiator].

### What Makes Us Different

| Dimension | Alternatives | Our Approach |
|-----------|--------------|--------------|
| [Dimension 1] | [How others do it] | [How we do it differently] |
| [Dimension 2] | [How others do it] | [How we do it differently] |
| [Dimension 3] | [How others do it] | [How we do it differently] |

### Value Proposition

| User Need | Current Pain | Our Solution |
|-----------|--------------|--------------|
| [Need 1] | [What's broken today] | [How we solve it] |
| [Need 2] | [What's broken today] | [How we solve it] |
| [Need 3] | [What's broken today] | [How we solve it] |

---

## 1.4 Target Users

### Primary Persona: [Name]

```
"[Quote that captures their core need]"

Demographics: [Age, role, context]
Current State: [How they handle this today]
Access Barriers: [What prevents them from solving this]

Functional Jobs:
• [What they need to accomplish]
• [What they need to accomplish]

Emotional Jobs:
• [How they need to feel]
• [How they need to feel]

Social Jobs:
• [How they need to appear to others]
• [How they need to appear to others]

Success Signals:
• "[Quote they'd say if the product worked]"
• "[Quote they'd say if the product worked]"
```

### Secondary Persona: [Name]

```
"[Quote that captures their core need]"

Demographics: [Age, role, context]
Current State: [How they handle this today]
Access Barriers: [What prevents them from solving this]

[Same structure as primary persona]
```

---

## 1.5 Forces of Progress Analysis

Understanding what drives users toward your product and what holds them back.

### Push Forces (Away from Current State)

| Force | Evidence | Strength |
|-------|----------|----------|
| [Pain point 1] | [How you know this is real] | Strong/Medium/Weak |
| [Pain point 2] | [How you know this is real] | Strong/Medium/Weak |

### Pull Forces (Toward Your Product)

| Force | Promise | Evidence |
|-------|---------|----------|
| [Attraction 1] | [What you offer] | [How you'll prove it] |
| [Attraction 2] | [What you offer] | [How you'll prove it] |

### Anxiety of Change

| Concern | User Verbalization | Mitigation |
|---------|-------------------|------------|
| [Fear 1] | "[What they'd say]" | [How you address it] |
| [Fear 2] | "[What they'd say]" | [How you address it] |

### Habit of the Present

| Current Habit | Switching Cost | Strategy |
|---------------|----------------|----------|
| [Existing behavior] | [What they'd lose] | [How you overcome it] |

---

# Part 2: Scope Definition

## 2.1 Phase Boundaries

### Phase 1: MVP

**Theme**: "[3-5 word theme]"

**Objective**: [One sentence objective]

**In Scope**:

| Feature | Acceptance Criteria | Why Essential |
|---------|---------------------|---------------|
| [Feature 1] | [How you know it's done] | [Why it can't be cut] |
| [Feature 2] | [How you know it's done] | [Why it can't be cut] |

**Out of Scope for Phase 1**:

| Feature | Why Deferred |
|---------|--------------|
| [Feature] | [Rationale] |

**Phase 1 Success Criteria**:

| Metric | Target | Kill Threshold |
|--------|--------|----------------|
| [Metric 1] | [Target] | [Below this, pivot] |
| [Metric 2] | [Target] | [Below this, pivot] |

---

### Phase 2: [Name]

**Theme**: "[3-5 word theme]"

**Objective**: [One sentence objective]

**Unlocked By**: Phase 1 success criteria met

**In Scope**:

| Feature | Acceptance Criteria | Why Now |
|---------|---------------------|---------|
| [Feature 1] | [How you know it's done] | [Why this phase] |

**Phase 2 Success Criteria**:

| Metric | Target | Kill Threshold |
|--------|--------|----------------|
| [Metric] | [Target] | [Below this, pivot] |

---

### Phase 3: [Name]

**Theme**: "[3-5 word theme]"

**Objective**: [One sentence objective]

**Unlocked By**: Phase 2 success criteria met

[Same structure as Phase 2]

---

## 2.2 Explicit Kill List (Never Build)

These features are explicitly out of scope regardless of phase. They represent scope creep, premature optimization, or strategic misalignment.

| Feature | Rationale for Rejection |
|---------|------------------------|
| [Feature 1] | [Why this conflicts with strategy] |
| [Feature 2] | [Why this conflicts with strategy] |
| [Feature 3] | [Why this conflicts with strategy] |

---

## 2.3 Licensing & Ethics

### License Choice

**License**: [MIT | Apache 2.0 | Hippocratic | GPL | Proprietary]

**Rationale**: [Why this license fits your project]

### Ethical Constraints

| Constraint Type | Specification |
|-----------------|---------------|
| **Prohibited Uses** | [What the system can NEVER be used for] |
| **Required Behaviors** | [What the system must ALWAYS do] |
| **Data Principles** | [How user data is handled] |

---

# Part 3: Success Measurement

## 3.1 Metrics Dashboard

### North Star (Weekly Review)

```
┌─────────────────────────────────────────────────────────────────────┐
│  NORTH STAR: [Your Metric Name]                                     │
│                                                                     │
│  Current: ████████████░░░░░░░░ [X]%    Target: [Y]%    Trend: ↑↓   │
└─────────────────────────────────────────────────────────────────────┘
```

### Input Metrics (Daily Review)

| Metric | Current | Target | Status |
|--------|---------|--------|--------|
| [Metric 1] | [X]% | [Y]% | On Track / At Risk |

### Health Metrics (Weekly Review)

| Metric | Current | Threshold | Status |
|--------|---------|-----------|--------|
| [Error Rate] | [X]% | <[Y]% | Healthy / Warning |
| [Latency P95] | [X]s | <[Y]s | Healthy / Warning |

---

## 3.2 Validation Gates

Before committing significant resources, validate assumptions:

| Gate | Question | Evidence Required | Decision |
|------|----------|-------------------|----------|
| **Problem** | Is this a real problem worth solving? | [Evidence type] | Proceed / Pivot |
| **Solution** | Does our approach solve it? | [Evidence type] | Proceed / Pivot |
| **Retention** | Will users come back? | [Metric threshold] | Proceed / Pivot |
| **Scale** | Can we grow this? | [Evidence type] | Proceed / Pivot |

---

## 3.3 Course Correction Triggers

| Signal | Threshold | Observation Period | Response |
|--------|-----------|-------------------|----------|
| [Metric 1] | <[X]% | [Time period] | [Action] |
| [Metric 2] | >[X]% | [Time period] | [Action] |
| Safety incident | Any | Immediate | Pause development |

---

# Part 4: MVP Architecture Summary

## 4.1 Agent Topology

> **Note**: Full implementation details are in `ARCHITECTURE_BLUEPRINT.md`. This section provides strategic context.

```
[ASCII diagram of your agent topology]

Example:
USER INPUT
     │
     ├────────┬────────┐
     ▼        ▼        ▼
  Agent1  Agent2  Agent3   ← Parallel
     │        │        │
     └────────┴────────┘
              │
              ▼
          Agent4           ← Sequential
              │
              ▼
         RESPONSE
```

### Agent Specifications (MVP)

| Agent | Model | Timeout | Responsibility |
|-------|-------|---------|----------------|
| [Agent 1] | [Model] | [ms] | [What it does] |
| [Agent 2] | [Model] | [ms] | [What it does] |

### Latency Budget

- **Total**: <[X]s P95
- **With Streaming**: TTFT <[X]s (perceived instant)

---

## 4.2 Technology Stack (MVP)

| Layer | Choice | Rationale |
|-------|--------|-----------|
| **LLM** | [Provider/Model] | [Why this choice] |
| **Framework** | [Framework] | [Why this choice] |
| **Database** | [Database] | [Why this choice] |
| **Hosting** | [Platform] | [Why this choice] |

### Deferred Technology (Phase 2+)

| Technology | Phase | Why Deferred |
|------------|-------|--------------|
| [Tech] | Phase [X] | [Rationale] |

---

## 4.3 Technology Constraints

| Constraint | Requirement | Rationale |
|------------|-------------|-----------|
| **Latency** | [Target] | [Why this matters for users] |
| **Scale** | [Expected load] | [Growth assumptions] |
| **Integration** | [Required systems] | [Dependencies] |
| **Compliance** | [Requirements] | [Regulatory context] |

---

# Part 5: Operations

## 5.1 Launch Plan

### Phase 1 Launch

**Launch Type**: [Closed Beta | Open Beta | GA]

**Target Users**: [Number and source]

**Launch Sequence**:

| Week | Action |
|------|--------|
| -4 | [Preparation] |
| -2 | [Preparation] |
| 0 | Launch |
| +1 | [Monitoring] |
| +4 | [Review] |

---

## 5.2 Risk Monitoring

### Safety Risks

| Risk | Monitoring | Threshold | Action |
|------|------------|-----------|--------|
| [Risk 1] | [How monitored] | [Trigger] | [Response] |

### Technical Risks

| Risk | Monitoring | Threshold | Action |
|------|------------|-----------|--------|
| [Risk 1] | [How monitored] | [Trigger] | [Response] |

---

# Part 6: Document Hierarchy

This North Star specification is the **strategic anchor**. Implementation details live in supporting documents:

```
NORTHSTAR.md (this document)
├── Strategic decisions
├── Scope boundaries
├── Success metrics
└── MVP architecture summary

Supporting Documentation
├── NORTHSTAR_EXTRACT.md: Design DNA (immutable patterns)
├── ARCHITECTURE_BLUEPRINT.md: Multi-agent system design
├── SECURITY_ARCHITECTURE.md: Authentication, safety
├── BRAND_GUIDELINES.md: Identity and voice
├── ADR.md: Architecture decision records
└── INDEX.md: Document hierarchy and relationships
```

---

# Appendix A: Glossary

| Term | Definition |
|------|------------|
| [Term 1] | [Definition] |
| [Term 2] | [Definition] |

---

**End of North Star Specification**

*This document should be reviewed monthly and updated when strategic decisions change.*

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDERS]` with your project specifics
2. Fill in the North Star metric — this is the most important decision
3. Define 2-3 personas with real jobs-to-be-done
4. Set phase boundaries with concrete success/kill thresholds
5. Write the kill list — features you'll NEVER build
6. Sketch the MVP agent topology

**Record decisions as you go:**

Use [ADR_TEMPLATE.md](ADR_TEMPLATE.md) to capture significant decisions made while filling this template. Strategic choices (North Star selection, kill list items, persona prioritization) are worth recording with rationale.

**What makes a good North Star spec:**

- Clear enough that anyone can explain what success looks like
- Specific enough that you can measure progress weekly
- Constrained enough that you can say "no" to good ideas
- Honest about what you're NOT building

---

## Validation Schema (For AI Generation)

```yaml
# NORTHSTAR validation gate
inputs_required:
  - brand.name: from_brand_guidelines
  - brand.beliefs[]: from_brand_guidelines
  - brand.kill_list[]: from_brand_guidelines
  - target_users_expanded: from_user

outputs_produced:
  - northstar.metric.name: referenced_in_all_docs
  - northstar.metric.definition: used_by_architecture
  - northstar.anti_metrics[]: used_by_observability
  - northstar.personas[]: used_by_agent_prompts
  - northstar.phases[]: used_by_architecture
  - northstar.kill_list[]: used_by_northstar_extract

validation_gate:
  required_sections:
    - "North Star Metric"
    - "Input Metrics Hierarchy"
    - "Target Users"
    - "Phase Boundaries"
    - "Explicit Kill List"

  metric_criteria:
    leading: true
    actionable: true
    customer_centric: true
    understandable: true

  minimum_content:
    personas: 1
    personas[].functional_jobs: 1
    phases: 2
    phases[0].name: "MVP"
    kill_list_items: 3
    anti_metrics: 2

  placeholders_filled:
    - "[PROJECT NAME]"
    - "[Your North Star Metric Name]"
    - "[DATE]"

cross_references:
  - kill_list: must_include_items_from: brand.kill_list
  - positioning: must_align_with: brand.positioning

quality_checks:
  - metric_definition: one_sentence
  - each_persona: has_functional_and_emotional_jobs
  - each_phase: has_success_and_kill_thresholds
  - kill_list_items: have_rationale

decision_prompts:
  - "What's your gut feel for the North Star metric? (retention-based? outcome-based? task completion?)"
  - "Who is the *primary* user if you had to pick one?"
  - "What would make you kill this project? (kill thresholds)"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] North Star metric satisfies all 4 criteria (leading, actionable, customer-centric, understandable)
- [ ] At least 1 persona with functional and emotional jobs
- [ ] Phase 1 is named "MVP" with in-scope and out-of-scope defined
- [ ] Kill list includes items from BRAND_GUIDELINES kill list
- [ ] Anti-metrics explain why they're rejected
- [ ] Each phase has success criteria AND kill thresholds

---

© [YEAR] [AUTHOR/ORG]
