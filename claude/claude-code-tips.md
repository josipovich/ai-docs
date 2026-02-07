// I asked Claude Code to go through my session histories and come up with some generalised quick tips based on how I use it.

# Claude Code Beginner Tips

## Starting a Session

| Command | Purpose |
|---------|---------|
| `claude` | Start interactive session |
| `claude "task"` | Run a one-time task |
| `claude -c` | Continue recent work |
| `/init` | Create CLAUDE.md for your project |
| `/rename <name>` | Name your session for easy finding |

## During Work

### How to Ask

1. **State the goal, number the checkpoints** — Tell Claude what to achieve, not how to code it
   - Bad: "open auth.ts, find line 42, change X to Y"
   - Good:
     1. API returns 500 when email is empty — fix it
     2. Write tests
     3. Run tests and fix failures
     4. Get a code review
   - Why: Claude figures out HOW, you define WHAT and verification

2. **Reference files, lines, or directories** — Use `@filename`, `@filename:10-20`, or `@src/auth/`
   - Why: Claude sees the exact code instead of searching and possibly missing context

### Plan Mode (for complex tasks)

1. Press `Shift+Tab` or type `/plan` to enter Plan Mode
2. Claude explores the codebase and asks clarifying questions
3. Claude creates a plan without changing anything
4. Review and approve the plan
5. Claude executes the approved plan

Why: Prevents wasted work. Catch wrong approaches before any code is written.

### Keyboard Shortcuts

| Shortcut | Purpose |
|----------|---------|
| `Esc` | Stop Claude mid-action |
| `Esc + Esc` | Rewind to previous checkpoint |
| `Ctrl+R` | Search command history |
| `Ctrl+O` | Show Claude's thinking and reasoning |
| `Shift+Tab` | Cycle: Normal → Auto-accept → Plan Mode |
| `!command` | Run bash directly (e.g., `! npm test`) |

## Verifying Work

Build verification into your numbered steps — Claude handles everything:

1. **Include "run tests" as a step** — Claude executes and shows output
2. **Include "review for issues" as a step** — Claude catches its own mistakes
3. **Include "check edge cases" as a step** — Claude verifies empty inputs, nulls, errors

Example prompt:
1. API returns 500 when email is empty — fix it
2. Write tests including edge cases
3. Run tests and fix failures
4. Review for security issues
5. Show me the final test output

Why: Claude can validate its own work in a single flow

## Managing Context

| Problem | Solution | Why |
|---------|----------|-----|
| Unrelated tasks | `/clear` before new topic | Mixed context confuses Claude |
| Long session | `/compact` to compress | Frees space for new work |
| Check usage | `/cost` to see tokens | Know when context is filling up |
| Same mistake repeating | `/clear` and rewrite prompt | Failed attempts pollute context |
| Code change broke something | `Esc + Esc` to rewind | Every change creates a checkpoint |

## Ending a Session

1. **Verify before finishing** — Make Claude run tests one final time
   - Why: Last-minute changes can break working code

2. **Save learnings** — "Add what we learned to CLAUDE.md"
   - Why: Future sessions start with this knowledge

3. **Name the session** — `/rename feature-auth` for easy finding later
   - Why: "Continue the auth work" is easier than scrolling through dates

## Common Mistakes

1. **Kitchen sink sessions** — Don't mix unrelated tasks
   - Why: Context about bug A confuses Claude when working on feature B

2. **Correcting more than twice** — `/clear` and write a better prompt instead
   - Why: Each failed attempt adds noise that makes the next attempt worse

3. **Assuming it works** — Make Claude run tests and validate its own changes
   - Why: Plausible-looking code often has subtle bugs only caught by running it

4. **Infinite exploration** — Be specific about scope
   - Bad: "investigate the codebase"
   - Good: "how does auth work in src/auth/"
   - Why: Broad requests read hundreds of files and fill context with irrelevant info

## Advanced Patterns

1. **Parallel agents for large tasks** — "Review this codebase for security, performance, and test coverage"
   - Why: Claude spawns separate agents, each with fresh context focused on one aspect
   - Note: Claude decides when to parallelize; you can also request it explicitly

2. **Trace external dependencies** — "Trace the imports to see where this comes from"
   - Why: Wrapper functions can hide the real behavior in third-party code

3. **Capture evidence** — "Show me the actual output, not what you think it should be"
   - Why: Claude's mental model can diverge from reality

4. **Turn patterns into components** — "Create a skill/agent/rule for this workflow"
   - **Skills**: Reusable workflows triggered by `/command`
   - **Agents**: Parallel workers for independent subtasks
   - **Rules**: Always-on context in CLAUDE.md or `.claude/rules/`
   - Why: Repeated tasks become reusable components

## Key Insight

Claude Code is an agent, not a chatbot. Steer with goals and verification. Iterate in conversation.
