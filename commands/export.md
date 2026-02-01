---
description: Package docs and ai-context.yml into a shareable zip file.
---

# /northstar:export

Package generated strategic documents and AI context into a shareable zip file.

---

## Usage

```
/northstar:export [--output <path>]
```

| Option | Description |
|--------|-------------|
| `--output <path>` | Custom output path (default: `north-star-advisor-export.zip`) |

---

## What Gets Included

**Note:** Document names come from `templates/index.yml`. Glob actual files from `north-star-advisor/docs/`.

```
north-star-advisor-export.zip
├── ai-context.yml                    # Strategic context for Claude Code
└── docs/
    ├── [core templates from index.yml where flag: null]
    ├── design/                       # If --ux was used
    │   └── [templates from index.yml where flag: ux]
    └── architecture/
        └── [templates from index.yml where flag: deep or AGENT_PROMPTS]
```

Structure depends on which flags were enabled. Only include files that actually exist.

## What Gets Excluded

- `.work-in-progress/` - Working state (not needed for sharing)
- Research cache
- Checkpoints
- Internal state files

---

## Workflow

### Step 1: Check Prerequisites

1. Check if `north-star-advisor/ai-context.yml` exists
2. Check if `north-star-advisor/docs/` exists and has content
3. If missing:
   ```
   No generated documents found.
   Run /northstar:advisor-build first to generate documents.
   ```

### Step 2: Gather Files

1. Read `north-star-advisor/ai-context.yml`
2. Glob `north-star-advisor/docs/**/*.md`
3. Build file manifest

### Step 3: Create Zip

```bash
cd north-star-advisor && zip -r ../north-star-advisor-export.zip ai-context.yml docs/
```

### Step 4: Display Summary and CLAUDE.md Instructions

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 NORTH STAR ADVISOR ► EXPORT COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Created: north-star-advisor-export.zip

Contents:
  • ai-context.yml (strategic context)
  • [N] documents in docs/

Size: [X] KB

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

Copy the above into your project's CLAUDE.md file.
```

---

## Use Cases

1. **Share with team** - Send strategic docs to collaborators
2. **Import to other tools** - Use in v0, Lovable, Replit, etc.
3. **Backup** - Archive completed planning session
4. **Version control** - Commit zip as a milestone snapshot

---

## Tools Used

- `Glob` - Find all generated documents
- `Read` - Verify files exist
- `Bash` - Create zip archive

---

## Examples

```bash
# Create zip with default name
/northstar:export

# Create zip with custom name
/northstar:export --output my-project-strategy.zip

# Create zip in specific directory
/northstar:export --output ~/exports/project-v1.zip
```
