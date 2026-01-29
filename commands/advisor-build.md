---
description: Research and generate 22 strategic documents. Runs domain research, then generates documents informed by findings.
---

# /northstar:advisor-build

Research and generate strategic documents following the North Star methodology. This command first conducts domain research, then generates 13 strategic documents informed by the research findings.

---

## Arguments

```
/northstar:advisor-build [options]
```

| Option | Description |
|--------|-------------|
| `--from <N>` | Start from phase N (research runs if not cached) |
| `--to <N>` | Stop at phase N |
| `--only <N>` | Regenerate only phase N (research runs if not cached) |

---

## Prerequisites

- Project must be initialized with `/northstar:advisor`
- `north-star-advisor/.work-in-progress/state.json` must exist with `understanding_verified: true`
- `north-star-advisor/.work-in-progress/inputs.yml` must contain required inputs

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

Use `Task` tool with `run_in_background: true` for all 4 agents simultaneously:

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

Use WebSearch to find real-world examples and best practices.
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

Use WebSearch to find real-world examples and patterns.
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

Use WebSearch to find real-world examples and patterns.
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

Use WebSearch to find real-world examples and cautionary tales.
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

| Phase | Template | Output | Dependencies |
|-------|----------|--------|--------------|
| 1 | BRAND_GUIDELINES | north-star-advisor/docs/BRAND_GUIDELINES.md | inputs.yml |
| 2 | NORTHSTAR | north-star-advisor/docs/NORTHSTAR.md | Phase 1 |
| 3 | COMPETITIVE_LANDSCAPE | north-star-advisor/docs/COMPETITIVE_LANDSCAPE.md | Phase 1, 2 |
| 4 | NORTHSTAR_EXTRACT | north-star-advisor/docs/NORTHSTAR_EXTRACT.md | Phase 1, 2, 3 |
| 5a | USER_JOURNEYS | north-star-advisor/docs/design/USER_JOURNEYS.md | Phase 1-4 (--ux only) |
| 5b | UI_DESIGN_SYSTEM | north-star-advisor/docs/design/UI_DESIGN_SYSTEM.md | Phase 5a (--ux only) |
| 5c | ACCESSIBILITY | north-star-advisor/docs/design/ACCESSIBILITY.md | Phase 5b (--ux only) |
| 6 | ARCHITECTURE_BLUEPRINT | north-star-advisor/docs/ARCHITECTURE_BLUEPRINT.md | Phase 1-4 (+ 5 if --ux), Research |
| 7 | AGENT_PROMPTS | north-star-advisor/docs/architecture/AGENT_PROMPTS.md | Phase 6, Research |
| 8 | SECURITY_ARCHITECTURE | north-star-advisor/docs/SECURITY_ARCHITECTURE.md | Phase 6, 7, Research |
| 9 | ADR | north-star-advisor/docs/ADR.md | Phase 6, 7, 8 |
| 10 | POST_DEPLOYMENT | north-star-advisor/docs/POST_DEPLOYMENT.md | Phase 6-9 |
| 11 | STRATEGIC_RECOMMENDATION | north-star-advisor/docs/STRATEGIC_RECOMMENDATION.md | Phase 1-10 |
| 12 | ACTION_ROADMAP | north-star-advisor/docs/ACTION_ROADMAP.md | Phase 11 |
| 13 | INDEX | north-star-advisor/docs/INDEX.md | All phases |

### Deep Templates (--deep flag)

After Phase 7, these additional templates are generated in `north-star-advisor/docs/architecture/`:

| Template | Output Path |
|----------|-------------|
| PIPELINE_ORCHESTRATION | north-star-advisor/docs/architecture/PIPELINE_ORCHESTRATION.md |
| RESILIENCE_PATTERNS | north-star-advisor/docs/architecture/RESILIENCE_PATTERNS.md |
| IMPLEMENTATION_SCAFFOLD | north-star-advisor/docs/architecture/IMPLEMENTATION_SCAFFOLD.md |
| OBSERVABILITY | north-star-advisor/docs/architecture/OBSERVABILITY.md |
| TESTING_STRATEGY | north-star-advisor/docs/architecture/TESTING_STRATEGY.md |
| HANDOFF_PROTOCOL | north-star-advisor/docs/architecture/HANDOFF_PROTOCOL.md |

---

## Step 1: Load State

1. Read `north-star-advisor/.work-in-progress/state.json`
2. Read `north-star-advisor/.work-in-progress/inputs.yml`
3. Read `north-star-advisor/.work-in-progress/research/summary.md`
4. Determine which phases to generate based on:
   - Flags from advisor (--ux, --deep, --full)
   - Command options (--from, --to, --only)
   - Previously completed phases

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
│     output_path MUST be: north-star-advisor/docs/{...}.md   │
│                                                             │
│  5. Validate Output                                         │
│     Spawn northstar-validator agent                         │
│     Check required sections present                         │
│     Verify cross-references valid                           │
│     BLOCKING: failures stop generation                      │
│                                                             │
│  6. Write Output                                            │
│     Write to north-star-advisor/docs/{path}/{TEMPLATE_NAME}.md │
│     Extract key outputs to north-star-advisor/.work-in-progress/outputs/{name}.yml │
│                                                             │
│  7. Update ai-context.yml                                   │
│     Add phase-specific fields (see table below)             │
│     Update _meta.phases_complete array                      │
│     Update _meta.last_updated timestamp                     │
│                                                             │
│  8. Update State & Checkpoint                               │
│     Add phase to completed_phases                           │
│     Update phase_status                                     │
│     Save checkpoint                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
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

---

## Step 4: Progress Display

During generation, display:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► GENERATING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase 3/13: COMPETITIVE_LANDSCAPE
├── Loading template...
├── Researching competitors...
├── Generating content...
├── Validating output...
├── Updating ai-context.yml...
└── ✓ Complete

Progress: ███████░░░░░░░░░░░░░ 3/13 (23%)

Next: Phase 4 - NORTHSTAR_EXTRACT
```

## Step 5: Completion

After all phases complete:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Generated: 13 documents
Location:  north-star-advisor/docs/

Documents:
  ✓ BRAND_GUIDELINES.md
  ✓ NORTHSTAR.md
  ✓ COMPETITIVE_LANDSCAPE.md
  ✓ NORTHSTAR_EXTRACT.md
  ✓ ARCHITECTURE_BLUEPRINT.md
  ✓ SECURITY_ARCHITECTURE.md
  ✓ ADR.md
  ✓ POST_DEPLOYMENT.md
  ✓ STRATEGIC_RECOMMENDATION.md
  ✓ ACTION_ROADMAP.md
  ✓ INDEX.md

Strategic context: north-star-advisor/ai-context.yml (complete)
Research summary:  north-star-advisor/.work-in-progress/research/summary.md

▶ Next: Run /northstar:export to create a shareable zip
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
- `WebSearch` / `mcp__pplx__perplexity-search` - Research queries

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
```
