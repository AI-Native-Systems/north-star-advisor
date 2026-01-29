# [PROJECT_NAME]: User Journey Maps

<!-- GENERATION: This is Step 5a (with --ux flag). Generate first in design/ sequence, informs architecture. See GENERATION_MANIFEST.md -->

> **Parent**: [../NORTHSTAR_EXTRACT.md](../NORTHSTAR_EXTRACT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 5a of 13 — Requires `northstar.personas[]`, `brand.voice`
> **Principle Alignment**: Clarity (map user journeys to understand context and emotion)

Visual journey maps with emotional states for core user flows.

---

## Document Purpose

This document provides **visual user journey maps** that capture:

1. User actions at each step
2. Emotional states (anxiety, confidence, delight)
3. System responses and feedback
4. Potential friction points and mitigation strategies

**Relationship to Other Documents:**
- **PRINCIPLES.md** defines the Clarity principle requiring journey mapping
- **UI_DESIGN_SYSTEM.md** implements the patterns identified here
- **BRAND_GUIDELINES.md** provides the voice for each touchpoint

---

## 1. First-Time User Journey

### 1.1 Journey Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FIRST-TIME USER JOURNEY                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  AWARENESS          SIGNUP           ONBOARDING        CORE ACTION   VALUE  │
│      │                │                  │                 │           │    │
│      ▼                ▼                  ▼                 ▼           ▼    │
│  ┌───────┐       ┌───────┐         ┌───────┐         ┌───────┐   ┌───────┐ │
│  │Landing│──────▶│[AUTH] │────────▶│Welcome│────────▶│[CORE] │──▶│Results│ │
│  │ Page  │       │ Flow  │         │ Modal │         │ Flow  │   │ View  │ │
│  └───────┘       └───────┘         └───────┘         └───────┘   └───────┘ │
│                                                                              │
│  EMOTION:        EMOTION:          EMOTION:          EMOTION:    EMOTION:   │
│  Curious/        Low-            Oriented/         Invested/    Satisfied/ │
│  Skeptical       Friction        Hopeful           Anxious      Confident  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Detailed Journey Map

| Phase | User Action | Emotional State | System Response | Friction Points | Mitigation |
|-------|-------------|-----------------|-----------------|-----------------|------------|
| **Awareness** | Lands on homepage | Curious, possibly skeptical | Hero with clear value prop, social proof | "Is this real?" | Show actual output examples, not mock-ups |
| **Awareness** | Scrolls to "How It Works" | Evaluating | [N]-step visual walkthrough | Unclear outcome | Emphasize deliverable + outcome clarity |
| **Awareness** | Reads FAQ | Comparing alternatives | Direct answers, competitive positioning | "How is this different?" | Competitor comparison table |
| **Signup** | Clicks "[CTA_TEXT]" | Committed but cautious | [AUTH_FLOW: email-only / OAuth / magic link] | Too many fields | Minimize friction, progressive profile |
| **Signup** | Confirms email | Building trust | Welcome email with preview | Spam folder | Clear sender name, mobile-friendly |
| **Onboarding** | First login | Excited but uncertain | Welcome modal with [N]-step preview | Overwhelmed | Progressive disclosure, one action per screen |
| **Onboarding** | Clicks "[FIRST_ACTION_CTA]" | Hopeful | Guided creation with tooltips | Blank canvas paralysis | Pre-filled examples, clear prompts |
| **Core Action** | [DESCRIBES_CORE_ACTION] | Invested | Real-time validation, progress indicator | "Am I doing this right?" | Example toggle, contextual help |
| **Core Action** | Completes [CORE_FLOW_NAME] | Building momentum | Step completion celebrations | Dropping off mid-flow | Save progress, resume later |
| **Processing** | Clicks "[SUBMIT_CTA]" | Anxious anticipation | Progress UI with visibility | "Is it working?" | Real-time status, estimated time |
| **Processing** | Waits [EXPECTED_WAIT_TIME] | Impatient but engaged | Per-item progress cards | Fear of failure | Show completed items, not just spinner |
| **Results** | Views completed [OUTPUT_TYPE] | Relieved, curious | [PRIMARY_OUTPUT] highlighted | "Where do I start?" | Guide to first item |
| **Results** | Explores [OUTPUT_TYPE] | Delighted | Smooth navigation | Getting lost | Breadcrumbs, navigation map |
| **Results** | Exports/saves | Satisfied | Download with [FORMATS] | Format confusion | Default to common format, offer alternatives |

### 1.3 Emotional Arc Visualization

```
CONFIDENCE
    ▲
    │                                                           ●───● VALUE
    │                                                          ╱     REALIZED
    │                                                         ╱
    │                                            ●───●───●   ╱
    │                                           ╱ PROCESSING
    │                              ●───●───●   ╱
    │                             ╱ CORE      ●
    │                ●───●───●   ╱  ACTION
    │               ╱ONBOARDING ╱
    │    ●───●     ╱           ╱
    │   ╱SIGNUP   ╱           ╱
    │  ╱         ╱           ╱
    │ ●         ╱           ● (anxiety dip during wait)
    │AWARENESS ╱
    │         ╱
────┼────────────────────────────────────────────────────────────▶ TIME
    │
    ▼
ANXIETY
```

**Critical Moments:**
1. **Awareness → Signup**: Must overcome skepticism with tangible proof
2. **Core Action → Processing**: Highest anxiety point; transparency is crucial
3. **Processing → Results**: Moment of truth; must exceed expectations

---

## 2. Returning User Journey

### 2.1 Journey Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         RETURNING USER JOURNEY                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  RETURN              RESUME                NEW [ITEM]           ITERATE     │
│     │                  │                       │                    │       │
│     ▼                  ▼                       ▼                    ▼       │
│  ┌───────┐        ┌────────┐             ┌────────┐           ┌────────┐   │
│  │ Login │───────▶│Dashboard│────────────▶│Streamline│─────────▶│ Refine │  │
│  │       │        │        │             │ Flow   │           │[OUTPUT]│   │
│  └───────┘        └────────┘             └────────┘           └────────┘   │
│                        │                                                    │
│                        ▼                                                    │
│                  ┌────────┐                                                 │
│                  │ Resume │                                                 │
│                  │ [ITEM] │                                                 │
│                  └────────┘                                                 │
│                                                                              │
│  EMOTION:         EMOTION:               EMOTION:             EMOTION:      │
│  Familiar/        Oriented/              Efficient/           Mastery/      │
│  Purposeful       In-Control             Confident            Satisfaction  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Detailed Journey Map

| Phase | User Action | Emotional State | System Response | Friction Points | Mitigation |
|-------|-------------|-----------------|-----------------|-----------------|------------|
| **Return** | Opens app | Purpose-driven | Fast load, remember last state | Forgot where they were | Resume prompt for incomplete work |
| **Dashboard** | Scans [ITEMS] | Oriented | Recent items sorted by last modified | Finding the right one | Search + status badges |
| **Dashboard** | Checks [ITEM] status | In control | Clear badges (Draft, Processing, Complete) | Unclear next action | CTAs per item state |
| **Resume** | Opens in-progress [ITEM] | Determined | Return to exact step | Lost context | Progress summary, "Where you left off" |
| **Resume** | Continues input | Efficient | Pre-filled with previous answers | Changing their mind | Easy edit of prior steps |
| **New [ITEM]** | Creates new [ITEM] | Confident | Streamlined flow (no tooltips) | Repeating setup | Clone from template option |
| **New [ITEM]** | Adjusts from previous | Experienced | Show comparison to previous | Starting from scratch | Import prior context |
| **Iterate** | Regenerates [OUTPUT] | Exploring | Selective regeneration, diff view | Losing good parts | Version history, rollback |
| **Iterate** | Compares versions | Analytical | Side-by-side comparison | Confusion | Clear version labels |

### 2.3 Power User Patterns

```
RETURNING USER EFFICIENCY CURVE

ACTIONS/MINUTE
    ▲
    │                                    ●───●───●───● POWER USER
    │                               ●───●
    │                          ●───●
    │                     ●───●
    │                ●───●
    │           ●───●
    │      ●───●
    │ ●───● FIRST-TIME USER
    │
────┼───────────────────────────────────────────────────────────▶ SESSIONS
    │     1    2    3    4    5    6    7    8    9   10
```

**Optimization Opportunities:**
- Keyboard shortcuts for power users
- Quick actions from dashboard
- Batch operations (export all, regenerate set)
- Template library from previous [ITEMS]

---

## 3. Error Recovery Journey

### 3.1 Journey Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ERROR RECOVERY JOURNEY                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ERROR OCCURS       RECOGNITION        RECOVERY PATH      RESOLUTION        │
│       │                 │                   │                 │             │
│       ▼                 ▼                   ▼                 ▼             │
│  ┌─────────┐       ┌─────────┐        ┌─────────┐       ┌─────────┐        │
│  │Something│──────▶│ Clear   │───────▶│ Guided  │──────▶│ Back on │        │
│  │ Failed  │       │ Message │        │ Action  │       │  Track  │        │
│  └─────────┘       └─────────┘        └─────────┘       └─────────┘        │
│                                                                              │
│  EMOTION:          EMOTION:           EMOTION:          EMOTION:            │
│  Frustrated/       Understanding/     Hopeful/          Relieved/           │
│  Anxious           Calm               Active            Confident           │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Error Type Mapping

| Error Type | User Sees | Emotional Impact | Recovery Path | System Behavior |
|------------|-----------|------------------|---------------|-----------------|
| **Network** | "Connection interrupted" | Mild frustration | "Work saved locally. Reconnecting..." | Auto-retry, local cache |
| **Processing Timeout** | "Taking longer than expected" | Anxiety | "Continue waiting" or "Retry" | Circuit breaker, skip option |
| **Processing Failure** | "Couldn't complete [ITEM_NAME]" | Disappointment | "Retry" or "Skip and continue" | Partial completion preserved |
| **Validation** | "Missing required information" | Self-doubt | Highlight specific fields, offer examples | Never blame user |
| **Rate Limit** | "High demand right now" | Impatience | "We'll continue in [time]" | Queue with estimate |
| **Auth Expired** | "Session expired" | Interrupted | "Sign in to continue" (preserve state) | Don't lose work |
| **Unknown** | "Something went wrong" | Confusion | "Try again" + "Contact support" | Error tracking, incident ID |

### 3.3 Error Recovery Emotional Design

```
TRUST IMPACT BY ERROR HANDLING QUALITY

TRUST LEVEL
    ▲
    │ ●───●───●───●───● EXCELLENT RECOVERY
    │     ╲
    │      ╲●───●───● GOOD RECOVERY
    │       ╲
    │        ●───● POOR RECOVERY
    │         ╲
    │          ●─● NO RECOVERY
    │           ╲
    │            ● BLAME USER
    │
────┼─────────────────────────────────────────────────────────▶ TIME
    │ ERROR   +1h    +1d    +1w    +1mo
    │ OCCURS
```

**Recovery Quality Factors:**
1. **Speed to understanding**: User knows what happened in < 2 seconds
2. **Path to resolution**: Clear next action always visible
3. **State preservation**: Never lose user's work
4. **Emotional tone**: Calm, helpful, never blaming

---

## 4. Upgrade Journey (If Applicable)

> **Note**: Include this section if your product has a freemium or tiered model.

### 4.1 Free to Paid Transition

| Trigger | User Mindset | Prompt Design | Friction Points | Conversion Strategy |
|---------|--------------|---------------|-----------------|---------------------|
| Hits free limit | Invested, momentum | Soft gate with value preview | Price objection | Show completed work at risk |
| Sees paid feature | Curious | In-context upgrade prompt | Unclear benefit | Feature comparison table |
| Completes [CORE_ACTION] | Satisfied | Celebration + "More with [TIER]" | Not now | Discount for immediate upgrade |
| Returns after [N] days | Re-engaged | "Welcome back" + paid benefits | Forgot value | Remind of previous success |

### 4.2 Upgrade Decision Journey

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         UPGRADE DECISION JOURNEY                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  TRIGGER           EVALUATE            DECIDE              ACTIVATE         │
│     │                 │                   │                    │            │
│     ▼                 ▼                   ▼                    ▼            │
│  ┌───────┐       ┌─────────┐        ┌─────────┐         ┌─────────┐        │
│  │ Limit │──────▶│ Compare │───────▶│ Commit  │────────▶│Immediate│        │
│  │  Hit  │       │  Plans  │        │  Value  │         │  Access │        │
│  └───────┘       └─────────┘        └─────────┘         └─────────┘        │
│                                                                              │
│  EMOTION:         EMOTION:           EMOTION:            EMOTION:           │
│  Blocked/         Analytical/        Decisive/           Empowered/         │
│  Motivated        Comparing          Confident           Excited            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Persona-Specific Journey Variations

> **Note**: Create one subsection per persona from NORTHSTAR.md

### 5.1 [PERSONA_1_NAME] Journey

| Difference from Base | Reason | Accommodation |
|---------------------|--------|---------------|
| [BEHAVIOR_DIFFERENCE_1] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_2] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_3] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_4] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |

### 5.2 [PERSONA_2_NAME] Journey

| Difference from Base | Reason | Accommodation |
|---------------------|--------|---------------|
| [BEHAVIOR_DIFFERENCE_1] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_2] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_3] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_4] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |

### 5.3 [PERSONA_3_NAME] Journey

| Difference from Base | Reason | Accommodation |
|---------------------|--------|---------------|
| [BEHAVIOR_DIFFERENCE_1] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_2] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_3] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |
| [BEHAVIOR_DIFFERENCE_4] | [WHY_THIS_PERSONA_DIFFERS] | [UI/UX_ACCOMMODATION] |

---

## 6. Journey Metrics

### 6.1 Key Performance Indicators

| Journey Phase | Metric | Target | Measurement |
|---------------|--------|--------|-------------|
| **Awareness → Signup** | Conversion rate | > [N]% | Landing page → auth submit |
| **Signup → First [ITEM]** | Activation rate | > [N]% | Account → [ITEM] created ([N]d) |
| **First [ITEM] → Complete** | Completion rate | > [N]% | Start → view results |
| **Complete → Return** | Retention rate | > [N]% | First [ITEM] → second [ITEM] ([N]d) |
| **Error → Recovery** | Recovery rate | > [N]% | Error shown → successful retry |
| **Free → Paid** | Conversion rate | > [N]% | Free users → paid (annual) |

### 6.2 Emotional Measurement

| Touchpoint | Measurement Method | Signal |
|------------|-------------------|--------|
| Post-signup | NPS survey (delayed) | Initial satisfaction |
| Post-[CORE_ACTION] | In-app rating | Core value delivery |
| Post-error | Recovery success tracking | Resilience perception |
| [N]-day follow-up | Email survey | Sustained value |
| [N]-day milestone | Feature usage analytics | Power user development |

---

## 7. Implementation Priorities

### 7.1 Critical Path Items

| Priority | Journey Element | UI_DESIGN_SYSTEM Reference | Status |
|----------|-----------------|---------------------------|--------|
| **P0** | First-run welcome modal | Section [X] | [Planned/Defined/Built] |
| **P0** | Processing progress UI | Section [X] Loading States | [Planned/Defined/Built] |
| **P0** | Error states with recovery | Section [X] Error States | [Planned/Defined/Built] |
| **P1** | Dashboard resume prompts | Section [X] Returning User | [Planned/Defined/Built] |
| **P1** | Step-by-step progress indicator | Section [X] First-Time Journey | [Planned/Defined/Built] |
| **P2** | Navigation helpers | Section [X] Results phase | [Planned/Defined/Built] |
| **P2** | Version history for iteration | Section [X] | [Planned/Defined/Built] |

### 7.2 Accessibility Considerations per Journey

> **Full accessibility patterns**: See [ACCESSIBILITY.md](architecture/ACCESSIBILITY.md) for comprehensive WCAG 2.1 AA compliance, screen reader patterns, and testing protocols.

This table maps journey phases to key accessibility concerns:

| Journey Phase | Primary Concern | Accessibility Reference |
|---------------|-----------------|------------------------|
| **All phases** | Reduced motion | ACCESSIBILITY.md § Cognitive Load Mitigation |
| **All phases** | Screen reader announcements | ACCESSIBILITY.md § Live Region Strategy |
| **Awareness → Signup** | Form accessibility | ACCESSIBILITY.md § Form Accessibility |
| **Core Action** | Keyboard navigation | ACCESSIBILITY.md § Keyboard Navigation |
| **Processing wait** | Non-visual progress | ACCESSIBILITY.md § Screen Reader Patterns for Streaming UI |
| **Error recovery** | Error announcements | ACCESSIBILITY.md § Error Announcement Pattern |
| **Results viewing** | Focus management | ACCESSIBILITY.md § Focus Management During Generation |

**Key Integration Points:**

1. **Processing Wait States**: Long-running operations (e.g., 8-minute generation) require coalesced announcements to avoid overwhelming screen reader users. See ACCESSIBILITY.md § 2.3 Progress Announcement Patterns.

2. **Error Recovery**: Error states must move focus to the error heading and provide clear recovery paths. See ACCESSIBILITY.md § 2.5 Error Announcement Pattern.

3. **First-Time vs Returning**: First-time users need more guidance; returning users benefit from keyboard shortcuts. Both patterns are documented in ACCESSIBILITY.md § 6 Keyboard Navigation.

---

## Validation Schema (For AI Generation)

```yaml
# USER_JOURNEYS validation gate
inputs_required:
  - northstar.personas[]: from_northstar
  - northstar.phases[]: from_northstar
  - brand.voice: from_brand_guidelines
  - extract.axioms[]: from_northstar_extract

outputs_produced:
  - journeys.first_time_user: phases, emotional_states, friction_points
  - journeys.returning_user: power_user_patterns
  - journeys.error_recovery: error_type_mapping
  - journeys.persona_variations[]: per_persona_differences
  - journeys.metrics[]: kpis_per_phase

validation_gate:
  required_sections:
    - "First-Time User Journey"
    - "Returning User Journey"
    - "Error Recovery Journey"
    - "Persona-Specific Journey Variations"
    - "Journey Metrics"
    - "Implementation Priorities"

  minimum_content:
    journey_phases: 5
    error_types: 5
    persona_variations: 1
    kpis: 4

cross_references:
  - persona_variations: must_match: northstar.personas
  - journey_phases: must_align_with: northstar.phases
  - emotional_tone: must_align_with: brand.voice
  - accessibility_per_phase: must_reference: accessibility.patterns
  - error_journey: must_align_with: accessibility.error_announcements
  - processing_journey: must_align_with: accessibility.streaming_patterns

quality_checks:
  - emotional_arc: includes_anxiety_dip_and_recovery
  - error_recovery: never_blames_user
  - accessibility: all_phases_have_considerations
  - accessibility_references: links_to_accessibility_doc
```

**After generation, verify:**
- [ ] First-time user journey mapped with emotional states
- [ ] Returning user patterns identified
- [ ] Error recovery paths documented for all error types
- [ ] Persona variations captured for each persona
- [ ] Metrics defined for each phase
- [ ] Implementation priorities established
- [ ] Cross-referenced to UI_DESIGN_SYSTEM.md
- [ ] Accessibility considerations reference ACCESSIBILITY.md sections
- [ ] Processing wait patterns align with screen reader guidance

---

*[PROJECT_NAME] — User Journey Maps*
*Last Updated: [DATE]*

© [YEAR] [PROJECT_NAME]™
