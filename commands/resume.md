# /northstar:resume

Resume generation from the last checkpoint after interruption.

---

## Arguments

```
/northstar:resume [options]
```

| Option | Description |
|--------|-------------|
| `--restart` | Restart current phase from scratch |
| `--rollback` | Go back to previous phase |
| `--checkpoint <name>` | Resume from specific checkpoint |

---

## Prerequisites

- Project must be initialized with `/northstar:advisor`
- At least one checkpoint must exist in `north-star-advisor/.work-in-progress/checkpoints/`

---

## Workflow

### Step 1: Load State

1. Read `north-star-advisor/.work-in-progress/state.json`
2. Check `last_checkpoint` field
3. If no checkpoint exists:
   ```
   No checkpoint found.
   Run /northstar:advisor-build to start generation.
   ```

### Step 2: Display Resume Options

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► RESUME
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Last Session: <timestamp>
Stopped At:   Phase 6 - ARCHITECTURE_BLUEPRINT

AVAILABLE CHECKPOINTS
───────────────────────────────────────────────────────

1. phase-5-northstar-extract     (2024-01-15 14:30)
2. phase-4-competitive-landscape (2024-01-15 14:15)
3. phase-3-northstar             (2024-01-15 14:00)

OPTIONS
───────────────────────────────────────────────────────

▶ Continue from Phase 6 (default)
  Picks up where you left off

○ Restart Phase 6 (--restart)
  Regenerate current phase from scratch

○ Rollback to Phase 5 (--rollback)
  Go back to previous checkpoint

How would you like to proceed?
```

### Step 3: Process Option

#### Default: Continue

1. Load current phase state
2. Resume generation from last step
3. If mid-generation:
   - Check if partial output exists
   - Offer to keep or regenerate

#### --restart

1. Clear current phase outputs
2. Reset phase status to "pending"
3. Start phase from beginning
4. Preserve all previous phases

#### --rollback

1. Load previous checkpoint
2. Reset current phase status
3. Delete current phase outputs (optional)
4. Resume from previous phase

#### --checkpoint <name>

1. Load specified checkpoint
2. Reset all phases after checkpoint
3. Offer to delete outputs after checkpoint
4. Resume from checkpoint phase

### Step 4: Confirm and Execute

```
Resuming from Phase 6: ARCHITECTURE_BLUEPRINT

Previous inputs preserved:
  • Orchestration pattern: Hybrid
  • Agent count: 4

Continue? (Y/n)
```

If confirmed, hand off to `/northstar:advisor-build --from <phase>`

---

## Checkpoint Structure

```json
// north-star-advisor/.work-in-progress/checkpoints/phase-5-northstar-extract.json
{
  "phase": 5,
  "phase_name": "NORTHSTAR_EXTRACT",
  "created_at": "2024-01-15T14:30:00Z",
  "completed_phases": [1, 2, 3, 4, 5],
  "phase_outputs": {
    "1": "north-star-advisor/docs/BRAND_GUIDELINES.md",
    "2": "north-star-advisor/docs/NORTHSTAR.md",
    "3": "north-star-advisor/docs/COMPETITIVE_LANDSCAPE.md",
    "4": "north-star-advisor/docs/NORTHSTAR_EXTRACT.md"
  },
  "inputs_snapshot": {
    "application_name": "My App",
    "phase_decisions": {...}
  }
}
```

---

## Recovery Scenarios

### Scenario 1: Interrupted Mid-Generation

```
Detected: Partial output for Phase 6

north-star-advisor/docs/ARCHITECTURE_BLUEPRINT.md exists but is incomplete.

Options:
1. Continue generation (append to existing)
2. Regenerate from scratch
3. Keep partial and skip to next phase
```

### Scenario 2: Validation Failed

```
Phase 6 failed validation:
  Error: Agent topology section missing

Options:
1. Regenerate Phase 6
2. Manually fix and re-validate
3. Skip validation (not recommended)
```

### Scenario 3: State Corruption

```
Warning: State file appears corrupted.

Attempting recovery from last checkpoint...
Found: phase-5-northstar-extract (2024-01-15 14:30)

Restore from this checkpoint? (Y/n)
```

---

## Error Handling

| Error | Recovery |
|-------|----------|
| No checkpoints | Direct to /northstar:advisor-build |
| Corrupted checkpoint | Try previous checkpoint |
| Missing output files | Offer to regenerate |
| State mismatch | Rebuild state from outputs |

---

## Tools Used

- `Read` - Load state and checkpoints
- `Write` - Update state after resume
- `Glob` - Find available checkpoints
- `AskUserQuestion` - Confirm resume option
