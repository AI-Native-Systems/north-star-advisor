# [PROJECT_NAME]: Accessibility Strategy

<!-- GENERATION: This is Step 5c (with --ux flag). Generate after USER_JOURNEYS, parallel with UI_DESIGN_SYSTEM. See GENERATION_MANIFEST.md -->

> **Parent**: [USER_JOURNEYS.md](USER_JOURNEYS.md)
> **Related**: [UI_DESIGN_SYSTEM.md](UI_DESIGN_SYSTEM.md)
> **Generation Step**: 5c of 13 — Requires `journeys.*`
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **WCAG Target**: 2.1 Level AA

Comprehensive accessibility patterns for [PROJECT_DESCRIPTION], including streaming UI, focus management, and inclusive design.

---

## Document Purpose

This document defines **accessibility as a first-class citizen** in [PROJECT_NAME]. It provides:

1. WCAG 2.1 AA compliance checklist
2. Screen reader patterns for streaming/async UI
3. Focus management during [CORE_ASYNC_OPERATION]
4. Cognitive load mitigation strategies
5. Testing protocols and tooling

**Design Principle**: Accessibility is not a feature—it's the foundation. Every pattern here enables users to accomplish their goals regardless of ability.

**Relationship to Other Documents:**
- **UI_DESIGN_SYSTEM.md** implements the visual tokens (focus rings, colors)
- **USER_JOURNEYS.md** maps journey phases to accessibility concerns
- **This document** provides comprehensive patterns and testing protocols

---

## 1. WCAG 2.1 AA Compliance Checklist

### 1.1 Perceivable

| Criterion | Requirement | Implementation | Status |
|-----------|-------------|----------------|--------|
| **1.1.1 Non-text Content** | All images have alt text | `alt` on all `<img>`, `aria-label` on icon buttons | [TODO] |
| **1.3.1 Info and Relationships** | Semantic HTML structure | Heading hierarchy, landmark regions, form labels | [TODO] |
| **1.3.2 Meaningful Sequence** | Reading order matches visual order | DOM order reflects visual layout | [TODO] |
| **1.3.4 Orientation** | No orientation lock | Responsive design, no `orientation: portrait` lock | [TODO] |
| **1.3.5 Identify Input Purpose** | Input autocomplete attributes | `autocomplete` on form fields collecting user data | [TODO] |
| **1.4.1 Use of Color** | Color not sole indicator | Icons + text + color for status | [TODO] |
| **1.4.3 Contrast (Minimum)** | 4.5:1 text, 3:1 large text | [COLOR_SYSTEM] verified | [TODO] |
| **1.4.4 Resize Text** | Readable at 200% zoom | Responsive typography, no fixed heights | [TODO] |
| **1.4.10 Reflow** | No horizontal scroll at 320px | Responsive breakpoints, stack layouts | [TODO] |
| **1.4.11 Non-text Contrast** | 3:1 for UI components | Focus rings, borders, icons verified | [TODO] |
| **1.4.12 Text Spacing** | No loss of content when spaced | No overflow:hidden on text containers | [TODO] |
| **1.4.13 Content on Hover/Focus** | Dismissible, hoverable, persistent | Tooltips remain visible, Esc to dismiss | [TODO] |

### 1.2 Operable

| Criterion | Requirement | Implementation | Status |
|-----------|-------------|----------------|--------|
| **2.1.1 Keyboard** | All functionality keyboard accessible | Tab navigation, Enter/Space activation | [TODO] |
| **2.1.2 No Keyboard Trap** | Focus can always escape | Modal focus trap with Esc escape | [TODO] |
| **2.4.1 Bypass Blocks** | Skip to main content | Skip link, landmark regions | [TODO] |
| **2.4.2 Page Titled** | Descriptive page titles | Dynamic titles reflecting state | [TODO] |
| **2.4.3 Focus Order** | Logical tab sequence | DOM order matches visual flow | [TODO] |
| **2.4.4 Link Purpose** | Link text is descriptive | No "click here", context-aware links | [TODO] |
| **2.4.6 Headings and Labels** | Descriptive headings | Semantic heading hierarchy | [TODO] |
| **2.4.7 Focus Visible** | Visible focus indicator | 2px ring, 3:1 contrast | [TODO] |
| **2.5.1 Pointer Gestures** | Single-pointer alternatives | No swipe-only gestures | [TODO] |
| **2.5.2 Pointer Cancellation** | Cancel on up-event | `onClick` not `onMouseDown` | [TODO] |
| **2.5.3 Label in Name** | Visible label in accessible name | Button text matches `aria-label` | [TODO] |
| **2.5.4 Motion Actuation** | No shake/tilt requirements | Standard input only | [TODO] |

### 1.3 Understandable

| Criterion | Requirement | Implementation | Status |
|-----------|-------------|----------------|--------|
| **3.1.1 Language of Page** | `lang` attribute on `<html>` | `lang="[LANGUAGE_CODE]"` | [TODO] |
| **3.1.2 Language of Parts** | `lang` on foreign text | Mark any non-[PRIMARY_LANGUAGE] content | [TODO] |
| **3.2.1 On Focus** | No context change on focus | Focus doesn't trigger navigation | [TODO] |
| **3.2.2 On Input** | No unexpected context change | Form submission explicit | [TODO] |
| **3.2.3 Consistent Navigation** | Same nav across pages | Persistent header/sidebar | [TODO] |
| **3.2.4 Consistent Identification** | Same function = same label | Button labels consistent | [TODO] |
| **3.3.1 Error Identification** | Errors identified in text | Error messages adjacent to fields | [TODO] |
| **3.3.2 Labels or Instructions** | Form fields have labels | Visible labels, not placeholder-only | [TODO] |
| **3.3.3 Error Suggestion** | Suggest corrections | "Did you mean...?" patterns | [TODO] |
| **3.3.4 Error Prevention** | Confirm/review submissions | Review step before [CORE_ACTION] | [TODO] |

### 1.4 Robust

| Criterion | Requirement | Implementation | Status |
|-----------|-------------|----------------|--------|
| **4.1.1 Parsing** | Valid HTML | No duplicate IDs, proper nesting | [TODO] |
| **4.1.2 Name, Role, Value** | Custom components have ARIA | Proper roles on custom widgets | [TODO] |
| **4.1.3 Status Messages** | Status announced without focus | `aria-live` regions | [TODO] |

---

## 2. Screen Reader Patterns for Streaming UI

### 2.1 The Challenge

[PROJECT_NAME] involves:
- **[EXPECTED_WAIT_TIME] P95 processing time** with [NUM_STEPS] sequential steps
- **Streaming content** that updates in real-time
- **Progress states** that change frequently
- **Status messages** that appear and disappear

Screen reader users need to be informed of progress **without being overwhelmed**.

### 2.2 Live Region Strategy

```typescript
// [PATH_TO_A11Y_UTILS]

/**
 * ARIA Live Region Strategy for [CORE_ASYNC_OPERATION]
 *
 * Principle: Polite announcements for progress, assertive for errors only
 */

export const LIVE_REGION_CONFIG = {
  // Progress updates - polite, coalesced
  progress: {
    role: 'status',
    'aria-live': 'polite',
    'aria-atomic': 'true',
    announceInterval: [ANNOUNCEMENT_INTERVAL_MS], // Max once per N seconds
  },

  // Step completion - polite, immediate
  stepComplete: {
    role: 'status',
    'aria-live': 'polite',
    'aria-atomic': 'true',
  },

  // Errors - assertive, immediate
  error: {
    role: 'alert',
    'aria-live': 'assertive',
    'aria-atomic': 'true',
  },

  // Final completion - assertive (important milestone)
  complete: {
    role: 'alert',
    'aria-live': 'assertive',
    'aria-atomic': 'true',
  },
} as const;
```

### 2.3 Progress Announcement Patterns

**Problem**: Announcing every streaming chunk would overwhelm users.

**Solution**: Coalesced progress announcements at meaningful intervals.

```tsx
// [PATH_TO_PROGRESS_COMPONENT]

'use client';

import { useEffect, useRef, useState } from 'react';

interface AccessibleProgressProps {
  currentStep: number;
  totalSteps: number;
  currentStepName: string;
  status: 'pending' | 'processing' | 'complete' | 'error';
  percentage: number;
}

export function AccessibleProgress({
  currentStep,
  totalSteps,
  currentStepName,
  status,
  percentage,
}: AccessibleProgressProps) {
  const [announcement, setAnnouncement] = useState('');
  const lastAnnouncementRef = useRef<number>(0);
  const ANNOUNCEMENT_INTERVAL = [ANNOUNCEMENT_INTERVAL_MS];

  useEffect(() => {
    const now = Date.now();
    const timeSinceLastAnnouncement = now - lastAnnouncementRef.current;

    // Coalesce progress updates to avoid overwhelming screen readers
    if (status === 'processing' && timeSinceLastAnnouncement >= ANNOUNCEMENT_INTERVAL) {
      setAnnouncement(
        `Processing step ${currentStep} of ${totalSteps}: ${currentStepName}. ${percentage}% complete.`
      );
      lastAnnouncementRef.current = now;
    }

    // Always announce step completion immediately
    if (status === 'complete') {
      setAnnouncement(
        `Step ${currentStep} complete: ${currentStepName}. Moving to next step.`
      );
      lastAnnouncementRef.current = now;
    }
  }, [currentStep, totalSteps, currentStepName, status, percentage]);

  return (
    <>
      {/* Progress bar with ARIA */}
      <div
        role="progressbar"
        aria-valuenow={percentage}
        aria-valuemin={0}
        aria-valuemax={100}
        aria-label={`[OPERATION_NAME] progress: ${percentage}% complete`}
        aria-describedby="progress-description"
        className="[PROGRESS_BAR_CLASSES]"
      >
        <div
          className="[PROGRESS_FILL_CLASSES]"
          style={{ width: `${percentage}%` }}
        />
      </div>

      {/* Hidden description for context */}
      <div id="progress-description" className="sr-only">
        [OPERATION_DESCRIPTION]. Currently on step {currentStep}.
      </div>

      {/* Live region for announcements - coalesced updates */}
      <div
        role="status"
        aria-live="polite"
        aria-atomic="true"
        className="sr-only"
      >
        {announcement}
      </div>
    </>
  );
}
```

### 2.4 Error Announcement Pattern

```tsx
// [PATH_TO_ERROR_COMPONENT]

interface AccessibleErrorProps {
  message: string;
  recoverable: boolean;
  onRetry?: () => void;
  onDismiss?: () => void;
}

export function AccessibleError({
  message,
  recoverable,
  onRetry,
  onDismiss,
}: AccessibleErrorProps) {
  const headingRef = useRef<HTMLHeadingElement>(null);

  // Move focus to error heading for immediate awareness
  useEffect(() => {
    headingRef.current?.focus();
  }, []);

  return (
    <div
      role="alertdialog"
      aria-labelledby="error-heading"
      aria-describedby="error-description"
      className="[ERROR_CONTAINER_CLASSES]"
    >
      <h2
        id="error-heading"
        ref={headingRef}
        tabIndex={-1}
        className="[ERROR_HEADING_CLASSES]"
      >
        [OPERATION_NAME] Error
      </h2>

      <p id="error-description" className="[ERROR_MESSAGE_CLASSES]">
        {message}
      </p>

      {recoverable && (
        <div className="[ERROR_ACTIONS_CLASSES]" role="group" aria-label="Error recovery actions">
          <button onClick={onRetry} className="[PRIMARY_BUTTON_CLASSES]">
            Retry [OPERATION_NAME]
          </button>
          <button onClick={onDismiss} className="[SECONDARY_BUTTON_CLASSES]">
            Dismiss
          </button>
        </div>
      )}
    </div>
  );
}
```

---

## 3. Focus Management During [CORE_ASYNC_OPERATION]

### 3.1 Focus Strategy by State

| State | Focus Behavior | Rationale |
|-------|----------------|-----------|
| **Form submission** | Retain on submit button | User may want to correct input |
| **[OPERATION] starts** | Move to progress region | Confirm action was received |
| **[OPERATION] progress** | No movement | Don't interrupt user |
| **Step complete** | Optional: move to viewer | User may want to preview |
| **All complete** | Move to results heading | Announce completion, show results |
| **Error occurs** | Move to error heading | Immediate awareness |

### 3.2 Implementation Pattern

```tsx
// [PATH_TO_FOCUS_HOOK]

import { useRef, useEffect } from 'react';

type OperationState = 'idle' | 'starting' | 'processing' | 'complete' | 'error';

export function useFocusManagement(state: OperationState) {
  const progressRef = useRef<HTMLDivElement>(null);
  const resultsRef = useRef<HTMLHeadingElement>(null);
  const errorRef = useRef<HTMLHeadingElement>(null);
  const previousStateRef = useRef<OperationState>('idle');

  useEffect(() => {
    const previousState = previousStateRef.current;

    // Only manage focus on state transitions
    if (previousState === state) return;

    switch (state) {
      case 'starting':
        // Move focus to progress region when operation begins
        progressRef.current?.focus();
        break;

      case 'complete':
        // Move focus to results heading when complete
        resultsRef.current?.focus();
        break;

      case 'error':
        // Move focus to error heading for immediate awareness
        errorRef.current?.focus();
        break;

      // 'idle' and 'processing' don't change focus
    }

    previousStateRef.current = state;
  }, [state]);

  return {
    progressRef,
    resultsRef,
    errorRef,
  };
}
```

### 3.3 Skip Links for Long Operations

During [EXPECTED_WAIT_TIME] operations, users may want to skip to other content:

```tsx
// [PATH_TO_SKIP_LINKS_COMPONENT]

export function OperationSkipLinks() {
  return (
    <div className="sr-only focus-within:not-sr-only">
      <a href="#main-content" className="[SKIP_LINK_CLASSES]">
        Skip to main content
      </a>
      <a href="#[RESULTS_SECTION_ID]" className="[SKIP_LINK_CLASSES]">
        Skip to [RESULTS_NAME]
      </a>
      <a href="#[CONTROLS_SECTION_ID]" className="[SKIP_LINK_CLASSES]">
        Skip to [OPERATION_NAME] controls
      </a>
    </div>
  );
}
```

---

## 4. Cognitive Load Mitigation

### 4.1 Progressive Disclosure

[PROJECT_NAME] may present [NUM_ITEMS] items. Avoid overwhelming users:

```tsx
// [PATH_TO_PROGRESSIVE_VIEWER]

interface ProgressiveViewerProps {
  items: GeneratedItem[];
}

export function ProgressiveViewer({ items }: ProgressiveViewerProps) {
  const [expandedId, setExpandedId] = useState<string | null>(null);

  return (
    <div role="region" aria-label="[ITEMS_LABEL]">
      {/* Summary first */}
      <div className="[SUMMARY_CLASSES]">
        <h2>[RESULTS_HEADING]</h2>
        <p>{items.length} [ITEMS_NAME] generated</p>
        <p>[QUALITY_METRIC_NAME]: {calculateQuality(items)}/100</p>
      </div>

      {/* Expandable list - one at a time */}
      <ul role="list" className="[LIST_CLASSES]">
        {items.map((item) => (
          <li key={item.id}>
            <button
              aria-expanded={expandedId === item.id}
              aria-controls={`content-${item.id}`}
              onClick={() => setExpandedId(expandedId === item.id ? null : item.id)}
              className="[ACCORDION_BUTTON_CLASSES]"
            >
              <span className="font-semibold">{item.name}</span>
              <span className="sr-only">
                {expandedId === item.id ? ', expanded' : ', collapsed'}
              </span>
            </button>

            <div
              id={`content-${item.id}`}
              role="region"
              aria-labelledby={`heading-${item.id}`}
              hidden={expandedId !== item.id}
              className="[ACCORDION_CONTENT_CLASSES]"
            >
              {item.content}
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 4.2 Time Expectation Setting

```tsx
// [PATH_TO_TIME_EXPECTATION_COMPONENT]

interface TimeExpectationProps {
  estimatedMinutes: number;
  currentMinutes: number;
}

export function TimeExpectation({ estimatedMinutes, currentMinutes }: TimeExpectationProps) {
  const remainingMinutes = Math.max(0, estimatedMinutes - currentMinutes);

  return (
    <div role="timer" aria-label="Estimated time remaining">
      <p>
        <span className="font-semibold">Estimated time:</span>{' '}
        <span aria-live="polite">
          {remainingMinutes > 0
            ? `About ${remainingMinutes} minute${remainingMinutes !== 1 ? 's' : ''} remaining`
            : 'Almost done...'}
        </span>
      </p>

      {/* Help users decide if they want to wait */}
      <p className="[HINT_TEXT_CLASSES]">
        You can leave this page open while we [OPERATION_VERB] your [ITEMS_NAME].
        We'll notify you when complete.
      </p>
    </div>
  );
}
```

### 4.3 Reduced Motion Support

```css
/* globals.css */

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }

  /* Keep essential progress indication without motion */
  .progress-bar {
    transition: none;
  }

  /* Static loading indicator instead of spinning */
  .loading-spinner {
    animation: none;
  }
  .loading-spinner::after {
    content: 'Loading...';
  }

  /* Fade-in sections appear instantly */
  .fade-in-section {
    opacity: 1;
    transform: none;
  }
}
```

---

## 5. Form Accessibility

### 5.1 Multi-Step Form Pattern

```tsx
// [PATH_TO_STEP_INDICATOR]

interface StepIndicatorProps {
  steps: { id: string; label: string }[];
  currentStep: number;
}

export function AccessibleStepIndicator({ steps, currentStep }: StepIndicatorProps) {
  return (
    <nav aria-label="Form progress">
      <ol role="list" className="[STEP_LIST_CLASSES]">
        {steps.map((step, index) => {
          const isComplete = index < currentStep;
          const isCurrent = index === currentStep;
          const isPending = index > currentStep;

          return (
            <li
              key={step.id}
              aria-current={isCurrent ? 'step' : undefined}
              className="[STEP_ITEM_CLASSES]"
            >
              {/* Step number with status */}
              <span
                className={cn(
                  '[STEP_NUMBER_CLASSES]',
                  isComplete && '[COMPLETE_CLASSES]',
                  isCurrent && '[CURRENT_CLASSES]',
                  isPending && '[PENDING_CLASSES]'
                )}
                aria-hidden="true"
              >
                {isComplete ? '✓' : index + 1}
              </span>

              {/* Step label */}
              <span
                className={cn(
                  isCurrent && 'font-semibold',
                  isPending && '[MUTED_CLASSES]'
                )}
              >
                {step.label}
                <span className="sr-only">
                  {isComplete && ' (completed)'}
                  {isCurrent && ' (current step)'}
                  {isPending && ' (not started)'}
                </span>
              </span>
            </li>
          );
        })}
      </ol>

      {/* Screen reader summary */}
      <div className="sr-only" aria-live="polite">
        Step {currentStep + 1} of {steps.length}: {steps[currentStep].label}
      </div>
    </nav>
  );
}
```

### 5.2 Accessible Input with Error Association

```tsx
// [PATH_TO_ACCESSIBLE_INPUT]

interface AccessibleInputProps {
  id: string;
  label: string;
  hint?: string;
  error?: string;
  required?: boolean;
}

export function AccessibleInput({
  id,
  label,
  hint,
  error,
  required,
  ...props
}: AccessibleInputProps & React.InputHTMLAttributes<HTMLInputElement>) {
  const hintId = hint ? `${id}-hint` : undefined;
  const errorId = error ? `${id}-error` : undefined;
  const describedBy = [hintId, errorId].filter(Boolean).join(' ') || undefined;

  return (
    <div className="[INPUT_WRAPPER_CLASSES]">
      <label htmlFor={id} className="[LABEL_CLASSES]">
        {label}
        {required && (
          <span className="[REQUIRED_INDICATOR_CLASSES]" aria-hidden="true">
            *
          </span>
        )}
        {required && <span className="sr-only">(required)</span>}
      </label>

      {hint && (
        <p id={hintId} className="[HINT_CLASSES]">
          {hint}
        </p>
      )}

      <input
        id={id}
        aria-required={required}
        aria-invalid={error ? 'true' : undefined}
        aria-describedby={describedBy}
        className={cn(
          '[INPUT_CLASSES]',
          error && '[INPUT_ERROR_CLASSES]'
        )}
        {...props}
      />

      {error && (
        <p id={errorId} className="[ERROR_MESSAGE_CLASSES]" role="alert">
          <span aria-hidden="true">[ERROR_ICON] </span>
          {error}
        </p>
      )}
    </div>
  );
}
```

---

## 6. Keyboard Navigation

### 6.1 Keyboard Shortcuts

```tsx
// [PATH_TO_KEYBOARD_NAVIGATION]

const KEYBOARD_SHORTCUTS = {
  'j': 'Next [ITEM]',
  'k': 'Previous [ITEM]',
  'e': 'Export current [ITEM]',
  'E': 'Export all [ITEMS]',
  '?': 'Show keyboard shortcuts',
  'Escape': 'Close viewer',
} as const;

export function useKeyboardNavigation(
  items: Item[],
  currentIndex: number,
  setCurrentIndex: (index: number) => void
) {
  useEffect(() => {
    function handleKeyDown(event: KeyboardEvent) {
      // Don't capture when typing in inputs
      if (
        event.target instanceof HTMLInputElement ||
        event.target instanceof HTMLTextAreaElement
      ) {
        return;
      }

      switch (event.key) {
        case 'j':
          setCurrentIndex(Math.min(currentIndex + 1, items.length - 1));
          break;
        case 'k':
          setCurrentIndex(Math.max(currentIndex - 1, 0));
          break;
        case '?':
          // Show shortcuts dialog
          break;
      }
    }

    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, [currentIndex, items.length, setCurrentIndex]);
}

// Shortcuts help dialog
export function KeyboardShortcutsDialog() {
  return (
    <div
      role="dialog"
      aria-labelledby="shortcuts-title"
      aria-describedby="shortcuts-description"
    >
      <h2 id="shortcuts-title">Keyboard Shortcuts</h2>
      <p id="shortcuts-description" className="sr-only">
        Available keyboard shortcuts for [ITEMS_NAME] navigation
      </p>

      <dl className="[SHORTCUTS_LIST_CLASSES]">
        {Object.entries(KEYBOARD_SHORTCUTS).map(([key, description]) => (
          <div key={key} className="contents">
            <dt>
              <kbd className="[KBD_CLASSES]">{key}</kbd>
            </dt>
            <dd>{description}</dd>
          </div>
        ))}
      </dl>
    </div>
  );
}
```

### 6.2 Focus Trap for Modals

```tsx
// [PATH_TO_FOCUS_TRAP_HOOK]

import { useEffect, useRef } from 'react';

export function useFocusTrap(isActive: boolean) {
  const containerRef = useRef<HTMLDivElement>(null);
  const previousFocusRef = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (!isActive || !containerRef.current) return;

    // Store previous focus to restore later
    previousFocusRef.current = document.activeElement as HTMLElement;

    // Find all focusable elements
    const focusableElements = containerRef.current.querySelectorAll<HTMLElement>(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    const firstElement = focusableElements[0];
    const lastElement = focusableElements[focusableElements.length - 1];

    // Focus first element
    firstElement?.focus();

    function handleKeyDown(event: KeyboardEvent) {
      if (event.key === 'Escape') {
        // Allow escape to close (handled by parent)
        return;
      }

      if (event.key !== 'Tab') return;

      // Trap focus within container
      if (event.shiftKey) {
        if (document.activeElement === firstElement) {
          event.preventDefault();
          lastElement?.focus();
        }
      } else {
        if (document.activeElement === lastElement) {
          event.preventDefault();
          firstElement?.focus();
        }
      }
    }

    document.addEventListener('keydown', handleKeyDown);

    return () => {
      document.removeEventListener('keydown', handleKeyDown);
      // Restore focus when trap is deactivated
      previousFocusRef.current?.focus();
    };
  }, [isActive]);

  return containerRef;
}
```

---

## 7. Testing Protocol

### 7.1 Automated Testing

```typescript
// tests/a11y/accessibility.test.ts

import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

const PAGES_TO_TEST = [
  '/',
  '/login',
  '/signup',
  '[ADD_PROJECT_PAGES]',
];

test.describe('Accessibility', () => {
  for (const path of PAGES_TO_TEST) {
    test(`${path} should have no WCAG 2.1 AA violations`, async ({ page }) => {
      await page.goto(path);

      const results = await new AxeBuilder({ page })
        .withTags(['wcag21aa', 'wcag2aa', 'best-practice'])
        .analyze();

      expect(results.violations).toEqual([]);
    });
  }

  test('[CORE_PAGE] should announce progress to screen readers', async ({ page }) => {
    await page.goto('[CORE_PAGE_PATH]');

    // Check live region exists
    const liveRegion = page.locator('[aria-live="polite"]');
    await expect(liveRegion).toBeAttached();

    // Check progress bar has proper ARIA
    const progressBar = page.locator('[role="progressbar"]');
    await expect(progressBar).toHaveAttribute('aria-valuenow');
    await expect(progressBar).toHaveAttribute('aria-valuemin', '0');
    await expect(progressBar).toHaveAttribute('aria-valuemax', '100');
  });

  test('Form should have proper error association', async ({ page }) => {
    await page.goto('[FORM_PAGE_PATH]');

    // Submit empty form
    await page.click('button[type="submit"]');

    // Check error messages are associated with inputs
    const errorInput = page.locator('input[aria-invalid="true"]');
    const describedBy = await errorInput.getAttribute('aria-describedby');
    expect(describedBy).toBeTruthy();

    const errorMessage = page.locator(`#${describedBy}`);
    await expect(errorMessage).toBeVisible();
  });
});
```

### 7.2 Manual Testing Checklist

| Test | Method | Pass Criteria |
|------|--------|---------------|
| **Keyboard navigation** | Tab through entire page | All interactive elements reachable, logical order |
| **Screen reader** | NVDA/VoiceOver walkthrough | All content announced, live regions work |
| **Zoom 200%** | Browser zoom | No horizontal scroll, content readable |
| **Zoom 400%** | Browser zoom | Content reflows, no loss of function |
| **Reduced motion** | System preference | No animations, progress still visible |
| **High contrast** | Windows High Contrast | Content visible, focus visible |
| **Color alone** | Grayscale filter | Status distinguishable without color |

### 7.3 Screen Reader Test Script

```markdown
## Screen Reader Test Script (VoiceOver)

### Page Load
1. Navigate to [CORE_PAGE_PATH]
2. Verify: Page title announced
3. Verify: Landmark regions announced (header, main, footer)

### Form Navigation
1. Tab through form
2. Verify: Each field label announced
3. Verify: Required fields announced
4. Verify: Hint text announced via aria-describedby

### Form Submission
1. Submit with errors
2. Verify: Error count announced
3. Verify: Focus moves to first error
4. Verify: Error message associated with field

### [OPERATION_NAME] Progress
1. Start [OPERATION]
2. Verify: "[OPERATION_NAME] started" announced
3. Verify: Progress updates announced (not too frequent)
4. Verify: Step completion announced
5. Verify: Final completion announced
6. Verify: Focus moves to results

### [RESULTS_NAME] Viewer
1. Navigate to [RESULTS]
2. Verify: [ITEMS] list announced with count
3. Verify: Current [ITEM] status announced
4. Verify: Keyboard shortcuts work (j/k navigation)
```

---

## 8. Component Library Requirements

### 8.1 Required ARIA Patterns by Component

| Component | Required ARIA | Notes |
|-----------|--------------|-------|
| **Button** | `aria-label` if icon-only | `aria-pressed` for toggle |
| **Progress** | `role="progressbar"` + value attributes | `aria-describedby` for context |
| **Alert** | `role="alert"` or `role="status"` | Assertive for errors only |
| **Dialog** | `role="dialog"` + `aria-labelledby` | Focus trap required |
| **Accordion** | `aria-expanded` + `aria-controls` | `aria-labelledby` on panel |
| **Tabs** | `role="tablist"` + `role="tab"` + `role="tabpanel"` | Arrow key navigation |
| **Menu** | `role="menu"` + `role="menuitem"` | Arrow key navigation |
| **Combobox** | `role="combobox"` + `aria-expanded` | `aria-activedescendant` |
| **Toast** | `role="status"` or `role="alert"` | Auto-dismiss with sufficient time |

### 8.2 Focus Indicator Specification

```css
/* Focus indicator - must meet 3:1 contrast */
:focus-visible {
  outline: 2px solid var(--ring);
  outline-offset: 2px;
}

/* Enhanced focus for keyboard users */
:focus:not(:focus-visible) {
  outline: none;
}

/* High contrast mode support */
@media (forced-colors: active) {
  :focus-visible {
    outline: 3px solid CanvasText;
  }
}
```

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDER]` values with project-specific content
2. Fill in WCAG checklist status for each criterion
3. Adjust code examples to match your component library
4. Add project-specific testing pages
5. Create screen reader test scripts for your core flows

**Key placeholders to replace:**
- `[PROJECT_NAME]` - Your project name
- `[CORE_ASYNC_OPERATION]` - Main async operation (e.g., "document generation")
- `[EXPECTED_WAIT_TIME]` - P95 wait time (e.g., "8-minute")
- `[NUM_STEPS]` - Number of sequential steps
- `[ITEMS_NAME]` - What you're generating (e.g., "documents")
- `[ANNOUNCEMENT_INTERVAL_MS]` - Milliseconds between announcements (e.g., 10000)

---

## Validation Schema (For AI Generation)

```yaml
# ACCESSIBILITY validation gate
inputs_required:
  - ui_design_system.colors: for_contrast_validation
  - ui_design_system.components: for_aria_requirements
  - user_journeys.error_recovery: for_error_announcement_patterns
  - user_journeys.metrics[]: for_processing_wait_patterns

outputs_produced:
  - a11y.wcag_checklist: used_by_testing
  - a11y.screen_reader_patterns: used_by_implementation
  - a11y.focus_management: used_by_implementation
  - a11y.cognitive_load_patterns: used_by_implementation
  - a11y.testing_protocol: used_by_qa

validation_gate:
  required_sections:
    - "WCAG 2.1 AA Compliance Checklist"
    - "Screen Reader Patterns for Streaming UI"
    - "Focus Management"
    - "Cognitive Load Mitigation"
    - "Form Accessibility"
    - "Keyboard Navigation"
    - "Testing Protocol"
    - "Component Library Requirements"

  minimum_content:
    wcag_criteria: 30
    live_region_patterns: 3
    focus_strategies: 4
    test_cases: 5
    component_aria: 5

cross_references:
  - color_contrast: must_reference: ui_design_system.colors
  - motion_patterns: must_reference: ui_design_system.animations
  - error_patterns: must_align_with: user_journeys.error_recovery
  - processing_patterns: must_align_with: user_journeys.metrics[]

decision_prompts:
  - "What is your primary async operation and expected wait time?"
  - "How many sequential steps does your core workflow have?"
  - "What screen readers should be prioritized for testing? (VoiceOver, NVDA, JAWS)"
  - "What is your announcement coalescing interval for long operations?"
```

**After generation, verify:**
- [ ] All 4 WCAG principles covered (Perceivable, Operable, Understandable, Robust)
- [ ] Live region strategy defined for streaming UI
- [ ] Focus management patterns for all operation states
- [ ] Reduced motion support documented
- [ ] Testing protocol includes automated and manual checks
- [ ] Screen reader test script provided
- [ ] Cross-referenced to UI_DESIGN_SYSTEM.md
- [ ] Cross-referenced to USER_JOURNEYS.md

---

© [YEAR] [AUTHOR/ORG]
