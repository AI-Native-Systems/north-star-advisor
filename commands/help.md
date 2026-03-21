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
      --ux      Include UX design templates (USER_JOURNEYS, UI_DESIGN_SYSTEM, ACCESSIBILITY, etc.)
      --deep    Include deep architecture templates (PIPELINE, RESILIENCE, OBSERVABILITY, etc.)
      --full    Include all 23 templates (equivalent to --ux --deep)

  /northstar:advisor-build [options]
    Research and generate strategic documents.
    Runs domain research, then generates documents.
    Options:
      --from <N>      Start from phase N
      --to <N>        Stop at phase N
      --only <N>      Regenerate only phase N
      Note: phase numbers come from templates/index.yml
      --ux            Add UX templates (can extend existing project)
      --deep          Add deep architecture templates
      --full          Add all templates (equivalent to --ux --deep)

  /northstar:status
    Show current progress (research, generation phases).

  /northstar:resume [options]
    Resume from the last checkpoint.
    Options:
      --restart       Restart current template from scratch
      --rollback      Go back to previous template

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
     - Research phase: 5 parallel agents (tech, features, arch, pitfalls, intelligence layer)
     - Research checkpoint
     - Generate strategic documents
     - Research informs phases 6, 7, 7d, 8

RESEARCH PHASE

  Before generation, 5 research agents run in parallel:
  - Tech Stack: Frameworks, libraries, best practices
  - Features & UX: Expected features, UX patterns
  - Architecture: Patterns, data flow, scalability
  - Pitfalls: Common mistakes, security concerns
  - Intelligence Layer: Embedding models, RAG patterns, model routing, evaluation

  Research is cached for 24 hours.

GENERATION PHASES (see templates/index.yml for authoritative list)

  Core templates (always generated):
    Phases 1-4:   Strategy & positioning
    Phases 6-13:  Architecture & implementation

  UX templates (--ux flag):
    Phase 5a-5d:  Design system, accessibility & wireframes

  Deep templates (--deep flag):
    Phase 7d:     Advanced architecture patterns

  Counts: 12 core + 4 ux + 7 deep = 23 total (+ ai-context.yml)

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

  # Add UX and deep templates after initial build
  /northstar:advisor-build --full

  # Check progress
  /northstar:status

  # Export for sharing
  /northstar:export

MORE INFO

  Documentation: https://github.com/AI-Native-Systems/north-star-advisor
  Author: Patrick Pena / AI Native Systems™
```
