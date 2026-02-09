# Claude Code — Features & Commands Reference

Complete reference for Claude Code CLI. See also `claude-code-tips.md` for workflow patterns.

---

## Starting & Resuming Sessions

| Command | Action |
|---------|--------|
| `claude` | Start interactive session |
| `claude "task"` | Start with initial prompt |
| `claude -c` | Continue most recent conversation |
| `claude -r "name"` | Resume specific session by name or ID |
| `claude -p "query"` | Print mode — respond and exit (headless) |
| `cat file \| claude -p "query"` | Pipe input for processing |
| `claude update` | Update to latest version |

---

## Keyboard Shortcuts

### Core Controls

| Shortcut | Action |
|----------|--------|
| `Esc` | Stop Claude mid-action |
| `Esc` + `Esc` | Rewind to previous checkpoint |
| `Ctrl+C` | Cancel current input or generation |
| `Ctrl+D` | Exit session |
| `Ctrl+O` | Toggle verbose output (see Claude's thinking) |
| `Ctrl+R` | Search command history |
| `Ctrl+G` | Open input in external text editor |
| `Cmd+V` | Paste image from clipboard |
| `Ctrl+B` | Background running task |
| `Ctrl+T` | Toggle task list |
| `Shift+Tab` | Cycle permission modes: Normal → Auto-accept → Plan |
| `Option+P` | Switch model |
| `Option+T` | Toggle extended thinking |

### Multiline Input

| Method | Works in |
|--------|----------|
| `\` + `Enter` | All terminals |
| `Option+Enter` | macOS default |
| `Shift+Enter` | iTerm2, WezTerm, Ghostty, Kitty |
| `Ctrl+J` | All terminals |

### Quick Prefixes

| Prefix | Action |
|--------|--------|
| `/` | Invoke slash command or skill |
| `!` | Run bash command directly |
| `@` | Mention file/directory (autocomplete) |

---

## Slash Commands

| Command | Action |
|---------|--------|
| `/help` | Usage help |
| `/clear` | Clear conversation history |
| `/compact [focus]` | Compress conversation (optional focus topic) |
| `/cost` | Show token usage |
| `/context` | Visualize context usage as colored grid |
| `/init` | Create CLAUDE.md for project |
| `/memory` | Edit CLAUDE.md memory files |
| `/model` | Change AI model (with effort adjustment for Opus) |
| `/plan` | Enter plan mode |
| `/rename <name>` | Name current session |
| `/resume` | Open session picker |
| `/rewind` | Rewind conversation/code or summarize |
| `/copy` | Copy last response to clipboard |
| `/export [file]` | Export conversation to file or clipboard |
| `/config` | Open settings interface |
| `/permissions` | View/update permissions |
| `/agents` | View/create/manage subagents |
| `/hooks` | Interactive hook management |
| `/plugin` | Manage plugins |
| `/mcp` | Manage MCP servers and OAuth |
| `/tasks` | List background tasks |
| `/todos` | List current TODO items |
| `/stats` | Daily usage, streaks, model preferences |
| `/status` | Version, model, account, connectivity |
| `/statusline` | Configure status line UI |
| `/theme` | Change color theme |
| `/vim` | Enable vim-style editing |
| `/debug` | Troubleshoot current session |
| `/doctor` | Check installation health |

---

## Permission Modes

Toggle with `Shift+Tab` during a session, or set in settings.

| Mode | Behavior |
|------|----------|
| `default` | Prompts for each new tool use |
| `acceptEdits` | Auto-accept file edits, still asks for commands |
| `plan` | Read-only — no edits or commands |
| `delegate` | Coordination-only for agent team leads |
| `dontAsk` | Auto-deny unless pre-approved |
| `bypassPermissions` | Skip all checks (dangerous) |

---

## Models

| Alias | Best for |
|-------|----------|
| `sonnet` | Daily coding, fast and capable |
| `opus` | Complex reasoning, architecture |
| `haiku` | Quick/simple tasks, cheap |
| `opusplan` | Opus for planning, Sonnet for execution |
| `sonnet[1m]` / `opus[1m]` | Extended 1M context window |

Switch with `/model`, `Option+P`, `--model <alias>`, or `ANTHROPIC_MODEL=opus`.

Opus supports effort levels (`low`, `medium`, `high`) — adjust in `/model` with arrow keys.

---

## CLAUDE.md — Project Memory

CLAUDE.md files are loaded at startup into Claude's context. They tell Claude about your project's conventions, architecture, and preferences.

### File Hierarchy (highest to lowest priority)

| File | Scope | Shared via git? |
|------|-------|-----------------|
| `./CLAUDE.md` or `.claude/CLAUDE.md` | Project | Yes |
| `.claude/rules/*.md` | Modular project rules | Yes |
| `~/.claude/CLAUDE.md` | User (all projects) | No |
| `./CLAUDE.local.md` | Local project overrides | No (gitignored) |
| `~/.claude/projects/<project>/memory/` | Auto memory (Claude's notes) | No |

### Rules Directory

Break rules into files for modularity. Supports conditional loading with glob frontmatter:

```
.claude/rules/
├── code-style.md
├── testing.md
└── frontend/
    └── react.md        # Can have: paths: ["src/**/*.tsx"]
```

### Auto Memory

Claude automatically writes learnings to `~/.claude/projects/<project>/memory/MEMORY.md`. This persists across sessions — Claude remembers patterns, debugging insights, and project-specific knowledge.

### Imports

Reference other files from any CLAUDE.md:
```
@path/to/file.md
@~/.claude/my-preferences.md
```

---

## Settings & Permissions

### Settings Files

| Location | Scope | Shared? |
|----------|-------|---------|
| `~/.claude/settings.json` | User (all projects) | No |
| `.claude/settings.json` | Project | Yes |
| `.claude/settings.local.json` | Local project | No (gitignored) |

### Permission Rules

Define in settings under `permissions.allow`, `permissions.ask`, `permissions.deny`:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Read",
      "Edit(/docs/**)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Read(.env)",
      "WebFetch(domain:pastebin.com)"
    ]
  }
}
```

**Pattern syntax:**
- `Bash(prefix*)` — match command prefix
- `Read(.env)` / `Edit(/docs/**)` — glob file patterns
- `WebFetch(domain:example.com)` — domain restrictions
- `//path` — absolute from filesystem root
- `~/path` — from home directory

---

## Hooks

Hooks run shell commands or prompts in response to events. Configure in settings.json.

### Hook Events

| Event | Fires when | Can block? |
|-------|-----------|------------|
| `SessionStart` | Session begins/resumes | No |
| `UserPromptSubmit` | Before Claude processes your input | Yes |
| `PreToolUse` | Before tool execution | Yes (exit 2) |
| `PostToolUse` | After tool succeeds | No |
| `PostToolUseFailure` | After tool fails | No |
| `PermissionRequest` | Permission dialog appears | Yes |
| `SubagentStart` / `SubagentStop` | Subagent lifecycle | No |
| `PreCompact` | Before compaction | No |
| `Stop` | Claude finishes responding | No |
| `Notification` | Notification sent | No |

### Example: Auto-format after edits

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "prettier --write $FILE"
      }]
    }]
  }
}
```

### Example: Security gate with prompt hook

```json
{
  "PreToolUse": [{
    "matcher": "Bash",
    "hooks": [{
      "type": "prompt",
      "model": "claude-haiku-4-5-20251001",
      "prompt": "Evaluate if this command is safe... $ARGUMENTS",
      "timeout": 15
    }]
  }]
}
```

### Hook Types

| Type | What it does |
|------|-------------|
| `command` | Runs a shell command |
| `prompt` | Sends to a Claude model for evaluation |
| `agent` | Delegates to a subagent for verification |

**Exit codes for blocking hooks:** `0` = allow, `2` = block (stderr sent as feedback).

---

## Subagents

Subagents are child AI agents that handle tasks in isolated context. They work autonomously and return summaries.

### Built-in Agent Types

| Type | Model | Tools | Use for |
|------|-------|-------|---------|
| `Explore` | Haiku | Read-only | Fast codebase search/analysis |
| `Plan` | Inherits | Read-only | Research for implementation planning |
| `general-purpose` | Inherits | All | Complex multi-step tasks |
| `Bash` | Inherits | Bash only | Terminal commands |

### Creating Custom Subagents

Place in `.claude/agents/` (project) or `~/.claude/agents/` (user), or use `/agents`.

```markdown
---
name: code-reviewer
description: Reviews code for quality. Use proactively after changes.
tools: Read, Grep, Glob, Bash
model: sonnet
maxTurns: 10
---

You are a senior code reviewer. Focus on security, readability, error handling.
```

### Key Frontmatter Fields

| Field | Purpose | Example |
|-------|---------|---------|
| `name` | Unique identifier | `code-reviewer` |
| `description` | When Claude should use it | "Reviews code quality" |
| `tools` | Allowed tools (allowlist) | `Read, Grep, Glob` |
| `disallowedTools` | Denied tools (blocklist) | `Write, Edit` |
| `model` | Which model | `sonnet`, `haiku`, `opus`, `inherit` |
| `permissionMode` | Permission handling | `default`, `acceptEdits`, `dontAsk` |
| `maxTurns` | Max iterations | `10` |
| `skills` | Preload skill content | `[api-conventions]` |
| `memory` | Persistent memory scope | `user`, `project`, `local` |
| `hooks` | Lifecycle hooks | See hooks section |

### When Subagents Are Used

- **Automatically**: Claude delegates when it recognizes a matching task
- **Explicitly**: Ask Claude "use the code-reviewer to check this"
- **Parallel**: "Research auth and payments in parallel" spawns multiple agents

---

## Skills (Custom Slash Commands)

Skills are reusable instruction files that become `/commands`. They load on-demand to save context.

### Creating a Skill

Create `~/.claude/skills/<name>/SKILL.md` (user) or `.claude/skills/<name>/SKILL.md` (project):

```yaml
---
name: deploy
description: Deploy to production
disable-model-invocation: true
---

Deploy workflow:
1. Run test suite
2. Build application
3. Push to deployment target
4. Verify success
```

Invoke with `/deploy`.

### Key Frontmatter Fields

| Field | Purpose |
|-------|---------|
| `name` | Slash command name |
| `description` | When/why to use it |
| `disable-model-invocation` | Only user can invoke (not Claude) |
| `user-invocable` | Set `false` to hide from `/` menu |
| `allowed-tools` | Restrict available tools |
| `model` | Model to use |
| `context` | `fork` to run in isolated subagent |
| `agent` | Which agent type for fork |

### Skill Arguments

```yaml
---
name: fix-issue
---
Fix GitHub issue $ARGUMENTS following our standards.
```

Invoke: `/fix-issue 123`

Positional: `$0`, `$1`, `$2` etc.

### Dynamic Context (Shell Commands)

Run commands before the skill prompt reaches Claude:

```yaml
---
name: pr-summary
context: fork
---

PR diff: !`gh pr diff`
Changed files: !`gh pr diff --name-only`

Summarize this PR.
```

### Skills vs Subagents

| | Skills | Subagents |
|---|--------|-----------|
| **What** | Reusable instructions | Autonomous child agents |
| **Context** | Inline (unless `context: fork`) | Always isolated |
| **Invoked by** | `/name` or Claude auto-loads | Claude delegates or you ask |
| **Best for** | Quick workflows, conventions | Long/complex/parallel tasks |

---

## MCP Servers

MCP (Model Context Protocol) servers extend Claude with external tools and data sources.

### Adding Servers

```bash
claude mcp add --transport http <name> <url>         # HTTP (recommended)
claude mcp add --transport stdio <name> -- <cmd>      # Local process
claude mcp add --transport sse <name> <url>            # SSE (deprecated)
```

### Management

```bash
claude mcp list              # List all servers
claude mcp get <name>        # Server details
claude mcp remove <name>     # Remove
/mcp                         # In-session: manage and authenticate
```

### Configuration Scopes

| Scope | File | Shared? |
|-------|------|---------|
| `local` | `~/.claude.json` | No |
| `project` | `.mcp.json` | Yes |
| `user` | `~/.claude.json` | No |

---

## Plugins

Plugins bundle agents, skills, rules, hooks, and MCP servers into installable packages.

### Plugin Management

```bash
/plugin marketplace add anthropics/claude-plugins-official   # Add marketplace
/plugin install plugin-dev@claude-plugins-official            # Install plugin
/plugin                                                       # Browse/manage
```

### Recommended Official Plugins

| Plugin | Purpose |
|--------|---------|
| `security-guidance` | Identifies security issues |
| `code-review` | Reviews code for bugs and best practices |
| `feature-dev` | Guided feature development workflow |
| `pr-review-toolkit` | Comprehensive PR reviews |
| `plugin-dev` | Create your own plugins |
| Language-specific LSPs | Navigation and error checking |

---

## Headless / SDK Mode

Run Claude non-interactively for scripts and pipelines:

```bash
# Basic
claude -p "summarize this project"

# With piped input
cat logs.txt | claude -p "find errors"
gh pr diff | claude -p "review for security"

# Structured output
claude -p "list functions" --output-format json
claude -p "list functions" --output-format stream-json
claude -p "extract data" --json-schema '{"type":"object",...}'

# Constrained
claude -p "task" --max-turns 5 --max-budget-usd 1.00
claude -p "task" --allowedTools "Bash(npm test)" "Read"
```

---

## Useful CLI Flags

### Session Management

| Flag | Action |
|------|--------|
| `--continue` / `-c` | Continue most recent session |
| `--resume` / `-r` | Resume by name or open picker |
| `--fork-session` | Branch off a new session variant |
| `--from-pr <number>` | Resume sessions linked to a GitHub PR |
| `--no-session-persistence` | Don't save session |

### Model & Behavior

| Flag | Action |
|------|--------|
| `--model <alias>` | Set model (sonnet, opus, haiku) |
| `--permission-mode <mode>` | Set permission mode |
| `--agent <name>` | Run as specific custom agent |
| `--add-dir <path>` | Add extra working directories |
| `--verbose` | Show detailed logging |
| `--debug [category]` | Debug mode (e.g., "api,hooks") |

### System Prompt

| Flag | Action |
|------|--------|
| `--append-system-prompt "text"` | Add to default system prompt |
| `--system-prompt "text"` | Replace entire system prompt |
| `--append-system-prompt-file <path>` | Load additions from file |

### Permissions

| Flag | Action |
|------|--------|
| `--allowedTools "Tool(pattern)"` | Pre-approve specific tools |
| `--disallowedTools "Tool(pattern)"` | Block specific tools |

---

## Context Management

| Problem | Solution |
|---------|----------|
| Unrelated tasks polluting context | `/clear` before new topic |
| Long session filling up | `/compact` to compress |
| Check how much context used | `/cost` or `/context` |
| Want to undo Claude's changes | `Esc + Esc` to rewind |
| Need to keep working but context full | `/compact [focus topic]` |
| Same mistake repeating | `/clear` and rewrite prompt |

### Checkpointing

Every tool use creates a checkpoint. `Esc + Esc` or `/rewind` opens options:
- Restore code and conversation
- Restore conversation only
- Restore code only
- Summarize from here (compress context)

---

## Environment Variables

### Common

| Variable | Purpose |
|----------|---------|
| `ANTHROPIC_API_KEY` | API key for Console auth |
| `ANTHROPIC_MODEL` | Default model |
| `CLAUDE_CODE_EFFORT_LEVEL` | Thinking depth: `low`, `medium`, `high` |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | Disable auto memory (`1`) |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | Auto-compact threshold (e.g., `50`) |

---

## Quick Reference: What Goes Where

| I want to... | Put it in... |
|--------------|-------------|
| Set project conventions | `CLAUDE.md` or `.claude/rules/*.md` |
| Set personal preferences | `~/.claude/CLAUDE.md` |
| Local overrides (gitignored) | `CLAUDE.local.md` |
| Block dangerous commands | `~/.claude/settings.json` → `permissions.deny` |
| Auto-format on save | Settings → `hooks.PostToolUse` |
| Reusable workflow (/command) | `.claude/skills/<name>/SKILL.md` |
| Autonomous specialist agent | `.claude/agents/<name>.md` |
| External tool integration | `.mcp.json` or `claude mcp add` |
| Installable package of the above | Plugin (`.claude-plugin/plugin.json`) |
