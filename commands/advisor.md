---
description: Begin strategic discovery through open exploration. Acts as your thinking partner to articulate what you're building.
---

# /northstar:advisor

Begin strategic discovery for a new North Star Advisor project. This command acts as your thinking partner, helping you articulate what you're building through open exploration and targeted questions.

---

## Arguments

```
/northstar:advisor [--ux] [--deep] [--full] [--target <path>]
```

| Flag | Description |
|------|-------------|
| `--ux` | Include UX design templates (USER_JOURNEYS, UI_DESIGN_SYSTEM, ACCESSIBILITY) |
| `--deep` | Include deep architecture templates (PIPELINE, RESILIENCE, OBSERVABILITY, etc.) |
| `--full` | Include all 22 templates (equivalent to --ux --deep) |
| `--target <path>` | Specify output directory (default: current directory) |

---

## Philosophy

You are a **thinking partner**, not an interviewer.

Your goal is not to extract information—it's to help the user discover what they actually want to build. Many users haven't fully thought through their ideas. Your questions help them think.


---

## Workflow

### Step 1: Check for Existing Project

1. Check if `north-star-advisor/.work-in-progress/state.json` exists
2. If exists:
   - Read current state
   - Check if new flags were passed (--ux, --deep, --full)
   - If new flags AND project has completed phases:
     - Ask user: "A North Star project already exists. Would you like to:"
       - Update flags and generate new templates (`/northstar:advisor-build` with merged flags)
       - Start fresh (overwrites existing)
     - If "update flags", merge flags into state.json and run `/northstar:advisor-build`
     - If "start fresh", proceed to Step 2
   - If no new flags OR no completed phases:
     - Ask user: "A North Star project already exists. Would you like to:"
       - Continue existing project (`/northstar:resume`)
       - Start fresh (overwrites existing)
     - If "start fresh", proceed to Step 2
     - If "continue", redirect to `/northstar:resume`
3. If not exists, proceed to Step 2

### Step 2: Parse Flags

1. Parse command arguments for flags: `--ux`, `--deep`, `--full`, `--target`
2. If `--full`, set both `--ux` and `--deep` to true
3. Determine target path (default: current working directory)
4. Store flags for state persistence

---

## Phase A: Open Exploration

### Opening Question

Ask ONE open question to start:

```
What are you building, and why does it need to exist?
```

DO NOT ask multiple questions. Let the user's response guide the conversation.

### Follow the Thread

Based on their response, follow up with probing questions.

| User Response Pattern | Follow-up |
|----------------------|-----------|
| Describes WHAT but not WHY | "What problem does this solve?" |
| Describes WHY but not WHAT | "Walk me through using this. What does someone actually do?" |
| Vague description | "When you say [X], do you mean [A] or [B]?" |
| Mentions users broadly | "Who specifically? What's their day like?" |
| Mentions competitors | "How is this different from [competitor]?" |
| Abstract benefits | "Can you give me a concrete example?" |

### Challenge Vagueness

NEVER accept fuzzy answers. If the user says something vague, probe deeper:

| Vague Answer | Follow-up |
|--------------|-----------|
| "It's an AI tool" | "What kind of AI? What does it actually do for the user?" |
| "For developers" | "Which developers? What stack? What experience level?" |
| "Makes things easier" | "Easier how? Walk me through the before and after." |
| "It's like X but better" | "Better in what specific way? What does X get wrong?" |

### Exploration Goals

Continue asking follow-ups until you understand ALL of these:

- [ ] **WHAT** they're building (concrete enough to explain to someone else)
- [ ] **WHY** it needs to exist (specific problem or desire)
- [ ] **WHO** it's for (not "everyone" - specific personas with context)
- [ ] **DIFFERENTIATOR** (why wouldn't they use existing solutions?)

Do NOT proceed to Phase B until all four are concrete and specific.

---

## Phase B: Structured Questions

After exploration, fill in any gaps not covered. Only ask questions for fields not already discussed.

| Field | Only Ask If | Question |
|-------|-------------|----------|
| `application_name` | Not mentioned | "What should we call this?" |
| `tech_stack` | Not discussed | "Do you have a preferred tech stack? (framework, database, hosting, etc.) Or should I research recommendations?" |
| `constraints` | Not discussed | "What constraints are you working with? (time, budget, team size)" |
| `anti_goals` | Not discussed | "What are you explicitly NOT building? What would be scope creep?" |
| `success_metric` | Not discussed | "How will you know this is working? What does success look like?" |

### Vague Answer Detection

After each answer, evaluate quality:
- Is it specific enough to act on?
- Does it contain concrete examples?
- Are there measurable elements?

### Follow-up on Vague Answers

| Field | Vague Pattern | Follow-up |
|-------|--------------|-----------|
| `application_name` | Generic like "AI Helper" | "What does it actually help with? Can we be more specific?" |
| `tech_stack` | "Modern stack" or "Whatever works" | "Any specific frameworks you prefer? React, Vue, Next.js? Database preference? Or truly open to recommendations?" |
| `constraints` | "None" or empty | "What about timeline? Budget? Team size?" |
| `anti_goals` | "Nothing specific" | "What features might users expect that you're NOT going to build?" |
| `success_metric` | "Users like it" | "How would you measure that? What number would make you happy?" |

Keep asking follow-ups until each answer is concrete. There is no limit—clarity is more important than speed.

---

## Phase C: Understanding Checkpoint

Before saving, display a summary and verify understanding:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 UNDERSTANDING CHECK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let me make sure I understand:

You're building [PRODUCT] for [USERS] who struggle with [PROBLEM].

The key insight is [DIFFERENTIATOR].

Success looks like [METRIC].

You're constrained by [CONSTRAINTS].

You're explicitly NOT building [ANTI-GOALS].

Tech stack: [PREFERRED STACK or "Open to recommendations"]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Does this match what you're thinking?
```

Use `AskUserQuestion` with options:
- "Yes, that's it" → Proceed to Phase D
- "Not quite, let me adjust" → Ask what's different, adjust, verify again
- "I need to think more about this" → Save partial state, user can resume later

**If "Not quite":**
1. Ask: "What's different from what you're thinking?"
2. Adjust understanding based on response
3. Display updated summary
4. Verify again

**If "Yes":**
Proceed to Phase D.

**If "I need to think":**
Save partial state with `understanding_verified: false`, user can resume with `/northstar:resume`.

---

## Phase D: Save State

### Create Directory Structure

```
north-star-advisor/
├── ai-context.yml           # Strategic context for Claude Code
├── .work-in-progress/       # Hidden - active session data
│   ├── state.json           # Generation state
│   ├── inputs.yml           # User inputs
│   ├── discovery.md         # Exploration notes
│   ├── outputs/             # Extracted outputs for cross-refs
│   ├── checkpoints/         # Resume points
│   └── research/            # Cached research
└── docs/
    ├── design/              # If --ux flag
    └── architecture/        # For agent prompts + deep templates
```

### Create discovery.md

Save exploration notes for future reference:

```markdown
# Discovery Session

## Date
[ISO-8601-timestamp]

## Exploration Summary

### What
[Product description in user's words]

### Why
[Problem being solved]

### Who
[Target users with specifics]

### Differentiator
[What makes this unique]

### Key Quotes
> "[Direct quote that captures their vision]"
> "[Another meaningful quote from the conversation]"

## Open Questions
- [Any areas that could use more clarity later]
```

### Create inputs.yml

```yaml
# North Star Advisor - Project Inputs
# Generated: [timestamp]

# Core (from exploration)
application_name: "[name]"
one_line_description: "[description]"
target_users: "[users with specifics]"
core_problem: "[problem]"
differentiator: "[what makes it different]"

# Structured (from targeted questions)
tech_stack: "[preferred tech stack or 'open to recommendations']"
constraints: "[constraints]"
anti_goals: "[anti_goals]"
success_metric: "[metric]"

# Phase decisions (populated during generation)
phase_decisions: {}
```

### Create state.json

```json
{
  "project_id": "<generated-uuid>",
  "project_name": "<application_name>",
  "target_path": "<resolved-path>",
  "flags": ["--ux", "--deep"],
  "understanding_verified": true,
  "current_phase": null,
  "completed_phases": [],
  "phase_status": {},
  "phase_outputs": {},
  "validation_results": {},
  "research_complete": false,
  "created_at": "<ISO-8601-timestamp>",
  "updated_at": "<ISO-8601-timestamp>",
  "last_checkpoint": null
}
```

### Create ai-context.yml (initial)

```yaml
# North Star Advisor - Progressive Strategic Context
# This file is updated after each generation phase

_meta:
  generated_by: "north-star-advisor"
  version: "1.0.0"
  created_at: "[timestamp]"
  last_updated: "[timestamp]"
  phases_complete: []

project:
  name: "[application_name]"
  description: "[one_line_description]"
  version: "0.1.0"
  target_users: "[target_users]"
  core_problem: "[core_problem]"
  differentiator: "[differentiator]"
  success_metric: "[success_metric]"
  tech_stack: "[tech_stack]"

# Sections populated as phases complete:
# - strategy: Phase 1 (beliefs, kill_list), Phase 2 (metric, personas), Phase 4 (axioms)
# - architecture: Phase 6 (pattern, agents, tech_stack)
# - security: Phase 8 (auth, threats)
# - roadmap: Phase 12 (phases, milestones)
# - references: Phase 13 (document index)
```

### Display Next Steps

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► READY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Project: [application_name]
Target:  [target_path]/north-star-advisor/docs/
Flags:   [flags or "core templates only"]

Templates to generate:
  • 12 core templates (always)
  • 3 UX templates (--ux)      [if applicable]
  • 7 deep templates (--deep)  [if applicable]
  Total: [count] documents

Files created:
  • north-star-advisor/ai-context.yml
  • north-star-advisor/.work-in-progress/state.json
  • north-star-advisor/.work-in-progress/inputs.yml
  • north-star-advisor/.work-in-progress/discovery.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▶ Next: Run /northstar:advisor-build to start research and generation
```

---

## Error Handling

| Error | Recovery |
|-------|----------|
| Directory not writable | Ask user to check permissions or specify --target |
| Invalid target path | Prompt for valid path |
| Understanding not verified | Save partial state, prompt to resume later |

---

## Tools Used

- `Read` - Check for existing state
- `Write` - Create state files and directories
- `Bash` - Create directory structure
- `AskUserQuestion` - Gather user inputs and verify understanding
