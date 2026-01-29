---
description: Display available commands and usage information.
---

# /northstar:help

Display available North Star Advisor commands and usage information.

---

## Usage

```
/northstar:help
```

## Output

Display the following help information:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR
 Strategic Document Generation for Agentic Applications
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

COMMANDS

  /northstar:advisor [flags]
    Begin strategic discovery through open exploration.
    Acts as your thinking partner to articulate what you're building.
    Flags:
      --ux      Include UX design templates (USER_JOURNEYS, UI_DESIGN_SYSTEM, ACCESSIBILITY)
      --deep    Include deep architecture templates (PIPELINE, RESILIENCE, OBSERVABILITY, etc.)
      --full    Include all 22 templates (equivalent to --ux --deep)

  /northstar:advisor-build [options]
    Research and generate strategic documents.
    Runs domain research, then generates 13 documents.
    Options:
      --from <N>      Start from phase N
      --to <N>        Stop at phase N
      --only <N>      Regenerate only phase N

  /northstar:status
    Show current progress (research, generation phases).

  /northstar:resume [options]
    Resume from the last checkpoint.
    Options:
      --restart       Restart current phase from scratch
      --rollback      Go back to previous phase

  /northstar:export [options]
    Package docs and ai-context.yml into a shareable zip.
    Options:
      --output <path>   Custom output path

  /northstar:help
    Show this help message.

WORKFLOW

  1. /northstar:advisor
     - Open exploration: "What are you building, and why?"
     - Follow-up questions until concrete understanding
     - Understanding checkpoint
     - Saves: discovery.md, inputs.yml, ai-context.yml

  2. /northstar:advisor-build
     - Research phase: 4 parallel agents (tech, features, arch, pitfalls)
     - Research checkpoint
     - Generate 13 strategic documents
     - Research informs phases 6, 7, 8

RESEARCH PHASE

  Before generation, 4 research agents run in parallel:
  - Tech Stack: Frameworks, libraries, best practices
  - Features & UX: Expected features, UX patterns
  - Architecture: Patterns, data flow, scalability
  - Pitfalls: Common mistakes, security concerns

  Research is cached for 24 hours.

GENERATION PHASES

  Phase 1:  BRAND_GUIDELINES         Identity, positioning, voice
  Phase 2:  NORTHSTAR                Metric, personas, scope
  Phase 3:  COMPETITIVE_LANDSCAPE    Market shifts, competitors
  Phase 4:  NORTHSTAR_EXTRACT        Axioms, non-goals (synthesis)
  Phase 5:  [--ux] Design templates
            5a: USER_JOURNEYS        Emotional journey maps
            5b: UI_DESIGN_SYSTEM     Design tokens, components
            5c: ACCESSIBILITY        WCAG 2.1 AA patterns
  Phase 6:  ARCHITECTURE_BLUEPRINT   Agent topology, tech stack
  Phase 7:  AGENT_PROMPTS            Per-agent contracts
  Phase 8:  SECURITY_ARCHITECTURE    Threats, auth, safety
  Phase 9:  ADR                      Architecture decisions
  Phase 10: POST_DEPLOYMENT          Monitoring, maintenance
  Phase 11: STRATEGIC_RECOMMENDATION Paths, trade-offs
  Phase 12: ACTION_ROADMAP           30-90 day actions
  Phase 13: INDEX                    Documentation hub

OUTPUT STRUCTURE

  north-star-advisor/
  ├── ai-context.yml       Strategic context for Claude Code
  ├── .work-in-progress/   Working state (hidden)
  │   ├── state.json       Generation progress
  │   ├── inputs.yml       User inputs
  │   ├── discovery.md     Exploration notes
  │   └── research/        Cached research
  └── docs/                Generated strategic documents

  Use /northstar:resume to continue after interruption.
  Use /northstar:export to create a shareable zip.

EXAMPLES

  # Start strategic discovery
  /northstar:advisor --ux

  # Research and generate all documents
  /northstar:advisor-build

  # Generate only the architecture phase
  /northstar:advisor-build --only 6

  # Check progress
  /northstar:status

  # Export for sharing
  /northstar:export

MORE INFO

  Documentation: https://github.com/AI-Native-Systems/north-star-advisor
  Author: Patrick Pena / AI Native Systems
```
