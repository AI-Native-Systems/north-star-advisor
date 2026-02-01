---
description: Resume generation from the last checkpoint after interruption.
---

# /northstar:resume

Resume generation from the last checkpoint after interruption.

---

## Template Source of Truth

**CRITICAL:** Read `templates/index.yml` for the authoritative template list.

To find it:
1. Check `state.json` for `plugin_index_path` (cached location)
2. If not found, use Glob: `**/northstar/**/templates/index.yml`
3. Update `state.json` with found path for future use

- **NEVER** hallucinate document names not in `templates/index.yml`
- **ALWAYS** read this file to determine expected vs generated documents
- Filter templates by `flag` field based on project's stored flags (ux, deep)

---

## Arguments

```
/northstar:resume [options]
```

| Option | Description |
|--------|-------------|
| `--ux` | Add UX templates. Updates state.json. |
| `--deep` | Add deep architecture templates. Updates state.json. |
| `--full` | Enable all templates (--ux + --deep). Updates state.json. |
| `--search-tool <tool>` | Override search tool. Updates state.json. |
| `--restart` | Restart current phase from scratch |
| `--rollback` | Go back to previous phase |
| `--checkpoint <name>` | Resume from specific checkpoint |

---

## Prerequisites

- Project must be initialized with `/northstar:advisor`
- At least one checkpoint must exist in `north-star-advisor/.work-in-progress/checkpoints/`

---

## Workflow

### Step 1: Load State and Determine Expected Documents

1. Read `templates/index.yml` to get the authoritative template list
2. Read `north-star-advisor/.work-in-progress/state.json`
3. **Update state.json if command-line flags were passed:**
   - If `--full`: set `ux: true` and `deep: true`
   - If `--ux`: set `ux: true`
   - If `--deep`: set `deep: true`
   - If `--search-tool <tool>`: set `search_tool: "<tool>"`
   - Write updated state.json immediately
4. Check which flags are now enabled: `ux`, `deep`
5. **Filter templates from index.yml:**
   - Include all templates where `flag: null` (core)
   - If `ux: true`: include templates where `flag: ux`
   - If `deep: true`: include templates where `flag: deep`
6. Glob `north-star-advisor/docs/**/*.md` to find generated documents
7. Compare expected (from index.yml) vs generated to calculate remaining
8. **NEVER invent document names - only use names from templates/index.yml**

If no state exists:
```
No project found.
Run /northstar:advisor to start a new project.
```

### Step 1.5: Verify Existing Checkpoints (REQUIRED)

**Before resuming, verify all previously generated files still exist.**

```
1. Read all checkpoint files from:
   north-star-advisor/.work-in-progress/checkpoints/checkpoint-phase-*.json

2. For each checkpoint:
   a. Read checkpoint.output_path
   b. Check if file exists
   c. Check if file size > 0
   d. Record: { phase, template_name, exists, size }

3. If any checkpoint files are missing:
   Display warning:

   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    WARNING: Missing Checkpoint Files
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

   The following previously generated files are missing:

   ✗ Phase 2: NORTHSTAR.md
   ✗ Phase 3: COMPETITIVE_LANDSCAPE.md

   Options:
   1. Regenerate missing files before continuing
   2. Continue anyway (these phases will be re-run)

4. Use AskUserQuestion to let user decide
5. If regenerate: add missing phases to generation queue
6. Update state.json to reflect actual state
```

### Step 2: Display Resume Status

**CRITICAL:** Read `templates/index.yml` for template names. Only display documents that exist in index.yml.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► RESUME
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project: [application_name]
Last Session: <timestamp>
Search Tool: [search_tool or "WebSearch (default)"]

CURRENT FLAGS
───────────────────────────────────────────────────────

  --ux:    [enabled/disabled]
  --deep:  [enabled/disabled]

PROGRESS (X/Y documents) - from templates/index.yml
───────────────────────────────────────────────────────

Core:
  [list templates from index.yml where flag: null]
  [✓ for generated, ○ for pending]

UX: [if --ux enabled, else "Not enabled (use --ux to add)"]
  [list templates from index.yml where flag: ux]

Deep: [if --deep enabled, else "Not enabled (use --deep to add)"]
  [list templates from index.yml where flag: deep]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

AVAILABLE OPTIONS
───────────────────────────────────────────────────────

  --ux              Add UX templates (if not already enabled)
  --deep            Add deep architecture templates
  --search-tool     Override search tool (e.g., pplx)
  --restart         Restart current phase from scratch
  --rollback        Go back to previous checkpoint

How would you like to proceed?
```

### Step 3: Prompt User

Use `AskUserQuestion` with options:
- "Continue generation" (default)
- "Add --ux templates"
- "Add --deep templates"
- "Change search tool"
- "Restart current phase"
- "View generated documents"

If user selects a flag option:
1. Update state.json with new flags
2. Re-display status with updated counts
3. Ask again

Only proceed after user confirms "Continue generation".

### Step 4: Process Selected Option

#### Continue (default)

1. Load current phase state
2. Resume generation from last step
3. If mid-generation:
   - Check if partial output exists
   - Offer to keep or regenerate
4. Hand off to `/northstar:advisor-build --from <phase>`

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

### Step 5: Confirm and Execute

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

Each phase creates its own checkpoint file:

```json
// north-star-advisor/.work-in-progress/checkpoints/checkpoint-phase-{N}.json
{
  "phase": "{N}",
  "template_name": "{TEMPLATE_NAME from index.yml}",
  "output_path": "{template.output from index.yml}",
  "generated_at": "{ISO-8601 timestamp}",
  "verified": true,
  "file_size_bytes": 4521
}
```

**Note:** Phase numbers match `templates/index.yml`. Phase 5a-5c are UX (--ux flag). Phase 7d templates are deep architecture (--deep flag).

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

## Final Cross-Check (REQUIRED)

**After resume completes all remaining phases, run the same cross-check as advisor-build.**

See `advisor-build.md` Step 5 for the full cross-check procedure.

Summary:
1. Read `templates/index.yml`
2. Determine expected templates based on flags
3. Verify each expected file exists and has content
4. Display cross-check results
5. If any missing: offer to regenerate
6. Only mark complete if ALL expected files exist

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► FINAL CROSS-CHECK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Validating outputs against templates/index.yml...

Expected: [X] documents
Found:    [Y] documents

[✓ or ✗] [template.name] → [size or MISSING]
...

RESULT: [PASS ✓ | FAIL ✗ - N missing]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Update `state.json` with cross-check results:
```json
{
  "final_crosscheck": {
    "passed": true|false,
    "checked_at": "{timestamp}",
    "expected_count": 12,
    "found_count": 12,
    "missing": []
  }
}
```

---

## Error Handling

| Error | Recovery |
|-------|----------|
| No checkpoints | Direct to /northstar:advisor-build |
| Corrupted checkpoint | Try previous checkpoint |
| Missing output files | Offer to regenerate |
| State mismatch | Rebuild state from outputs |
| Cross-check fails | Offer to regenerate missing |

---

## Tools Used

- `Read` - Load state and checkpoints
- `Write` - Update state after resume
- `Glob` - Find available checkpoints
- `AskUserQuestion` - Confirm resume option
