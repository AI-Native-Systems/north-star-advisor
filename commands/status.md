# /northstar:status

Display current generation progress and next steps.

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

GENERATION PROGRESS
───────────────────────────────────────────────────────

Phase 1:  BRAND_GUIDELINES         ✓ Complete
Phase 2:  NORTHSTAR                ✓ Complete
Phase 3:  COMPETITIVE_LANDSCAPE    ✓ Complete
Phase 4:  NORTHSTAR_EXTRACT        ✓ Complete
Phase 5a: USER_JOURNEYS            ○ Pending (--ux)
Phase 5b: UI_DESIGN_SYSTEM         ○ Pending (--ux)
Phase 5c: ACCESSIBILITY            ○ Pending (--ux)
Phase 6:  ARCHITECTURE_BLUEPRINT   ▶ In Progress
Phase 7:  AGENT_PROMPTS            ○ Pending
Phase 8:  SECURITY_ARCHITECTURE    ○ Pending
Phase 9:  ADR                      ○ Pending
Phase 10: POST_DEPLOYMENT          ○ Pending
Phase 11: STRATEGIC_RECOMMENDATION ○ Pending
Phase 12: ACTION_ROADMAP           ○ Pending
Phase 13: INDEX                    ○ Pending

Progress: ████████░░░░░░░░░░░░ 5/15 (33%)

VALIDATION STATUS
───────────────────────────────────────────────────────

✓ Phase 1: All gates passed
✓ Phase 2: All gates passed
✓ Phase 3: All gates passed
✓ Phase 4: All gates passed
⚠ Phase 6: In progress

OUTPUTS
───────────────────────────────────────────────────────

north-star-advisor/docs/
├── BRAND_GUIDELINES.md          (2.3 KB)
├── NORTHSTAR.md                 (4.1 KB)
├── COMPETITIVE_LANDSCAPE.md     (5.7 KB)
├── NORTHSTAR_EXTRACT.md         (1.8 KB)
└── ARCHITECTURE_BLUEPRINT.md    (partial)

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
