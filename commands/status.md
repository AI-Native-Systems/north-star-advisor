---
description: Display current research and generation progress with next steps.
---

# /northstar:status

Display current generation progress and next steps.

---

## Usage

```
/northstar:status
```

---

## Workflow

### Step 1: Check for Project and Load State

1. Check if `north-star-advisor/.work-in-progress/state.json` exists
2. If not exists:
   ```
   No North Star project found.
   Run /northstar:advisor to start a new project.
   ```
3. If exists:
   a. Read state.json
   b. **Legacy state check:** If state.json contains `completed_phases` (pre-2.0.0 format):
      1. Display project name and creation date from state
      2. Display: "⚠ State format: v1.x (pre-2.0.0). Run /northstar:resume to migrate."
      3. **Stop.** Do NOT continue. Do NOT write to state.json.
   c. Get `plugin_index_path` from state.json
   d. Read `templates/index.yml` at that path
   e. Read `north-star-advisor/.work-in-progress/inputs.yml`

If `plugin_index_path` is missing from state.json:
- Glob: `**/northstar/**/templates/index.yml`
- If exactly 1 match: update state.json with found path
- If 0 matches: display "Could not locate `templates/index.yml` from cached state or fallback glob." and stop
- If multiple matches:
  - List all found paths
  - Use `AskUserQuestion` with each path as an option
  - **Stop and wait** for the user's selection
  - Store selected path in state.json as `plugin_index_path`
  - Then continue

### Step 2: Calculate Statistics

1. Use template list from index.yml for expected documents
2. Check `state.ux` and `state.deep` for enabled flags
3. Calculate completion from `completed_templates` array

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

[List validation status for completed templates]

OUTPUTS (actual files in north-star-advisor/docs/)
───────────────────────────────────────────────────────

[Glob north-star-advisor/docs/**/*.md and list actual files]
[Only show files that exist - do not list expected files]

LAST CHECKPOINT
───────────────────────────────────────────────────────

Saved: <timestamp>
Template: USER_JOURNEYS

▶ Next: Run /northstar:advisor-build to continue
         Run /northstar:resume --restart to restart current template
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
Phase 5d: WIREFRAMES               ⊘ Skipped (requires --ux)
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
  - INTELLIGENCE_LAYER
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
