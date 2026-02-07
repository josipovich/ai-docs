## Context Management Workflow

### Overview

This workflow separates context into two layers:
- **Temporary context**: Per-task/ticket documentation in a `docs/` folder within your workspace
- **Long-term context**: Versioned, portable patterns managed by claude-evolve toolkit

---

### Temporary Task Context (`docs/` folder)

For active work, maintain a `docs/` folder with ticket-based organization:

```
project-workspace/
├── docs/
│   └── issues/
│       ├── PROJ-123/              # Ticket number as folder
│       │   ├── context.md         # Problem statement, constraints
│       │   ├── investigation.md   # Findings, debugging notes
│       │   ├── decisions.md       # Why we chose X over Y
│       │   └── assets/            # Screenshots, diagrams
│       ├── PROJ-456/
│       │   └── ...
│       └── PROJ-789/
│           └── ...
├── repo-a/                        # Actual code repos
├── repo-b/
└── repo-c/
```

**Usage pattern:**
1. Create `docs/issues/PROJ-XXX/` when starting a ticket
2. Dump context, investigation notes, decisions as you work
3. Reference with `@docs/issues/PROJ-123/context.md` in Claude sessions

---

### Long-Term Context (claude-evolve toolkit)

For patterns that persist across projects and machines:

```
~/.claude-evolve/
├── active                         # Points to current toolkit name
├── toolkits/
│   └── {toolkit-name}/            # e.g., "personal" or username
│       ├── plugin.json            # Plugin manifest
│       ├── understanding/         # Learned patterns
│       │   ├── understanding.md   # Universal patterns
│       │   └── projects/          # Project-specific learnings
│       │       ├── retail-media.md
│       │       └── other-project.md
│       ├── agents/                # Custom subagents
│       │   └── my-agent.md
│       ├── skills/                # Reusable workflows
│       │   └── my-skill/
│       │       └── SKILL.md
│       ├── rules/                 # Always-active rules
│       │   └── my-rule.md
│       └── history/
│           └── events.json        # Learning history
└── signals/                       # Pending insights to capture
```

**Key commands:**
| Command | Purpose |
|---------|---------|
| `/evolve init` | Create/activate a toolkit |
| `/learn` | Extract learnings from current session |
| `/reflect` | Synthesize learnings into understanding files |
| `/evolve release` | Version and push to git for sync across machines |
| `/evolve understanding` | View all learned patterns |

**Lifecycle:**
1. **Capture**: `/learn` after debugging sessions or discoveries
2. **Synthesize**: `/reflect` periodically to update understanding files
3. **Release**: `/evolve release` to version and sync to other machines
4. **Apply**: Toolkit automatically loads on session start

---

### When to Use Which

| Scenario | Use Temporary (`docs/`) | Use Long-Term (toolkit) |
|----------|-------------------------|-------------------------|
| Debugging a specific bug | ✓ | |
| Discovered a reusable pattern | | ✓ |
| Ticket-specific constraints | ✓ | |
| Project architecture decisions | ✓ (initially) | ✓ (if universal) |
| One-off investigation notes | ✓ | |
| "Always do X in this codebase" | | ✓ |

---

### Example Flow

```
1. Start ticket PROJ-123
   └── Create docs/issues/PROJ-123/context.md

2. Investigate and document
   └── Add findings to docs/issues/PROJ-123/investigation.md

3. Discover reusable pattern
   └── Run /learn to capture insight
   └── Run /reflect to add to understanding.md

4. Complete ticket
   └── Archive or delete docs/issues/PROJ-123/
   └── Pattern persists in toolkit

5. Sync to laptop
   └── Run /evolve release
   └── On other machine: plugin syncs from git
```

---

This separation keeps temporary work isolated and disposable while building a growing library of versioned, portable knowledge
