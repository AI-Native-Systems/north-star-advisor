---
description: Research and generate strategic documents. Runs domain research, then generates documents informed by findings.
---

# /northstar:advisor-build

Research and generate strategic documents following the North Star methodology. This command first conducts domain research, then generates documents informed by the research findings.

**Template Source of Truth:** `templates/index.yml` defines all templates, phases, output paths, and dependencies.

To find it:
1. Check `state.json` for `plugin_index_path` (cached location)
2. If not found, use Glob: `**/northstar/**/templates/index.yml`
3. Update `state.json` with found path for future use

---

## Arguments

```
/northstar:advisor-build [options]
```

| Option | Description |
|--------|-------------|
| `--ux` | Add UX design templates (3 templates). Updates state.json. |
| `--deep` | Add deep architecture templates (6 templates). Updates state.json. |
| `--full` | Enable all templates (--ux + --deep). Updates state.json. |
| `--search-tool <tool>` | Override search tool. Updates state.json. |
| `--from <N>` | Start from phase N (research runs if not cached) |
| `--to <N>` | Stop at phase N |
| `--only <N>` | Regenerate only phase N (research runs if not cached) |

---

## Prerequisites

- Project must be initialized with `/northstar:advisor`
- `north-star-advisor/.work-in-progress/state.json` must exist with `understanding_verified: true`
- `north-star-advisor/.work-in-progress/inputs.yml` must contain required inputs

---

## Step 0: Initialize and Confirm

**This step is REQUIRED before any generation.**

### 0.1 Load Project State (FIRST)

```
1. Read north-star-advisor/.work-in-progress/state.json
2. Get plugin_index_path from state.json
3. Read north-star-advisor/.work-in-progress/inputs.yml
4. Get current flags: ux, deep, search_tool
5. Get completed_phases array
```

### 0.2 Load Template Manifest

```
1. Read templates/index.yml at plugin_index_path (SINGLE SOURCE OF TRUTH)
2. Parse all templates into memory
3. Note the counts: core=12, ux=3, deep=6
```

If `plugin_index_path` is missing from state.json:
- Glob: `**/northstar/**/templates/index.yml`
- Update state.json with found path

### 0.3 Merge Command Flags

```
1. Parse command-line flags: --ux, --deep, --full, --search-tool, --from, --to, --only
2. Merge with stored state (OR logic - once enabled, stays enabled):
   - If --ux passed OR state.ux is true: set ux = true
   - If --deep passed OR state.deep is true: set deep = true
3. If --full: set both ux = true and deep = true
4. If --search-tool provided: update state.json with new search_tool value
5. Write updated state.json immediately
```

### 0.4 Display Build Plan

**ALWAYS display this before proceeding.**

**IMPORTANT:** Read template names from `templates/index.yml`. The example below is illustrative - use actual names from index.yml.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► BUILD PLAN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project: [application_name]
Search Tool: [search_tool or "WebSearch (default)"]

FLAGS
───────────────────────────────────────────────────────

  --ux:    [enabled/disabled] ([count from index.yml] UX templates)
  --deep:  [enabled/disabled] ([count from index.yml] architecture templates)

TEMPLATES TO GENERATE (from templates/index.yml)
───────────────────────────────────────────────────────

Core ([count]):
  [list template names from index.yml where flag: null]

UX ([count]): [if --ux enabled, else "Not enabled"]
  [list template names from index.yml where flag: ux]

Deep ([count]): [if --deep enabled, else "Not enabled"]
  [list template names from index.yml where flag: deep]

Total: [X] documents + ai-context.yml

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

OPTIONS
───────────────────────────────────────────────────────

Available flags you can add:
  --ux          Add UX design templates
  --deep        Add deep architecture templates
  --full        Add all templates (--ux + --deep)
  --search-tool Use custom search tool (e.g., pplx)

Ready to proceed?
```

### 0.5 Confirm with User

Use `AskUserQuestion` with options:
- "Yes, start generation"
- "Add --ux templates"
- "Add --deep templates"
- "Add --full (all templates)"
- "Change search tool"

If user selects a flag option:
1. Update state.json: set `ux: true` and/or `deep: true` as appropriate
2. Re-display build plan with updated counts
3. Ask again

If user selects "Change search tool":
1. Ask: "Enter search tool name (e.g., pplx, mcp__pplx__perplexity-search):"
2. Update state.json with search_tool value
3. Re-display build plan
4. Ask again

Only proceed to Step 1 (Research) after user confirms "Yes, start generation".

---

## Workflow Overview

```
┌─────────────────────────────────────────────────────────────────┐
│ /northstar:advisor-build                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  STEP 0: RESEARCH PHASE (required)                              │
│  ├── Check for cached research (< 24h)                          │
│  ├── If not cached: Run 4 parallel research agents              │
│  │   ├── Tech Stack Research                                    │
│  │   ├── Features & UX Research                                 │
│  │   ├── Architecture Research                                  │
│  │   └── Pitfalls Research                                      │
│  ├── Synthesize → research/summary.md                           │
│  └── Research Checkpoint ✓                                      │
│                                                                 │
│  STEPS 1-13: GENERATION PHASES                                  │
│  └── Research informs Phases 6, 7, 8                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 0: Research Phase

### Check Research Cache

```
1. Check if north-star-advisor/.work-in-progress/research/summary.md exists
2. If exists, check file age
3. If < 24 hours old: Use cached research
4. If >= 24 hours old OR doesn't exist: Run research agents
```

Display if using cached:
```
Using cached research from [X hours] ago.
To refresh, delete north-star-advisor/.work-in-progress/research/summary.md
```

### Spawn Parallel Research Agents

Use `Task` tool with `run_in_background: true` for all 4 agents simultaneously.

**IMPORTANT:** Pass the `search_tool` value from state.json to each agent prompt. If `search_tool` is null or not set, use "WebSearch" as the default.

```
┌─────────────────────────────────────────────────────────────────┐
│ RESEARCH WAVE - 4 PARALLEL AGENTS                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────────┐  ┌──────────────────┐                    │
│  │ Tech Stack       │  │ Features & UX    │                    │
│  │                  │  │                  │                    │
│  │ • Frameworks     │  │ • Expected       │                    │
│  │ • Libraries      │  │   features       │                    │
│  │ • Best practices │  │ • UX patterns    │                    │
│  └──────────────────┘  └──────────────────┘                    │
│                                                                 │
│  ┌──────────────────┐  ┌──────────────────┐                    │
│  │ Architecture     │  │ Pitfalls         │                    │
│  │                  │  │                  │                    │
│  │ • Patterns       │  │ • Anti-patterns  │                    │
│  │ • Data flow      │  │ • Common mistakes│                    │
│  │ • Scalability    │  │ • Security       │                    │
│  └──────────────────┘  └──────────────────┘                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Agent 1: Tech Stack Research

```
Research the technology stack for building [project_type].

Context:
- Project: [application_name]
- Description: [one_line_description]
- Users: [target_users]
- User's preferred stack: [tech_stack]

IF user has preferred stack:
  - Research best practices for their chosen technologies
  - Find complementary libraries that work well with their stack
  - Identify potential issues or considerations for their choices

IF user is open to recommendations:
  - Research recommended frameworks for this type of application
  - Compare popular options and their trade-offs
  - Provide recommendations with rationale

Search for:
- Best practices for [tech_stack or project_type]
- Popular libraries and their trade-offs
- Version recommendations and compatibility

Search tool: [search_tool OR "WebSearch" if not specified]. Use this tool for all web searches.

IMPORTANT: Cite all sources with URLs for full traceability. Include a "Sources" section at the end listing all URLs consulted.

Return structured markdown with recommendations and rationale.
```

### Agent 2: Features & UX Research

```
Research common features and UX patterns for [project_type].

Context:
- Project: [application_name]
- Users: [target_users]
- Problem: [core_problem]

Search for:
- Standard features users expect in this domain
- UX patterns that work well for [target_users]
- Onboarding flows for similar products
- Accessibility considerations

Search tool: [search_tool OR "WebSearch" if not specified]. Use this tool for all web searches.

IMPORTANT: Cite all sources with URLs for full traceability. Include a "Sources" section at the end listing all URLs consulted.

Return structured markdown with expected features and UX recommendations.
```

### Agent 3: Architecture Research

```
Research architecture patterns for [project_type].

Context:
- Project: [application_name]
- Constraints: [constraints]
- Success metric: [success_metric]

Search for:
- Recommended architecture patterns for this domain
- Data flow and state management approaches
- API design patterns
- Scalability considerations

Search tool: [search_tool OR "WebSearch" if not specified]. Use this tool for all web searches.

IMPORTANT: Cite all sources with URLs for full traceability. Include a "Sources" section at the end listing all URLs consulted.

Return structured markdown with pattern recommendations.
```

### Agent 4: Pitfalls Research

```
Research common pitfalls and mistakes when building [project_type].

Context:
- Project: [application_name]
- Differentiator: [differentiator]
- Anti-goals: [anti_goals]

Search for:
- Common mistakes developers make in this domain
- Anti-patterns to avoid
- Security vulnerabilities specific to this type
- Performance issues and how to prevent them

Search tool: [search_tool OR "WebSearch" if not specified]. Use this tool for all web searches.

IMPORTANT: Cite all sources with URLs for full traceability. Include a "Sources" section at the end listing all URLs consulted.

Return structured markdown with pitfalls and prevention strategies.
```

### Collect and Synthesize

Wait for all agents to complete (timeout: 120 seconds).

Create `north-star-advisor/.work-in-progress/research/summary.md`:

```markdown
# Research Summary

## Generated
[ISO-8601-timestamp]

## Project Context
- **Name:** [application_name]
- **Type:** [inferred project_type]
- **Users:** [target_users]
- **Preferred Stack:** [tech_stack]

---

## Technology Stack

### Recommended / Validated
| Layer | Recommendation | Rationale |
|-------|---------------|-----------|
| Framework | [rec] | [why] |
| Database | [rec] | [why] |
| Auth | [rec] | [why] |

### Key Libraries
- [library]: [purpose]

### Best Practices
- [practice 1]
- [practice 2]

---

## Features & UX

### Expected Features
Users of [project_type] typically expect:
1. [feature]
2. [feature]
3. [feature]

### UX Patterns
- [pattern]: [when to use]

### Accessibility Requirements
- [requirement]

---

## Architecture

### Recommended Pattern
[pattern]: [description and rationale]

### Data Flow
[description of recommended data flow]

### Scalability Considerations
- [consideration 1]
- [consideration 2]

---

## Pitfalls to Avoid

### Common Mistakes
1. **[mistake]** → Prevention: [how]
2. **[mistake]** → Prevention: [how]

### Security Concerns
- [concern]: [mitigation]

### Performance Gotchas
- [gotcha]: [solution]

---

## Generation Guidance

These findings should inform:
- **Phase 6 (ARCHITECTURE_BLUEPRINT):** [specific guidance]
- **Phase 7 (AGENT_PROMPTS):** [specific guidance]
- **Phase 8 (SECURITY_ARCHITECTURE):** [specific guidance]
```

Save individual reports:
- `north-star-advisor/.work-in-progress/research/tech-stack.md`
- `north-star-advisor/.work-in-progress/research/features-ux.md`
- `north-star-advisor/.work-in-progress/research/architecture.md`
- `north-star-advisor/.work-in-progress/research/pitfalls.md`

### Research Checkpoint

Display summary and confirm before proceeding:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 RESEARCH COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Key findings:

Tech Stack:
  • [one-line summary]

Features & UX:
  • [one-line summary]

Architecture:
  • [one-line summary]

Pitfalls:
  • [one-line summary]

Full report: north-star-advisor/.work-in-progress/research/summary.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Ready to generate strategic documents?
```

Use `AskUserQuestion` with options:
- "Yes, proceed with generation"
- "Let me review the research first"

Update state.json: `research_complete: true`

---

## Generation Phases

**CRITICAL:** Read `templates/index.yml` for the authoritative template list, phases, output paths, and dependencies. Do NOT use hardcoded document names.

```
1. Read templates/index.yml
2. Filter by flag: include templates where flag is null (core), ux, or deep based on project flags
3. Sort by phase number
4. Generate in dependency order
```

### Execution Rules

**UX Templates (--ux flag):** MUST run SEQUENTIALLY (5a → 5b → 5c) due to dependencies. Do NOT parallelize.

**Deep Templates (--deep flag):** CAN run in PARALLEL after Phase 7 completes. All have the same dependencies (Phase 6/7).

**Core Templates:** Run in phase order (1 → 2 → 3 → ... → 13).

---

## Step 1: Load State and Merge Flags

1. Read `north-star-advisor/.work-in-progress/state.json`
2. Read `north-star-advisor/.work-in-progress/inputs.yml`
3. Read `north-star-advisor/.work-in-progress/research/summary.md`
4. **Merge command-line flags with stored state:**
   - Parse `--ux`, `--deep`, `--full`, `--search-tool` from command arguments
   - If `--full`: set both `ux = true` and `deep = true`
   - Merge using OR logic (once enabled, stays enabled):
     - `ux = (--ux passed) OR (state.ux == true)`
     - `deep = (--deep passed) OR (state.deep == true)`
   - If `--search-tool` provided, override stored value; otherwise use stored `search_tool`
   - Update state.json with merged values
5. Determine which phases to generate:
   - Build full phase list based on merged flags
   - Subtract `completed_phases` from state.json
   - Apply `--from`, `--to`, `--only` filters if specified
   - Result: phases that are enabled but not yet completed

## Step 2: Check Dependencies

For each phase to generate:

1. Verify all dependency phases are completed
2. If missing dependencies:
   - List missing phases
   - Ask user: "Missing dependencies. Generate them first?"
   - If yes, add to generation queue
   - If no, abort with error

## Step 3: Generate Each Phase

For each phase in order:

```
┌─────────────────────────────────────────────────────────────┐
│ PHASE {N}: {TEMPLATE_NAME}                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. Load Template                                           │
│     Read templates/{category}/{TEMPLATE_NAME}_TEMPLATE.md   │
│                                                             │
│  2. Decision Prompts (if applicable)                        │
│     Ask user for phase-specific decisions                   │
│     Store in inputs.yml under phase_decisions               │
│                                                             │
│  3. Gather Cross-References                                 │
│     Read north-star-advisor/.work-in-progress/outputs/ from completed phases │
│     Build context from dependencies                         │
│     Include research findings (for Phases 6, 7, 8)          │
│                                                             │
│  4. Spawn Generator Agent                                   │
│     Use Task tool with northstar-generator agent            │
│     Provide: template_path, output_path, inputs, cross-refs │
│     output_path MUST use template.output from index.yml     │
│                                                             │
│  5. Validate Output                                         │
│     Spawn northstar-validator agent                         │
│     Check required sections present                         │
│     Verify cross-references valid                           │
│     BLOCKING: failures stop generation                      │
│                                                             │
│  6. Write Output                                            │
│     Write to template.output path from index.yml            │
│     Create parent directories if needed (docs/design/, etc) │
│     Extract key outputs to north-star-advisor/.work-in-progress/outputs/{name}.yml │
│                                                             │
│  7. Update ai-context.yml (REQUIRED)                        │
│     For core phases: use "ai-context.yml Updates by Phase"  │
│     For deep templates: use "Deep Template ai-context.yml   │
│       Updates" table                                        │
│     Update _meta.phases_complete array                      │
│     Update _meta.last_updated timestamp                     │
│                                                             │
│  8. Verify & Checkpoint (REQUIRED)                          │
│     a. Verify output file exists at template.output path    │
│     b. Verify file size > 0 bytes                           │
│     c. If verification fails:                               │
│        - Display error: "Phase {N} verification failed"     │
│        - Ask user: "Retry generation?"                      │
│        - Do NOT proceed until file verified                 │
│     d. Write checkpoint file:                               │
│        north-star-advisor/.work-in-progress/checkpoints/    │
│        checkpoint-phase-{N}.json                            │
│     e. Update state.json:                                   │
│        - Add phase to completed_phases                      │
│        - Set phase_status[N] = "complete"                   │
│        - Set phase_outputs[N] = output_path                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘

### Checkpoint File Format

```json
// north-star-advisor/.work-in-progress/checkpoints/checkpoint-phase-{N}.json
{
  "phase": "{N}",
  "template_name": "{TEMPLATE_NAME}",
  "output_path": "{template.output from index.yml}",
  "generated_at": "{ISO-8601 timestamp}",
  "verified": true,
  "file_size_bytes": {size}
}
```

### Research Integration in Generation

For Phases 6, 7, 8, include research context in the generator prompt:

**Phase 6 (ARCHITECTURE_BLUEPRINT):**
```
Additional context from research:
[Include research.architecture section]
[Include research.tech_stack section]

Consider these recommendations when defining the architecture.
```

**Phase 7 (AGENT_PROMPTS):**
```
Additional context from research:
[Include research.features section]
[Include research.architecture section]

Consider expected features when designing agent capabilities.
```

**Phase 8 (SECURITY_ARCHITECTURE):**
```
Additional context from research:
[Include research.pitfalls section]

Address these security concerns in the threat model.
```

### ai-context.yml Updates by Phase

| Phase | Fields Added to ai-context.yml |
|-------|--------------------------------|
| 1 | `strategy.beliefs`, `strategy.kill_list`, `brand.voice` |
| 2 | `strategy.north_star_metric`, `strategy.measurement`, `strategy.personas` |
| 3 | `market.competitors`, `market.differentiation` |
| 4 | `strategy.axioms`, `strategy.non_goals`, `strategy.constraints` |
| 5a | `design.journeys` (--ux only) |
| 5b | `design.tokens` (--ux only) |
| 5c | `design.accessibility` (--ux only) |
| 6 | `architecture.pattern`, `architecture.agents`, `architecture.tech_stack` |
| 7 | `architecture.agent_prompts` (summaries) |
| 8 | `security.auth_pattern`, `security.threats`, `security.guardrails` |
| 9 | `decisions` (ADR summaries) |
| 10 | `operations.monitoring`, `operations.alerts` |
| 11 | `strategy.recommended_path`, `strategy.trade_offs` |
| 12 | `roadmap.phase_1`, `roadmap.phase_2`, `roadmap.phase_3` |
| 13 | `references.docs` (all document paths) |

### Deep Template ai-context.yml Updates (--deep flag)

| Template | Fields Added to ai-context.yml |
|----------|--------------------------------|
| PIPELINE_ORCHESTRATION | `architecture.pipeline_stages`, `architecture.handoff_triggers` |
| RESILIENCE_PATTERNS | `architecture.circuit_breakers`, `architecture.retry_policies`, `architecture.fallback_strategies` |
| IMPLEMENTATION_SCAFFOLD | `implementation.project_structure`, `implementation.setup_scripts` |
| OBSERVABILITY | `operations.logging_strategy`, `operations.tracing`, `operations.dashboards` |
| TESTING_STRATEGY | `testing.unit_coverage`, `testing.integration_tests`, `testing.golden_datasets` |
| HANDOFF_PROTOCOL | `architecture.delegation_rules`, `architecture.context_passing` |

---

## Step 4: Progress Display

During generation, display progress. **Use template names from `templates/index.yml` only.**

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► GENERATING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase [N]/[total]: [template.name from index.yml]
├── Loading template...
├── Generating content...
├── Validating output...
├── Updating ai-context.yml...
└── ✓ Complete

Progress: ███████░░░░░░░░░░░░░ [N]/[total] ([percent]%)

Next: Phase [N+1] - [next template.name from index.yml]
```

## Step 5: Final Cross-Check (REQUIRED)

**Before marking generation complete, validate ALL outputs against `templates/index.yml`.**

### Cross-Check Procedure

```
1. Read templates/index.yml
2. Determine expected templates based on state.json:
   - Always include: templates where flag: null (core)
   - If state.ux == true: include templates where flag: ux
   - If state.deep == true: include templates where flag: deep
3. For each expected template:
   a. Check if output file exists at template.output path
   b. Check if file has content (size > 0 bytes)
   c. Record: { name, expected_path, exists, size }
4. Calculate: expected_count, found_count, missing_list
5. Display cross-check results (see below)
6. If any missing:
   - Ask user: "Regenerate missing documents?"
   - If yes: regenerate ONLY the missing documents
   - If no: warn and mark generation as incomplete
7. Only proceed to completion if ALL expected files exist
```

### Cross-Check Display

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► FINAL CROSS-CHECK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Validating outputs against templates/index.yml...

Expected: [X] documents (core: 12, ux: [0|3], deep: [0|6])
Found:    [Y] documents

CORE TEMPLATES
───────────────────────────────────────────────────────
✓ BRAND_GUIDELINES.md              (4.2 KB)
✓ NORTHSTAR.md                     (3.1 KB)
✗ COMPETITIVE_LANDSCAPE.md         (MISSING)
...

UX TEMPLATES [if enabled]
───────────────────────────────────────────────────────
✓ USER_JOURNEYS.md                 (2.8 KB)
...

DEEP TEMPLATES [if enabled]
───────────────────────────────────────────────────────
✓ PIPELINE_ORCHESTRATION.md        (5.1 KB)
...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 RESULT: [PASS ✓ | FAIL ✗ - N missing]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Update state.json with Cross-Check Results

```json
{
  "final_crosscheck": {
    "passed": true|false,
    "checked_at": "{ISO-8601 timestamp}",
    "expected_count": 12,
    "found_count": 12,
    "missing": []
  }
}
```

---

## Step 6: Completion

**Only display after cross-check passes.** List only documents from `templates/index.yml`.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Generated: [count] documents
Location:  north-star-advisor/docs/

Documents: (from templates/index.yml)
  [list all generated template.name with ✓ prefix]

Strategic context: north-star-advisor/ai-context.yml (complete)
Research summary:  north-star-advisor/.work-in-progress/research/summary.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NEXT STEP: Add to your project's CLAUDE.md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Add this to your CLAUDE.md so Claude Code uses the strategic context:

┌─────────────────────────────────────────────────────
│ ## Strategic Context
│
│ This project was planned using North Star Advisor.
│ Before implementing features, read:
│
│ - `north-star-advisor/ai-context.yml` - Strategic context (start here)
│ - `north-star-advisor/docs/INDEX.md` - Documentation hub
│
│ The ai-context.yml contains:
│ - North Star metric and success criteria
│ - Architecture patterns and agent topology
│ - Security requirements and guardrails
│ - Roadmap phases and milestones
│
│ Always align implementation decisions with these strategic documents.
└─────────────────────────────────────────────────────

▶ Run /northstar:export to create a shareable zip
```

---

## Decision Prompts by Phase

### Phase 1: BRAND_GUIDELINES
```
What is your product's key belief or philosophy?
(e.g., "AI should augment human decision-making, not replace it")
```

### Phase 6: ARCHITECTURE_BLUEPRINT
```
Based on our research, we recommend [PATTERN] architecture.

What orchestration pattern should we use?
Options:
- Centralized (single orchestrator agent)
- Distributed (peer agents with shared state)
- Hierarchical (manager + worker agents)
- Hybrid (orchestrator + autonomous specialists)
```

### Phase 7: AGENT_PROMPTS
```
How many specialized agents do you need?
(Based on your architecture, we recommend 3-5 agents)
```

---

## Validation Gates

Each phase has validation gates that MUST pass:

| Phase | Validation |
|-------|------------|
| 1 | Product name defined, kill list present |
| 2 | North Star metric measurable, personas defined |
| 3 | At least 3 competitors analyzed |
| 4 | Axioms extracted, non-goals listed |
| 6 | Agent topology defined, tech stack specified |
| 7 | System prompts for all agents |
| 8 | Threat model present, auth pattern defined |
| 13 | All cross-references valid |

If validation fails:
1. Display specific failure reason
2. Ask: "Fix now or skip validation?"
3. If fix, re-run generation for that section
4. If skip, log warning and continue (not recommended)

---

## Error Handling

| Error | Recovery |
|-------|----------|
| Missing dependency | List missing phases, offer to generate |
| Validation failure | Show specific error, offer to fix or skip |
| Template not found | Check plugin installation |
| State corruption | Offer to restore from checkpoint |
| Research timeout | Wait longer or proceed with partial research |

---

## Tools Used

- `Read` - Load templates, state, inputs, and research
- `Write` - Save generated documents and state
- `Task` - Spawn generator, validator, and research agents
- `AskUserQuestion` - Gather decision prompts and confirmations
- `Bash` - Create directory structure
- `WebSearch` or user defined search tool - Research queries

---

## Examples

```bash
# Run research and generate all phases
/northstar:advisor-build

# Generate only phase 6 (research runs if not cached)
/northstar:advisor-build --only 6

# Generate phases 1 through 4
/northstar:advisor-build --from 1 --to 4

# Continue from phase 5
/northstar:advisor-build --from 5

# Add UX and deep templates after initial build completed
/northstar:advisor-build --full

# Add only UX templates to existing project
/northstar:advisor-build --ux

# Add only deep architecture templates to existing project
/northstar:advisor-build --deep
```
