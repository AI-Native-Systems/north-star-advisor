---
description: Resume generation from the last checkpoint after interruption.
---

# /northstar:resume

Resume generation from the last checkpoint after interruption.

---

## FIRST: Read State (REQUIRED)

**BEFORE ANY OTHER ACTION:**

```
1. Read: north-star-advisor/.work-in-progress/state.json
2. **Legacy state check:** If state.json contains "completed_phases" (pre-2.0.0 format):
   - Go to Legacy State Detection section (Step L1)
   - Do NOT continue into normal workflow
   - Do NOT write any flag updates to state.json
3. Get: plugin_index_path from state.json
4. Read: templates/index.yml at that exact path
```

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

**Rules:**
- **NEVER** hallucinate document names - only use names from index.yml
- **ALWAYS** use plugin_index_path from state.json
- Filter templates by `state.ux` and `state.deep` booleans

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
| `--restart` | Restart current template from scratch |
| `--rollback` | Go back to previous template |
| `--checkpoint <name>` | Resume from specific checkpoint |

---

## Prerequisites

- Project must be initialized with `/northstar:advisor`
- At least one checkpoint must exist in `north-star-advisor/.work-in-progress/checkpoints/`, OR legacy v1.x state is detected (handled via Legacy State Detection)

---

## Workflow

### Step 1: Load State and Determine Expected Documents

1. Read `templates/index.yml` to get the authoritative template list
2. Read `north-star-advisor/.work-in-progress/state.json`
3. **If legacy state was detected in FIRST section, skip to Legacy State Detection (Step L1).**
4. **Update state.json if command-line flags were passed:**
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
   north-star-advisor/.work-in-progress/checkpoints/checkpoint-*.json

2. For each checkpoint:
   a. Read checkpoint.output_path
   b. Check if file exists
   c. Check if file size > 0
   d. Record: { template_name, exists, size }

3. If any checkpoint files are missing:
   Display warning:

   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    WARNING: Missing Checkpoint Files
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

   The following previously generated files are missing:

   ✗ NORTHSTAR.md
   ✗ COMPETITIVE_LANDSCAPE.md

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
  --restart         Restart current template from scratch
  --rollback        Go back to previous checkpoint

How would you like to proceed?
```

### Step 3: Prompt User

Use `AskUserQuestion` with options:
- "Continue generation" (default)
- "Add --ux templates"
- "Add --deep templates"
- "Change search tool"
- "Restart current template"
- "View generated documents"

If user selects a flag option:
1. Update state.json: set `ux: true` and/or `deep: true` as appropriate
2. Re-display status with updated counts
3. Ask again

Only proceed after user confirms "Continue generation".

### Step 4: Process Selected Option

#### Continue (default)

1. Load current_template from state
2. Resume generation from that template
3. If mid-generation:
   - Check if partial output exists at template.output path
   - Offer to keep or regenerate
4. Hand off to /northstar:advisor-build with remaining templates

#### --restart

1. Clear current template output (the file at template.output)
2. Reset template_status[TEMPLATE_NAME] to "pending"
3. Remove TEMPLATE_NAME from completed_templates
4. Start that template from beginning
5. For deep templates: --restart TEMPLATE_NAME restarts one specific template

#### --rollback

1. Load previous checkpoint (checkpoint-{TEMPLATE_NAME}.json)
2. Reset current template status
3. Delete current template output (optional)
4. Resume from previous template in generation order

#### --checkpoint <name>

1. Load specified checkpoint (checkpoint-{TEMPLATE_NAME}.json)
2. Reset all templates after that point in generation order
3. Offer to delete outputs after checkpoint
4. Resume from checkpoint template

### Step 5: Confirm and Execute

```
Resuming from template: ARCHITECTURE_BLUEPRINT

Previous inputs preserved:
  • Orchestration pattern: Hybrid
  • Agent count: 4

Continue? (Y/n)
```

If confirmed, hand off to `/northstar:advisor-build` with remaining templates

---

## Checkpoint Structure

Each phase creates its own checkpoint file:

```json
// north-star-advisor/.work-in-progress/checkpoints/checkpoint-{TEMPLATE_NAME}.json
{
  "phase": "{N}",
  "template_name": "{TEMPLATE_NAME from index.yml}",
  "output_path": "{template.output from index.yml}",
  "generated_at": "{ISO-8601 timestamp}",
  "verified": true,
  "file_size_bytes": 4521
}
```

**Note:** Phase numbers match `templates/index.yml`. Phase 5a-5d are UX (--ux flag). Phase 7d templates are deep architecture (--deep flag).

---

## Recovery Scenarios

### Scenario 1: Interrupted Mid-Generation

```
Detected: Partial output for ARCHITECTURE_BLUEPRINT

north-star-advisor/docs/ARCHITECTURE_BLUEPRINT.md exists but is incomplete.

Options:
1. Continue generation (append to existing)
2. Regenerate from scratch
3. Keep partial and skip to next phase
```

### Scenario 2: Validation Failed

```
ARCHITECTURE_BLUEPRINT failed validation:
  Error: Agent topology section missing

Options:
1. Regenerate ARCHITECTURE_BLUEPRINT
2. Manually fix and re-validate
3. Skip validation (not recommended)
```

### Scenario 3: State Corruption

```
Warning: State file appears corrupted.

Attempting recovery from last checkpoint...
Found: checkpoint-NORTHSTAR_EXTRACT (last modified)

Restore from this checkpoint? (Y/n)
```

---

## Final Cross-Check (REQUIRED)

**After resume completes all remaining phases, run the same cross-check as advisor-build.**

See `advisor-build.md` Step 5 for the full cross-check procedure.

Summary:
1. Read `templates/index.yml`
2. Determine expected templates based on `state.ux` and `state.deep`
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

## Legacy State Detection

If state.json contains `completed_phases` (pre-2.0.0 format), **stop and prompt the user**. Do NOT auto-migrate.

### Step L1: Display Incompatibility Notice

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 INCOMPATIBLE STATE FORMAT (v1.x detected)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

This project was created with North Star Advisor v1.x.
Version 2.0.0 uses a new state format:

  v1.x: phase-keyed   (completed_phases, phase_status)
  v2.0: template-keyed (completed_templates, template_status)

Your generated documents are still intact in:
  north-star-advisor/docs/

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step L2: Ask User How to Proceed

Use `AskUserQuestion` with options:
- "Migrate existing state to v2.0.0 format"
- "Start fresh (keep existing docs as reference)"
- "Cancel"

### Step L3a: If "Migrate"

**Gather information before migrating.**

1. Read old state.json fields: `completed_phases`, `phase_status`, `phase_outputs`, `ux`, `deep`
2. Read templates/index.yml
3. **Filter templates to the legacy project's enabled set:**
   - Include all templates where `flag: null` (core — always enabled)
   - If old state `ux: true`: include templates where `flag: ux`
   - If old state `deep: true`: include templates where `flag: deep`
   - This is the **enabled template set** — only these are relevant for migration
4. **Verify output files only for enabled templates**
   - For each enabled template, check if `template.output` file exists and size > 0
   - Build a verified list of actually-completed enabled templates
5. **Handle ambiguous "7d" entries:**
   - If old `completed_phases` contains "7d" AND old state `deep: true`:
     - List only the enabled deep templates
     - Check which enabled deep template outputs actually exist
     - Display findings to user:
       ```
       Found "7d" in completed_phases. Checking enabled deep template outputs:

         ✓ PIPELINE_ORCHESTRATION     (45,231 bytes)
         ✓ RESILIENCE_PATTERNS        (38,102 bytes)
         ✓ IMPLEMENTATION_SCAFFOLD    (52,887 bytes)
         ✓ OBSERVABILITY              (41,003 bytes)
         ✓ TESTING_STRATEGY           (36,455 bytes)
         ✓ HANDOFF_PROTOCOL           (29,771 bytes)
         ✗ INTELLIGENCE_LAYER         (not found — new in v2.0.0)

       Mark the 6 found templates as complete?
       ```
     - Use `AskUserQuestion` to confirm
   - If old `completed_phases` contains "7d" but `deep: false`: ignore — deep was not enabled
6. **Display migration summary before writing:**
   ```
   Migration Summary:

   Enabled flags: [ux: true/false, deep: true/false]

   Templates to mark complete: [N] / [total enabled]
     [list each with ✓]

   Templates remaining: [M]
     [list each enabled but incomplete with ○]

   Proceed with migration?
   ```
7. **If confirmed, write new state.json:**
   - Create `completed_templates` from verified artifacts
   - Create `template_status` from verified artifacts
   - Create `template_outputs` from verified paths
   - Set `current_template` to the first enabled template in generation order that is NOT in `completed_templates` (if all complete, set to `null`)
   - Remove old fields (`completed_phases`, `phase_status`, `phase_outputs`, `current_phase`)
   - Write updated state.json
8. **Replace legacy checkpoints with v2 checkpoints:**
   - Delete all `checkpoint-phase-*.json` files from `north-star-advisor/.work-in-progress/checkpoints/`
   - For each template in `completed_templates`, synthesize a v2 checkpoint file:
     ```json
     // checkpoint-{TEMPLATE_NAME}.json
     {
       "phase": "{phase from index.yml}",
       "template_name": "{TEMPLATE_NAME}",
       "output_path": "{template.output from index.yml}",
       "generated_at": "{ISO-8601 timestamp of migration}",
       "source": "migrated_from_v1",
       "verified": true,
       "file_size_bytes": {actual file size from verification step}
     }
     ```
   - This ensures rollback, --checkpoint, and checkpoint verification work correctly for migrated templates
   - Display: "State migrated to v2.0.0 format. Run /northstar:resume to continue."

   This ensures `/northstar:resume` continues from the next pending template, not the last completed template.

### Step L3b: If "Start fresh"

1. Ask: "Delete working state? Your docs in north-star-advisor/docs/ will be preserved."
2. If confirmed:
   - Delete `north-star-advisor/.work-in-progress/` directory
   - Display: "Working state cleared. Run /northstar:advisor to start fresh."
   - Existing docs remain as reference

### Step L3c: If "Cancel"

1. Display: "No changes made. You can continue using the v1.x plugin or run this command again."

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
