# [PROJECT NAME]: UI Design System

<!-- GENERATION: This is Step 5b (with --ux flag). Requires outputs from USER_JOURNEYS and BRAND_GUIDELINES. See GENERATION_MANIFEST.md -->

> **Parent**: [../BRAND_GUIDELINES.md](../BRAND_GUIDELINES.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 5b of 13 — Requires `brand.colors`, `brand.typography`, `journeys.*`

Production-ready design tokens, CSS configuration, and component specifications.

---

## Document Purpose

This document provides **copy-paste ready code** for implementing [PROJECT NAME]'s visual design. It contains:

1. CSS custom properties (design tokens)
2. Framework integration patterns
3. Component specifications
4. Utility classes

**Relationship to Other Documents:**
- **BRAND_GUIDELINES.md** defines the rationale and constraints
- **This document** defines the implementation tokens

---

## 1. CSS Custom Properties

Add to your global CSS (e.g., `globals.css`, `styles.css`, or equivalent):

```css
:root {
  /* =========================
     COLOR TOKENS
     ========================= */

  /* Background & Surface */
  --color-background: [BACKGROUND_COLOR];
  --color-surface: [SURFACE_COLOR];
  --color-surface-elevated: [ELEVATED_SURFACE_COLOR];

  /* Brand Accent */
  --color-accent: [ACCENT_COLOR];
  --color-accent-hover: [ACCENT_HOVER_COLOR];
  --color-accent-subtle: [ACCENT_SUBTLE_COLOR];

  /* Text */
  --color-text-primary: [TEXT_PRIMARY_COLOR];
  --color-text-secondary: [TEXT_SECONDARY_COLOR];
  --color-text-muted: [TEXT_MUTED_COLOR];
  --color-text-inverse: [TEXT_INVERSE_COLOR];

  /* Borders */
  --color-border: [BORDER_COLOR];
  --color-border-strong: [BORDER_STRONG_COLOR];

  /* Semantic Colors */
  --color-info: [INFO_COLOR];
  --color-info-subtle: [INFO_SUBTLE_COLOR];
  --color-success: [SUCCESS_COLOR];
  --color-success-subtle: [SUCCESS_SUBTLE_COLOR];
  --color-warning: [WARNING_COLOR];
  --color-warning-subtle: [WARNING_SUBTLE_COLOR];
  --color-error: [ERROR_COLOR];
  --color-error-subtle: [ERROR_SUBTLE_COLOR];

  /* =========================
     TYPOGRAPHY TOKENS
     ========================= */

  /* Font Families */
  --font-display: [DISPLAY_FONT_STACK];
  --font-body: [BODY_FONT_STACK];
  --font-mono: [MONO_FONT_STACK];

  /* Font Sizes (Major Third Scale: 1.25) */
  --text-xs: 0.75rem;      /* 12px */
  --text-sm: 0.875rem;     /* 14px */
  --text-base: 1rem;       /* 16px */
  --text-lg: 1.125rem;     /* 18px */
  --text-xl: 1.25rem;      /* 20px */
  --text-2xl: 1.5rem;      /* 24px */
  --text-3xl: 2rem;        /* 32px */
  --text-4xl: 2.5rem;      /* 40px */

  /* Line Heights */
  --leading-none: 1;
  --leading-tight: 1.2;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;

  /* Font Weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;

  /* =========================
     SPACING TOKENS
     ========================= */

  --space-0: 0;
  --space-px: 1px;
  --space-0-5: 0.125rem;   /* 2px */
  --space-1: 0.25rem;      /* 4px */
  --space-2: 0.5rem;       /* 8px */
  --space-3: 0.75rem;      /* 12px */
  --space-4: 1rem;         /* 16px */
  --space-5: 1.25rem;      /* 20px */
  --space-6: 1.5rem;       /* 24px */
  --space-8: 2rem;         /* 32px */
  --space-10: 2.5rem;      /* 40px */
  --space-12: 3rem;        /* 48px */
  --space-16: 4rem;        /* 64px */
  --space-20: 5rem;        /* 80px */
  --space-24: 6rem;        /* 96px */

  /* =========================
     BORDER RADIUS
     ========================= */

  --radius-none: 0;
  --radius-sm: 0.25rem;    /* 4px */
  --radius-md: 0.375rem;   /* 6px */
  --radius-lg: 0.5rem;     /* 8px */
  --radius-xl: 0.75rem;    /* 12px */
  --radius-2xl: 1rem;      /* 16px */
  --radius-full: 9999px;

  /* =========================
     SHADOWS
     ========================= */

  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 1px 3px rgba(0, 0, 0, 0.08), 0 1px 2px rgba(0, 0, 0, 0.06);
  --shadow-lg: 0 4px 6px rgba(0, 0, 0, 0.07), 0 2px 4px rgba(0, 0, 0, 0.06);
  --shadow-xl: 0 10px 15px rgba(0, 0, 0, 0.08), 0 4px 6px rgba(0, 0, 0, 0.05);

  /* =========================
     TRANSITIONS
     ========================= */

  --transition-fast: 100ms ease-out;
  --transition-base: 150ms ease-out;
  --transition-slow: 200ms ease-in-out;
  --transition-slower: 300ms ease-in-out;

  /* =========================
     LAYOUT
     ========================= */

  --container-narrow: 40rem;   /* 640px */
  --container-default: 48rem;  /* 768px */
  --container-wide: 64rem;     /* 1024px */
  --container-full: 80rem;     /* 1280px */

  /* =========================
     Z-INDEX
     ========================= */

  --z-base: 0;
  --z-dropdown: 10;
  --z-sticky: 20;
  --z-fixed: 30;
  --z-modal-backdrop: 40;
  --z-modal: 50;
  --z-popover: 60;
  --z-tooltip: 70;
}

/* Reduced motion preference */
@media (prefers-reduced-motion: reduce) {
  :root {
    --transition-fast: 0ms;
    --transition-base: 0ms;
    --transition-slow: 0ms;
    --transition-slower: 0ms;
  }
}
```

---

## 2. Framework Integration

> **Note**: Adapt to your CSS framework (Tailwind, vanilla CSS, CSS-in-JS, Bootstrap, etc.)

### 2.1 Integration Patterns

Map CSS custom properties to your framework's configuration:

| Token Category | CSS Variable | Framework Mapping |
|----------------|--------------|-------------------|
| Colors | `--color-accent` | Theme color / utility class |
| Typography | `--font-display` | Font family setting |
| Spacing | `--space-4` | Spacing scale unit |
| Shadows | `--shadow-md` | Shadow utility / mixin |
| Transitions | `--transition-base` | Animation duration |

### 2.2 Configuration Structure

```
// Pseudocode - adapt to your framework

ThemeConfig:
  colors:
    background: var(--color-background)
    surface: var(--color-surface)
    accent:
      default: var(--color-accent)
      hover: var(--color-accent-hover)
      subtle: var(--color-accent-subtle)
    text:
      primary: var(--color-text-primary)
      secondary: var(--color-text-secondary)
      muted: var(--color-text-muted)
    border:
      default: var(--color-border)
      strong: var(--color-border-strong)
    semantic:
      info: var(--color-info)
      success: var(--color-success)
      warning: var(--color-warning)
      error: var(--color-error)

  typography:
    fontFamily:
      display: var(--font-display)
      body: var(--font-body)
      mono: var(--font-mono)
    fontSize:
      xs: var(--text-xs)
      sm: var(--text-sm)
      base: var(--text-base)
      lg: var(--text-lg)
      xl: var(--text-xl)
      2xl: var(--text-2xl)
      3xl: var(--text-3xl)
      4xl: var(--text-4xl)

  spacing: [--space-0 through --space-24]

  shadows:
    card: var(--shadow-md)
    cardHover: var(--shadow-lg)
    button: var(--shadow-sm)

  transitions:
    fast: var(--transition-fast)
    base: var(--transition-base)
    slow: var(--transition-slow)

  animations:
    fadeIn: opacity 0 → 1, duration 200ms
    fadeUp: opacity 0 → 1 + translateY 8px → 0, duration 200ms
    pulseSubtle: opacity 1 → 0.7 → 1, duration 2s infinite
```

---

## 3. Component Specifications

### 3.1 Buttons

```css
/* Primary Button */
.btn-primary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: var(--space-3) var(--space-5);
  background-color: var(--color-accent);
  color: var(--color-text-primary);
  font-family: var(--font-body);
  font-weight: var(--font-medium);
  font-size: var(--text-base);
  border-radius: [BUTTON_RADIUS];
  box-shadow: var(--shadow-sm);
  transition: all var(--transition-base);
}

.btn-primary:hover {
  background-color: var(--color-accent-hover);
}

.btn-primary:focus {
  outline: none;
  box-shadow: 0 0 0 2px var(--color-accent), 0 0 0 4px var(--color-background);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Secondary Button */
.btn-secondary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: var(--space-3) var(--space-5);
  background-color: transparent;
  color: var(--color-text-primary);
  font-family: var(--font-body);
  font-weight: var(--font-medium);
  font-size: var(--text-base);
  border: 1px solid var(--color-border);
  border-radius: [BUTTON_RADIUS];
  transition: all var(--transition-base);
}

.btn-secondary:hover {
  background-color: var(--color-background);
}

/* Ghost Button */
.btn-ghost {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: var(--space-3) var(--space-5);
  background-color: transparent;
  color: var(--color-text-secondary);
  font-family: var(--font-body);
  font-weight: var(--font-medium);
  font-size: var(--text-base);
  border-radius: [BUTTON_RADIUS];
  transition: all var(--transition-base);
}

.btn-ghost:hover {
  color: var(--color-text-primary);
  background-color: var(--color-background);
}
```

### 3.2 Cards

```css
/* Base Card */
.card {
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: [CARD_RADIUS];
  box-shadow: var(--shadow-md);
  padding: var(--space-6);
  transition: box-shadow var(--transition-slow);
}

.card:hover {
  box-shadow: var(--shadow-lg);
}

/* Interactive Card */
.card-interactive {
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: [CARD_RADIUS];
  box-shadow: var(--shadow-md);
  padding: var(--space-6);
  cursor: pointer;
  transition: all var(--transition-slow);
}

.card-interactive:hover {
  box-shadow: var(--shadow-lg);
  border-color: var(--color-accent);
}

.card-title {
  font-family: var(--font-display);
  font-size: var(--text-xl);
  color: var(--color-text-primary);
  margin-bottom: var(--space-2);
}

.card-description {
  font-family: var(--font-body);
  font-size: var(--text-base);
  color: var(--color-text-secondary);
}
```

### 3.3 Input Fields

```css
/* Text Input */
.input {
  width: 100%;
  padding: var(--space-3) var(--space-4);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  font-family: var(--font-body);
  font-size: var(--text-base);
  color: var(--color-text-primary);
  transition: border-color var(--transition-base);
}

.input::placeholder {
  color: var(--color-text-muted);
}

.input:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 1px var(--color-accent);
}

/* Text Area */
.textarea {
  width: 100%;
  padding: var(--space-3) var(--space-4);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  font-family: var(--font-body);
  font-size: var(--text-base);
  line-height: var(--leading-relaxed);
  color: var(--color-text-primary);
  resize: none;
  transition: border-color var(--transition-base);
}

.textarea:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 1px var(--color-accent);
}
```

### 3.4 Badges / Tags

```css
/* Status Badge */
.badge {
  display: inline-flex;
  align-items: center;
  padding: var(--space-0-5) var(--space-2);
  background-color: var(--color-background);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-full);
  font-family: var(--font-mono);
  font-size: var(--text-xs);
  color: var(--color-text-secondary);
}

/* Category Tag */
.tag {
  display: inline-flex;
  align-items: center;
  padding: var(--space-1) var(--space-2);
  background-color: var(--color-info-subtle);
  border-radius: var(--radius-md);
  font-family: var(--font-body);
  font-size: var(--text-xs);
  font-weight: var(--font-medium);
  color: var(--color-info);
}
```

### 3.5 Loading States

```css
/* Pulse Animation */
@keyframes pulse-subtle {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.7; }
}

/* Skeleton Loader */
.skeleton {
  background-color: var(--color-border);
  border-radius: var(--radius-sm);
  animation: pulse-subtle 2s ease-in-out infinite;
}

/* Skeleton Card */
.skeleton-card {
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: [CARD_RADIUS];
  padding: var(--space-6);
  animation: pulse-subtle 2s ease-in-out infinite;
}

.skeleton-title {
  height: var(--space-6);
  width: 50%;
  background-color: var(--color-border);
  border-radius: var(--radius-sm);
  margin-bottom: var(--space-3);
}

.skeleton-text {
  height: var(--space-4);
  width: 100%;
  background-color: var(--color-border);
  border-radius: var(--radius-sm);
  margin-bottom: var(--space-2);
}
```

---

## 4. Utility Classes

### 4.1 Text Utilities

```css
/* Add to globals.css */

/* Display text (headers, emphasis) */
.text-display {
  font-family: var(--font-display);
}

/* Body text (content, UI) */
.text-body {
  font-family: var(--font-body);
}

/* Mono text (code, technical) */
.text-mono {
  font-family: var(--font-mono);
}

/* Comfortable reading width */
.prose-width {
  max-width: 65ch;
}
```

### 4.2 Layout Utilities

```css
/* Container utilities */
.container-narrow {
  max-width: var(--container-narrow);
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--space-4);
  padding-right: var(--space-4);
}

.container-content {
  max-width: var(--container-default);
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--space-4);
  padding-right: var(--space-4);
}

.container-wide {
  max-width: var(--container-wide);
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--space-4);
  padding-right: var(--space-4);
}
```

### 4.3 Focus Utilities

```css
/* Consistent focus ring */
.focus-ring {
  outline: none;
}

.focus-ring:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
```

---

## 5. Responsive Breakpoints

### 5.1 Standard Breakpoints

| Name | Width | Use Case |
|------|-------|----------|
| `sm` | 640px | Small tablets, large phones |
| `md` | 768px | Tablets |
| `lg` | 1024px | Small laptops |
| `xl` | 1280px | Desktops |
| `2xl` | 1536px | Large desktops |

### 5.2 Mobile-First Patterns

```css
/* Responsive card grid */
.card-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--space-4);
}

@media (min-width: 640px) {
  .card-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: var(--space-6);
  }
}

@media (min-width: 1024px) {
  .card-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* Responsive container */
.responsive-container {
  padding-left: var(--space-4);
  padding-right: var(--space-4);
  max-width: var(--container-wide);
  margin-left: auto;
  margin-right: auto;
}

@media (min-width: 640px) {
  .responsive-container {
    padding-left: var(--space-6);
    padding-right: var(--space-6);
  }
}

@media (min-width: 1024px) {
  .responsive-container {
    padding-left: var(--space-8);
    padding-right: var(--space-8);
  }
}

/* Responsive typography */
.heading-responsive {
  font-family: var(--font-display);
  font-size: var(--text-2xl);
  color: var(--color-text-primary);
}

@media (min-width: 640px) {
  .heading-responsive {
    font-size: var(--text-3xl);
  }
}

@media (min-width: 1024px) {
  .heading-responsive {
    font-size: var(--text-4xl);
  }
}
```

---

## 6. Icon Specifications

### 6.1 Recommended Icon Sets

| Source | Style | Notes |
|--------|-------|-------|
| [Lucide](https://lucide.dev) | Outlined | Clean, consistent stroke |
| [Heroicons](https://heroicons.com) | Outlined | Well-designed system icons |
| [Phosphor](https://phosphoricons.com) | Multiple weights | Flexible icon family |
| [Tabler Icons](https://tabler-icons.io) | Outlined | Large collection |

> **Note**: Choose one icon set for consistency. Adapt import method to your framework.

### 6.2 Icon Sizing

| Size | Dimensions | Use Case |
|------|------------|----------|
| Small | 16×16px | Inline with text, compact buttons |
| Default | 20×20px | Standard UI elements |
| Large | 24×24px | Navigation, prominent actions |

```css
/* Icon sizing classes */
.icon-sm {
  width: 1rem;
  height: 1rem;
}

.icon-md {
  width: 1.25rem;
  height: 1.25rem;
}

.icon-lg {
  width: 1.5rem;
  height: 1.5rem;
}
```

### 6.3 Core Icon Set

| Purpose | Icon Name | Usage |
|---------|-----------|-------|
| Close | `X` / `Close` | Dismiss, close |
| Menu | `Menu` / `Bars` | Mobile navigation |
| Search | `Search` / `MagnifyingGlass` | Query input |
| Check | `Check` / `Checkmark` | Confirmation |
| Info | `Info` / `InformationCircle` | Information |
| Warning | `AlertTriangle` / `ExclamationTriangle` | Caution |
| Error | `AlertCircle` / `XCircle` | Errors |
| [ADD_PROJECT_SPECIFIC_ICONS] | | |

---

## 7. Implementation Checklist

### 7.1 Initial Setup

- [ ] Add CSS custom properties to global stylesheet
- [ ] Configure framework theme to use tokens (if applicable)
- [ ] Install icon package of choice
- [ ] Create component library structure

### 7.2 Core Components

- [ ] Button (Primary, Secondary, Ghost)
- [ ] Card (Base, Interactive)
- [ ] Input (Text, TextArea)
- [ ] Badge / Tag
- [ ] Loading states

### 7.3 Validation

- [ ] Verify color contrast (WCAG AA)
- [ ] Test reduced motion preference
- [ ] Check focus states on all interactive elements
- [ ] Validate responsive behavior

---

## 8. Accessibility Integration

This document provides **visual tokens and CSS implementation**. For comprehensive accessibility guidance, see:

- **[ACCESSIBILITY.md](architecture/ACCESSIBILITY.md)** - Full WCAG 2.1 AA checklist, screen reader patterns, testing protocols

### 8.1 What This Document Covers

| Concern | Implementation Here | Details in ACCESSIBILITY.md |
|---------|--------------------|-----------------------------|
| **Color contrast** | Token definitions (Section 1) | WCAG criteria validation, testing tools |
| **Focus states** | CSS implementation (Section 4.3) | Focus management strategy, tab order |
| **Reduced motion** | CSS media query (Section 1) | Cognitive load patterns, alternatives |
| **Component ARIA** | — | Full ARIA requirements per component |
| **Screen readers** | — | Live regions, announcements, testing scripts |
| **Keyboard navigation** | — | Shortcuts, focus traps, skip links |

### 8.2 Required Accessibility Tokens

Ensure these tokens meet accessibility requirements:

```css
/* Focus ring - must have 3:1 contrast against background */
--ring: [FOCUS_RING_COLOR];
--ring-offset: 2px;

/* Error states - must not rely on color alone */
--color-error: [ERROR_COLOR];        /* Visual indicator */
--color-error-subtle: [ERROR_BG];    /* Background for error states */
/* Always pair with icon + text, never color alone */
```

### 8.3 Cross-Reference Checklist

Before finalizing this document, verify alignment with ACCESSIBILITY.md:

- [ ] Color tokens pass WCAG AA contrast (4.5:1 text, 3:1 UI)
- [ ] Focus ring color has 3:1 contrast against all backgrounds
- [ ] Reduced motion tokens set transitions to 0ms
- [ ] Component specs include focus state definitions
- [ ] Loading states have non-motion alternatives

---

## Quick Copy Reference

### Essential Token Usage

```css
/* Backgrounds */
background-color: var(--color-background);    /* Page background */
background-color: var(--color-surface);       /* Cards, modals */
background-color: var(--color-accent);        /* CTA buttons */

/* Text */
color: var(--color-text-primary);             /* Main text */
color: var(--color-text-secondary);           /* Secondary text */
color: var(--color-text-muted);               /* Placeholder, hints */

/* Borders */
border-color: var(--color-border);            /* Default borders */
border-color: var(--color-accent);            /* Accent borders */

/* Font Families */
font-family: var(--font-display);             /* Headers */
font-family: var(--font-body);                /* Content */
font-family: var(--font-mono);                /* Code */

/* Shadows */
box-shadow: var(--shadow-md);                 /* Card elevation */
box-shadow: var(--shadow-lg);                 /* Card hover */

/* Transitions */
transition: all var(--transition-base);       /* 150ms */
transition: all var(--transition-slow);       /* 200ms */
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDER]` values with your design tokens
2. Use colors from BRAND_GUIDELINES.md
3. Use typography from BRAND_GUIDELINES.md
4. Add project-specific components
5. Add project-specific icons
6. Test accessibility (WCAG AA compliance)

**Good design system documentation:**

- Is copy-paste ready
- Uses semantic token names (not raw hex values)
- Includes responsive patterns
- Documents accessibility requirements
- Aligns with BRAND_GUIDELINES.md

---

## Validation Schema (For AI Generation)

```yaml
# UI_DESIGN_SYSTEM validation gate
inputs_required:
  - brand.colors: from_brand_guidelines
  - brand.typography: from_brand_guidelines
  - brand.design_principles: from_brand_guidelines

outputs_produced:
  - ui.css_tokens: used_by_implementation
  - ui.framework_config: used_by_implementation
  - ui.component_specs: used_by_implementation
  - ui.utility_classes: used_by_implementation

validation_gate:
  required_sections:
    - "CSS Custom Properties"
    - "Framework Integration"
    - "Component Specifications"
    - "Implementation Checklist"

  minimum_content:
    color_tokens: 10
    typography_tokens: 5
    component_specs: 5

  placeholders_filled:
    - "[BACKGROUND_COLOR]"
    - "[ACCENT_COLOR]"
    - "[DISPLAY_FONT_STACK]"

cross_references:
  - color_tokens: must_match: brand.colors
  - typography: must_match: brand.typography
  - component_styles: must_reflect: brand.design_principles
  - focus_implementation: must_align_with: accessibility.focus_management
  - reduced_motion: must_align_with: accessibility.cognitive_load_patterns
  - color_contrast: must_pass: accessibility.wcag_checklist

quality_checks:
  - colors: wcag_aa_contrast
  - tokens: semantic_naming
  - components: include_focus_states
  - accessibility_integration: section_present

decision_prompts:
  - "What CSS approach are you using? (utility-first, BEM, CSS-in-JS, vanilla)"
  - "What icon library do you prefer? (Lucide, Heroicons, Phosphor, custom)"
  - "What are your primary, secondary, and accent colors?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] Colors match BRAND_GUIDELINES.md
- [ ] Typography matches BRAND_GUIDELINES.md
- [ ] All components have focus states
- [ ] Reduced motion preference handled

---

© [YEAR] [AUTHOR/ORG]
