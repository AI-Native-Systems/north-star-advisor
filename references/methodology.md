# North Star Advisor Methodology

Core methodology reference for strategic document generation.

---

## Overview

The North Star Advisor methodology is a structured approach to generating strategic design documentation for agentic applications. It produces 22 documents across 13 generation phases, ensuring consistency, completeness, and cross-reference integrity.

---

## Core Principles

### 1. Strategy Before Code

Every architectural decision traces back to strategic intent. The methodology enforces this by requiring foundational documents (BRAND, NORTHSTAR) before technical documents (ARCHITECTURE, AGENT_PROMPTS).

### 2. Documents Are Executable Context

Generated documents serve as active context for AI-assisted development, not passive documentation. The `ai-context.yml` file provides progressive strategic context that grows as generation progresses.

### 3. Validation Prevents Drift

Blocking validation gates ensure documents remain internally consistent and aligned with upstream decisions. A change to the North Star metric cascades through dependent documents.

### 4. Progressive Disclosure

The 13-phase process reveals complexity progressively. Early phases establish constraints that simplify later decisions.

---

## Generation Phases

### Phase 1: BRAND_GUIDELINES
**Purpose:** Establish identity, positioning, and voice

**Key Outputs:**
- Product name
- Core beliefs (what we believe to be true)
- Kill list (what we won't build)
- Voice and tone guidelines

**Validation Gates:**
- Product name must be defined
- At least 3 beliefs required
- Kill list must have concrete items

---

### Phase 2: NORTHSTAR
**Purpose:** Define success metric and user personas

**Key Outputs:**
- North Star metric (measurable, time-bound)
- User personas with goals and pains
- Success phases (how we know we're winning)
- Scope boundaries

**Validation Gates:**
- Metric must be measurable
- Metric must have time component
- At least 2 personas required

**Dependencies:** Phase 1

---

### Phase 3: COMPETITIVE_LANDSCAPE
**Purpose:** Understand market and competition

**Key Outputs:**
- Competitor analysis (3+ competitors)
- Feature comparison matrix
- Market trends
- Differentiation opportunities

**Validation Gates:**
- At least 3 competitors analyzed
- Feature comparison table present
- Differentiation clearly stated

**Dependencies:** Phases 1, 2
**Special:** Uses `northstar-researcher` agent for web research

---

### Phase 4: NORTHSTAR_EXTRACT
**Purpose:** Synthesize strategic constraints

**Key Outputs:**
- Axioms (derived truths)
- Non-goals (explicit exclusions)
- Constraints (technical, ethical, regulatory)
- Cross-reference summary

**Validation Gates:**
- Axioms extracted from prior docs
- Non-goals listed
- Cross-references valid

**Dependencies:** Phases 1, 2, 3

---

### Phase 5: Design Templates (--ux flag)

#### Phase 5a: USER_JOURNEYS
**Purpose:** Map emotional user experience

**Key Outputs:**
- Journey maps by persona
- Friction points
- Success moments
- Error recovery paths

#### Phase 5b: UI_DESIGN_SYSTEM
**Purpose:** Define visual language

**Key Outputs:**
- Design tokens (colors, spacing, typography)
- Component patterns
- Interaction states
- Responsive breakpoints

#### Phase 5c: ACCESSIBILITY
**Purpose:** Ensure inclusive design

**Key Outputs:**
- WCAG 2.1 AA compliance plan
- Screen reader patterns
- Keyboard navigation
- Testing protocols

**Dependencies:** Phases 1-4

---

### Phase 6: ARCHITECTURE_BLUEPRINT
**Purpose:** Design system architecture

**Key Outputs:**
- Agent topology
- Orchestration pattern
- Tech stack decisions
- Data flow diagram
- Integration points

**Validation Gates:**
- Agent topology defined
- Orchestration pattern named
- Each agent has clear responsibility
- Tech stack specified

**Dependencies:** Phases 1-4 (+ Phase 5 if --ux)

---

### Phase 7: AGENT_PROMPTS
**Purpose:** Define agent contracts

**Key Outputs:**
- System prompt per agent
- Tool access per agent
- Example I/O
- Handoff protocols

**Validation Gates:**
- System prompt for each agent
- Tools listed per agent

**Dependencies:** Phase 6

---

### Deep Templates (--deep flag, after Phase 7)

- **PIPELINE_ORCHESTRATION:** Agent coordination patterns
- **RESILIENCE_PATTERNS:** Circuit breakers, retries, fallbacks
- **IMPLEMENTATION_SCAFFOLD:** Project structure, setup scripts
- **OBSERVABILITY:** Logging, tracing, alerting
- **TESTING_STRATEGY:** Test plans, golden datasets
- **HANDOFF_PROTOCOL:** Agent-to-agent delegation

---

### Phase 8: SECURITY_ARCHITECTURE
**Purpose:** Define security posture

**Key Outputs:**
- Threat model
- Authentication pattern
- Data classification
- Safety guardrails
- Incident response

**Validation Gates:**
- Threat model present
- Auth pattern defined
- Data classification complete

**Dependencies:** Phases 6, 7

---

### Phase 9: ADR
**Purpose:** Document architecture decisions

**Key Outputs:**
- Key decisions with rationale
- Alternatives considered
- Trade-offs accepted
- References

**Dependencies:** Phases 6, 7, 8

---

### Phase 10: POST_DEPLOYMENT
**Purpose:** Plan operations

**Key Outputs:**
- Monitoring strategy
- Maintenance procedures
- Scaling considerations
- Runbook

**Dependencies:** Phases 6-9

---

### Phase 11: STRATEGIC_RECOMMENDATION
**Purpose:** Synthesize strategic paths

**Key Outputs:**
- Recommended path forward
- Alternative paths
- Trade-off analysis
- Decision framework

**Dependencies:** Phases 1-10

---

### Phase 12: ACTION_ROADMAP
**Purpose:** Define concrete actions

**Key Outputs:**
- 30-day actions (immediate)
- 60-day actions (near-term)
- 90-day actions (medium-term)
- Dependencies and milestones

**Dependencies:** Phase 11

---

### Phase 13: INDEX
**Purpose:** Create documentation hub

**Key Outputs:**
- Document index with summaries
- Navigation structure
- Cross-reference map
- Quick start guide

**Validation Gates:**
- All documents linked
- All links resolve

**Dependencies:** All phases

---

## Template Categories

### Core Templates (12)
Always generated:
1. BRAND_GUIDELINES
2. NORTHSTAR
3. COMPETITIVE_LANDSCAPE
4. NORTHSTAR_EXTRACT
5. ARCHITECTURE_BLUEPRINT
6. SECURITY_ARCHITECTURE
7. ADR
8. POST_DEPLOYMENT
9. STRATEGIC_RECOMMENDATION
10. ACTION_ROADMAP
11. INDEX
12. OPERATIONS_RUNBOOK (optional)

### Design Templates (3, --ux flag)
5a. USER_JOURNEYS
5b. UI_DESIGN_SYSTEM
5c. ACCESSIBILITY

### Architecture Templates (7, --deep flag)
- AGENT_PROMPTS (always with --deep)
- PIPELINE_ORCHESTRATION
- RESILIENCE_PATTERNS
- IMPLEMENTATION_SCAFFOLD
- OBSERVABILITY
- TESTING_STRATEGY
- HANDOFF_PROTOCOL

---

## Generation Flags

| Flag | Templates Added | Total |
|------|-----------------|-------|
| (none) | 12 core | 12 |
| --ux | +3 design | 15 |
| --deep | +7 architecture | 19 |
| --full | All | 22 |

---

## Quality Framework

### Validation Levels

- **BLOCKING:** Must pass to continue
- **WARNING:** Logged but generation continues

### Content Standards

- Specific, not generic
- Actionable, not descriptive
- Consistent terminology
- Resolved cross-references

### Cross-Reference Integrity

Every document references upstream sources. Validation ensures:
- All references resolve
- Terminology is consistent
- Data is current
