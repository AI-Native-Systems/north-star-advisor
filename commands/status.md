---
description: Display current research and generation progress with next steps.
---

# /northstar:status

Display current generation progress and next steps.

---

## Template Source of Truth

**CRITICAL:** Read `templates/index.yml` for the authoritative template list.

- **NEVER** display document names not in `templates/index.yml`
- Use `counts` section from index.yml for progress calculations

---

## Usage

```
/northstar:status
```

---

## Workflow

### Step 1: Check for Project

1. Check if `north-star-advisor/.work-in-progress/state.json` exists
2. If not exists:
   ```
   No North Star project found.
   Run /northstar:advisor to start a new project.
   ```
3. If exists, proceed to Step 2

### Step 2: Load State

1. Read `north-star-advisor/.work-in-progress/state.json`
2. Read `north-star-advisor/.work-in-progress/inputs.yml`
3. Calculate completion statistics

### Step 3: Display Status

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► STATUS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project: <application_name>
Created: <created_at>
Updated: <updated_at>

Flags: <--ux --deep or "core templates only">

RESEARCH STATUS
───────────────────────────────────────────────────────

Research: ✓ Complete (cached 2 hours ago)
  • Tech Stack:    Next.js 14, PostgreSQL, Clerk Auth
  • Features:      Dashboard, API, Export
  • Architecture:  Hybrid orchestration pattern
  • Pitfalls:      3 security concerns identified

GENERATION PROGRESS (from templates/index.yml)
───────────────────────────────────────────────────────

[List all templates from templates/index.yml]
[Format: Phase N: TEMPLATE_NAME    [status]]
[Filter by flag based on project settings]
[Status: ✓ Complete | ▶ In Progress | ○ Pending | ⊘ Skipped]

Progress: [progress bar] [completed]/[total] ([percent]%)

VALIDATION STATUS
───────────────────────────────────────────────────────

[List validation status for completed phases]

OUTPUTS (actual files in north-star-advisor/docs/)
───────────────────────────────────────────────────────

[Glob north-star-advisor/docs/**/*.md and list actual files]
[Only show files that exist - do not list expected files]

LAST CHECKPOINT
───────────────────────────────────────────────────────

Saved: <timestamp>
Phase: 5 - USER_JOURNEYS (before)

▶ Next: Run /northstar:advisor-build to continue
         Run /northstar:resume --restart to restart current phase
```

---

## Status Indicators

| Symbol | Meaning |
|--------|---------|
| ✓ | Phase completed and validated |
| ▶ | Currently in progress |
| ○ | Pending (not yet started) |
| ✗ | Failed validation |
| ⊘ | Skipped (not applicable based on flags) |

---

## Conditional Display

### When --ux flag is NOT set:
```
Phase 5a: USER_JOURNEYS            ⊘ Skipped (requires --ux)
Phase 5b: UI_DESIGN_SYSTEM         ⊘ Skipped (requires --ux)
Phase 5c: ACCESSIBILITY            ⊘ Skipped (requires --ux)
```

### When --deep flag is NOT set:
```
Deep Templates:                    ⊘ Skipped (requires --deep)
  - PIPELINE_ORCHESTRATION
  - RESILIENCE_PATTERNS
  - IMPLEMENTATION_SCAFFOLD
  - OBSERVABILITY
  - TESTING_STRATEGY
  - HANDOFF_PROTOCOL
```

### When validation failed:
```
Phase 6:  ARCHITECTURE_BLUEPRINT   ✗ Validation Failed
  Error: Agent topology not defined
  Fix: Re-run /northstar:advisor-build --only 6
```

---

## Tools Used

- `Read` - Load state and calculate statistics
- `Glob` - List output files and sizes
