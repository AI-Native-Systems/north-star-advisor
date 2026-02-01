# [PROJECT_NAME]: Wireframes

<!-- GENERATION: This is Step 5d (with --ux flag). Generate after USER_JOURNEYS, UI_DESIGN_SYSTEM, and ACCESSIBILITY. See GENERATION_MANIFEST.md -->

> **Parent**: [UI_DESIGN_SYSTEM.md](UI_DESIGN_SYSTEM.md)
> **Related**: [USER_JOURNEYS.md](USER_JOURNEYS.md), [ACCESSIBILITY.md](ACCESSIBILITY.md), [../BRAND_GUIDELINES.md](../BRAND_GUIDELINES.md)
> **Generation Step**: 5d of 13 — Requires `journeys.*`, `ui.*`, `a11y.*`, `brand.*`
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Platform**: [PLATFORM] (e.g., React Native / Expo, Next.js, etc.)

ASCII wireframe specifications synthesizing user journeys, design system, and accessibility into visual screen layouts.

---

## Document Purpose

This document provides **visual wireframe specifications** that synthesize:

1. Emotional states and friction points from USER_JOURNEYS.md
2. Design tokens and components from UI_DESIGN_SYSTEM.md
3. Accessibility requirements from ACCESSIBILITY.md
4. Brand voice and identity from BRAND_GUIDELINES.md

**Relationship to Other Documents:**
- **USER_JOURNEYS.md** defines the emotional arc we design for
- **UI_DESIGN_SYSTEM.md** provides the tokens we reference
- **ACCESSIBILITY.md** provides per-screen requirements
- **BRAND_GUIDELINES.md** provides voice validation criteria
- **This document** translates strategy into visual specifications

---

## 1. Overview

### 1.1 Critical Brand Gaps Addressed

> **Note**: Identify any gaps between brand intent and current design system, then specify fixes.

| Gap | Issue | Solution | Applied In |
|-----|-------|----------|------------|
| [GAP_1_NAME] | [DESCRIPTION_OF_MISMATCH] | [SPECIFIC_FIX] | [SCREEN_NAMES] |
| [GAP_2_NAME] | [DESCRIPTION_OF_MISMATCH] | [SPECIFIC_FIX] | [SCREEN_NAMES] |
| [GAP_3_NAME] | [DESCRIPTION_OF_MISMATCH] | [SPECIFIC_FIX] | [SCREEN_NAMES] |

### 1.2 Design Token References

All specifications reference tokens from `UI_DESIGN_SYSTEM.md`:

- **Colors**: `--color-[TOKEN]` (background, surface, accent, text, semantic)
- **Typography**: `--text-[SIZE]`, `--font-[FAMILY]`
- **Spacing**: `--space-[N]` (4px increments)
- **Components**: Button sizes, touch targets, border radius

---

## 2. Color Extensions

> **Note**: If the base palette needs extension for this project's specific needs.

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `--color-[SEMANTIC_1]` | [HEX] | [HEX] | [USAGE_DESCRIPTION] |
| `--color-[SEMANTIC_2]` | [HEX] | [HEX] | [USAGE_DESCRIPTION] |
| `--color-[SEMANTIC_3]-glow` | [RGBA] | [RGBA] | [GLOW_EFFECT_USAGE] |

---

## 3. Brand Visual Identity Elements

### 3.1 [BRAND_ELEMENT_NAME] Component

> **Note**: Define visual signature elements that express brand archetype (e.g., AI avatar, coach bubble, assistant indicator).

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [ASCII REPRESENTATION OF BRAND ELEMENT]        │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Specifications:**
- Max Width: [WIDTH]
- Padding: [SPACING_TOKEN]
- Border Radius: [RADIUS_SPEC]
- Background: [COLOR_TOKEN]
- Font: [TYPOGRAPHY_TOKEN]

### 3.2 [BRAND_ELEMENT_NAME] Presence Indicator

```
┌─────────────────────────────────────────────────┐
│                                                 │
│   [INDICATOR_VISUAL]                            │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

## 4. Core Screen Wireframes

---

### 4.1 [SCREEN_1_NAME]

**Purpose:** [WHAT_THIS_SCREEN_DOES]
**Emotional State:** [STATE_FROM_USER_JOURNEYS] (per USER_JOURNEYS.md)
**Critical Requirement:** [KEY_UX_REQUIREMENT]

```
┌─────────────────────────────────────────────────┐
│ [HEADER_AREA]                                   │
├─────────────────────────────────────────────────┤
│                                                 │
│  [MAIN_CONTENT_AREA]                            │
│                                                 │
│  ┌───────────────────────────────────────────┐  │
│  │                                           │  │
│  │  [CONTENT_BLOCK_1]                        │  │
│  │                                           │  │
│  └───────────────────────────────────────────┘  │
│                                                 │
│  ┌───────────────────────────────────────────┐  │
│  │                                           │  │
│  │  [CONTENT_BLOCK_2]                        │  │
│  │                                           │  │
│  └───────────────────────────────────────────┘  │
│                                                 │
├─────────────────────────────────────────────────┤
│  [ACTION_AREA]                                  │
│                                                 │
│  ┌─────────────┐        ┌─────────────────┐    │
│  │ [SECONDARY] │        │    [PRIMARY]    │    │
│  └─────────────┘        └─────────────────┘    │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Component Specifications:**

| Element | Token/Size | Notes |
|---------|------------|-------|
| [ELEMENT_1] | [TOKEN_REF] | [NOTES] |
| [ELEMENT_2] | [TOKEN_REF] | [NOTES] |
| [ELEMENT_3] | [TOKEN_REF] | [NOTES] |

**Accessibility Requirements:**
- [ ] Touch targets ≥ 44px
- [ ] [SCREEN_SPECIFIC_A11Y_1]
- [ ] [SCREEN_SPECIFIC_A11Y_2]
- [ ] [SCREEN_SPECIFIC_A11Y_3]

**Brand Voice Validation:**
- [ ] [VOICE_CHECK_1]
- [ ] [VOICE_CHECK_2]
- [ ] [VOICE_CHECK_3]

---

### 4.2 [SCREEN_2_NAME]

**Purpose:** [WHAT_THIS_SCREEN_DOES]
**Emotional State:** [STATE_FROM_USER_JOURNEYS] (per USER_JOURNEYS.md)
**Critical Requirement:** [KEY_UX_REQUIREMENT]

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [ASCII WIREFRAME]                              │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Component Specifications:**

| Element | Token/Size | Notes |
|---------|------------|-------|
| [ELEMENT_1] | [TOKEN_REF] | [NOTES] |

**Accessibility Requirements:**
- [ ] [REQUIREMENT_1]

**Brand Voice Validation:**
- [ ] [VALIDATION_1]

---

### 4.3 [SCREEN_3_NAME]

**Purpose:** [WHAT_THIS_SCREEN_DOES]
**Emotional State:** [STATE_FROM_USER_JOURNEYS] (per USER_JOURNEYS.md)
**Critical Requirement:** [KEY_UX_REQUIREMENT]

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [ASCII WIREFRAME]                              │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Component Specifications:**

| Element | Token/Size | Notes |
|---------|------------|-------|
| [ELEMENT_1] | [TOKEN_REF] | [NOTES] |

**Accessibility Requirements:**
- [ ] [REQUIREMENT_1]

**Brand Voice Validation:**
- [ ] [VALIDATION_1]

---

### 4.4 [SCREEN_4_NAME]

**Purpose:** [WHAT_THIS_SCREEN_DOES]
**Emotional State:** [STATE_FROM_USER_JOURNEYS] (per USER_JOURNEYS.md)
**Critical Requirement:** [KEY_UX_REQUIREMENT]

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [ASCII WIREFRAME]                              │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Component Specifications:**

| Element | Token/Size | Notes |
|---------|------------|-------|
| [ELEMENT_1] | [TOKEN_REF] | [NOTES] |

**Accessibility Requirements:**
- [ ] [REQUIREMENT_1]

**Brand Voice Validation:**
- [ ] [VALIDATION_1]

---

### 4.5 [SCREEN_5_NAME]

**Purpose:** [WHAT_THIS_SCREEN_DOES]
**Emotional State:** [STATE_FROM_USER_JOURNEYS] (per USER_JOURNEYS.md)
**Critical Requirement:** [KEY_UX_REQUIREMENT]

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [ASCII WIREFRAME]                              │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Component Specifications:**

| Element | Token/Size | Notes |
|---------|------------|-------|
| [ELEMENT_1] | [TOKEN_REF] | [NOTES] |

**Accessibility Requirements:**
- [ ] [REQUIREMENT_1]

**Brand Voice Validation:**
- [ ] [VALIDATION_1]

---

## 5. Screen States

### 5.1 Loading/Processing States

> **Note**: Use human language, never clinical terms like "Processing..." or "Analyzing..."

**Processing Messages (Rotate):**
1. "[HUMAN_LANGUAGE_MESSAGE_1]"
2. "[HUMAN_LANGUAGE_MESSAGE_2]"
3. "[HUMAN_LANGUAGE_MESSAGE_3]"

**Banned Processing Language:**
- ❌ "Processing..."
- ❌ "Analyzing..."
- ❌ "Please wait..."
- ❌ "Loading..."

```
┌─────────────────────────────────────────────────┐
│                                                 │
│           [PROGRESS_VISUALIZATION]              │
│                                                 │
│  ╭─────────────────────────────────────────╮   │
│  │                                         │   │
│  │  "[HUMAN_LANGUAGE_MESSAGE]"             │   │
│  │                                         │   │
│  ╰─────────────────────────────────────────╯   │
│                                                 │
│       ┌───────────────────────────────┐         │
│       │ [PROGRESS_BAR]                │         │
│       └───────────────────────────────┘         │
│                                                 │
└─────────────────────────────────────────────────┘
```

### 5.2 Error States

> **Note**: Never blame user. Use collaborative language ("Let's try...").

| Error Type | Icon | Message | Solution |
|------------|------|---------|----------|
| [ERROR_TYPE_1] | [ICON] | "[NO_BLAME_MESSAGE]" | "[LETS_SOLUTION]" |
| [ERROR_TYPE_2] | [ICON] | "[NO_BLAME_MESSAGE]" | "[LETS_SOLUTION]" |
| [ERROR_TYPE_3] | [ICON] | "[NO_BLAME_MESSAGE]" | "[LETS_SOLUTION]" |

**Error Language Rules:**
- ✅ "I couldn't..." (takes responsibility)
- ✅ "This sometimes happens..." (normalizes)
- ✅ "Let's try..." (collaborative)
- ❌ "You did something wrong"
- ❌ "Error code: XXX"
- ❌ "Failed" / "Invalid" / "Wrong"

```
┌─────────────────────────────────────────────────┐
│                                                 │
│                    ╭───╮                        │
│                    │[I]│                        │  ← Friendly icon
│                    ╰───╯                        │
│                                                 │
│  ╭─────────────────────────────────────────╮   │
│  │                                         │   │
│  │  "[NO_BLAME_MESSAGE]"                   │   │
│  │                                         │   │
│  │  "[LETS_SOLUTION]"                      │   │
│  │                                         │   │
│  ╰─────────────────────────────────────────╯   │
│                                                 │
│            ┌─────────────────────┐              │
│            │     [ACTION]        │              │
│            └─────────────────────┘              │
│                                                 │
└─────────────────────────────────────────────────┘
```

### 5.3 Empty States

```
┌─────────────────────────────────────────────────┐
│                                                 │
│                    ╭───╮                        │
│                    │[I]│                        │
│                    ╰───╯                        │
│                                                 │
│            "[ENCOURAGING_MESSAGE]"              │
│                                                 │
│            ┌─────────────────────┐              │
│            │   [GET_STARTED]     │              │
│            └─────────────────────┘              │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

## 6. Component Quick Reference

### 6.1 Buttons

| Type | Height | Style | Usage |
|------|--------|-------|-------|
| Primary | 48px | `--color-accent` bg, white text | Main action (one per screen) |
| Secondary | 48px | Transparent, `--color-accent` border | Supporting action |
| Ghost | 44px | Transparent, `--color-text-secondary` | Tertiary, navigation |
| [SPECIAL_BUTTON] | [SIZE] | [STYLE] | [USAGE] |

### 6.2 Cards

| Type | Left Border | Icon | Usage |
|------|-------------|------|-------|
| [CARD_TYPE_1] | [BORDER_COLOR] | [ICON] | [USAGE] |
| [CARD_TYPE_2] | [BORDER_COLOR] | [ICON] | [USAGE] |

### 6.3 [BRAND_ELEMENT] Elements

| Element | Style | Usage |
|---------|-------|-------|
| [ELEMENT_1] | [STYLE_SPEC] | [USAGE] |
| [ELEMENT_2] | [STYLE_SPEC] | [USAGE] |

---

## 7. Brand Voice Validation Checklist

### 7.1 Per-Screen Validation

For every screen, validate:

- [ ] Leading with something positive?
- [ ] Using collaborative language ("let's," "we")?
- [ ] Feedback specific (with context/timestamps)?
- [ ] Avoiding absolute judgments?
- [ ] Limiting to actionable items?
- [ ] Sounds like [BRAND_ARCHETYPE], not critic?

### 7.2 Banned Elements (Kill List)

> **Note**: Elements that must NEVER appear in the UI.

- [ ] No [BANNED_ELEMENT_1]
- [ ] No [BANNED_ELEMENT_2]
- [ ] No [BANNED_ELEMENT_3]
- [ ] No [BANNED_ELEMENT_4]
- [ ] No [BANNED_ELEMENT_5]

---

## 8. Implementation Priority

### P0 - MVP

> **Note**: Must have for launch.

1. [SCREEN_NAME] - [REASON]
2. [SCREEN_NAME] - [REASON]
3. [SCREEN_NAME] - [REASON]
4. [SCREEN_NAME] - [REASON]

### P1 - Core Experience

> **Note**: Required for complete experience.

5. [SCREEN_NAME] - [REASON]
6. [SCREEN_NAME] - [REASON]
7. [SCREEN_NAME] - [REASON]

### P2 - Polish

> **Note**: Enhancement and refinement.

8. [FEATURE_NAME] - [REASON]
9. [FEATURE_NAME] - [REASON]
10. [FEATURE_NAME] - [REASON]

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDER]` values with project-specific content
2. Add ASCII wireframes for each core screen (minimum 5)
3. Reference design tokens from UI_DESIGN_SYSTEM.md
4. Add accessibility requirements per screen
5. Add brand voice validation per screen
6. Define banned elements from brand kill list
7. Set implementation priorities

**Key placeholders to replace:**
- `[PROJECT_NAME]` - Your project name
- `[PLATFORM]` - Target platform (React Native, Next.js, etc.)
- `[SCREEN_N_NAME]` - Names of core screens
- `[BRAND_ARCHETYPE]` - Brand archetype (Coach, Guide, etc.)
- `[BRAND_ELEMENT_NAME]` - Visual signature element

---

## Validation Schema (For AI Generation)

```yaml
# WIREFRAMES validation gate
inputs_required:
  # From USER_JOURNEYS (5a)
  - journeys.first_time_user: phases, emotional_states, friction_points
  - journeys.returning_user: power_user_patterns
  - journeys.error_recovery: error_type_mapping
  - journeys.persona_variations[]: per_persona_differences

  # From UI_DESIGN_SYSTEM (5b)
  - ui.css_tokens: color, typography, spacing tokens
  - ui.component_specs: buttons, cards, inputs
  - ui.utility_classes: layout, text utilities

  # From ACCESSIBILITY (5c)
  - a11y.wcag_checklist: per_screen_validation
  - a11y.focus_management: state_transitions
  - a11y.cognitive_load_patterns: progressive_disclosure

  # From BRAND_GUIDELINES (1)
  - brand.voice: tone, language patterns
  - brand.beliefs[]: core_principles
  - brand.kill_list[]: banned_elements

outputs_produced:
  - wireframes.core_screens[]: ascii_wireframes_with_specs
  - wireframes.color_extensions: additional_semantic_colors
  - wireframes.brand_identity: visual_signature_elements
  - wireframes.component_specs[]: per_screen_specifications
  - wireframes.accessibility_checklist[]: per_screen_requirements
  - wireframes.brand_validation[]: voice_checklist_per_screen
  - wireframes.banned_elements: kill_list
  - wireframes.implementation_priority: p0_p1_p2

validation_gate:
  required_sections:
    - "Overview"
    - "Color Extensions"
    - "Brand Visual Identity Elements"
    - "Core Screen Wireframes"
    - "Screen States"
    - "Component Quick Reference"
    - "Brand Voice Validation Checklist"
    - "Implementation Priority"

  minimum_content:
    core_screens: 5
    component_specs: 5
    accessibility_items_per_screen: 3
    banned_elements: 3

cross_references:
  - emotional_states: must_match: journeys.first_time_user.emotional_states
  - error_screens: must_align_with: journeys.error_recovery
  - design_tokens: must_reference: ui.css_tokens
  - component_sizes: must_match: ui.component_specs
  - touch_targets: must_pass: a11y.wcag_checklist (2.5.5)
  - focus_states: must_align_with: a11y.focus_management
  - voice_validation: must_align_with: brand.voice
  - banned_elements: must_include: brand.kill_list[]

quality_checks:
  - screens: include_emotional_state_annotation
  - components: reference_design_tokens
  - accessibility: per_screen_checklist_present
  - brand_voice: validation_checklist_per_screen
  - implementation: priority_levels_assigned

decision_prompts:
  - "What are the core screens for your MVP?"
  - "Does your brand have a visual signature element (avatar, bubble, indicator)?"
  - "What color extensions are needed beyond the base palette?"
  - "What elements are explicitly banned from your UI?"
```

**After generation, verify:**
- [ ] At least 5 core screens with ASCII wireframes
- [ ] All screens reference design tokens from UI_DESIGN_SYSTEM.md
- [ ] All screens have accessibility requirements
- [ ] All screens have brand voice validation
- [ ] Banned elements section populated from brand kill list
- [ ] Implementation priorities assigned (P0/P1/P2)
- [ ] No `[PLACEHOLDER]` text remains

---


*Document generated by North Star Advisor*
