A couple of times I've encountered Claude Code or Codex trying to fetch random stuff from the web. Even if you accept everything manually, there's something called approval fatigue  so you might miss something.

I highly recommend adding some rules, deny settings, and hooks for your favourite agent to prevent this.

Here are examples for Claude Code:
~/.claude/rules/web-fetch-policy.md
~/.claude/settings.json -> permissions.deny

for Claude Code also there's an official plugin:
security-guidance Plugin · claude-plugins-official


=========

I mentioned this on friday, you don't need go through docs.
at start, add official marketplace - add some official plugins, especially plugin-dev (for creating proper agents, skills, rules)
then just ask claude what you want 
mention claude guide ask how things work
@claude-code-guide (agent)
or say something like
@claude-code-guide (agent) how can we improve productivity on this project, ask to plugin dev agentsjust interact with it naturally to set things up for max productivity etc,
[2:55 PM]just e.g. ask
@"claude-code-guide (agent)" configure settings.json to block any dangerous commands
[2:56 PM]so you can ask, it will adjust itself 
[2:57 PM]e.g.
@"claude-code-guide (agent)" disable commit message attributions

========

initial claude code setup 

*1. Add the Official Marketplace*
/plugin marketplace add anthropics/claude-plugins-official

*2. Install a Plugin*
/plugin install plugin-dev@claude-plugins-official

*3. Browse & Enable Plugins*
/plugin
Use the interactive UI to discover and enable plugins.

*My initial recommended official plugins*
• `security-guidance` — Identifies security issues
• `code-review` — Reviews code for bugs and best practices
• `feature-dev` — Guided feature development workflow
• `typescript-lsp` — TypeScript navigation and errors (and any other lsp for language that you use)
• `pr-review-toolkit` — Comprehensive PR reviews
• `plugin-dev` — Create your own plugins (for creating agents/skills/rules etc.)

also:
@"claude-code-guide (agent)" configure settings.json to block any dangerous commands (edited)


========

I'd like to share some of my insights about AI usage in general, Claude Code specifically, and how I've been experimenting with making it learn and adapt over time.

I've been using Claude Code since it launched. Early on I built my own solutions for context management, multi-phase workflows, persistent session context etc. It didn't have patterns for these yet. Over time Claude Code implemented many of these features itself.

Claude Code is taking off because most AI tools give you a chat box and some settings. Claude Code gives you hooks, agents, skills, rules, etc. a whole plugin system. You're not just using AI, you're building on top of it. (Cursor has similar primitives now, so these should be possible there too.)

You can define what happens when a session starts. What gets checked before a tool runs. What Claude does when you stop talking. What sub-agents spin up for specific jobs. It's all markdown and bash. No SDK. Write files, behaviour changes.

The building blocks:

• *Agents* are separate processes you spin up for specific jobs like code review, architecture analysis, or any domain you define. They run isolated with their own context.
• *Skills* are reusable prompts and reference knowledge. They use progressive disclosure: descriptions are always loaded, full content only loads when invoked or when Claude decides it's relevant.
• *Rules* are always on, shaping every interaction. Think coding conventions, preferences, project context etc.

So you stop repeating yourself. You get more consistent results. Claude follows actual processes instead of winging it.

But none of it learns. This is the gap. You correct Claude, that correction disappears tomorrow. Doesn't matter if you wrote the components yourself or grabbed them from somewhere.

For a while I was doing it manually. End of session I'd ask Claude to turn the mistakes I corrected, patterns discovered, or approaches that worked into agents, skills, or rules. It worked but got tedious.

So I automated it 

That became claude-evolve. It's a meta plugin that tries to close the loop. Tech stack and domain agnostic, so it should work for any task, not just code.

What it does:
• Flags insights when I correct something
• Saves them as structured observations
• Synthesizes those into persistent understanding that loads every session
• Turns recurring patterns into actual agents and skills
• For complex work, breaks things into phases automatically with progress tracking
• Triggers an interview if there's not enough context for the task

Still rough and experimental but mostly works fine. Also I wouldn't be surprised if Claude Code builds something like this natively soon. 

*The great MD inflation*  Something I keep seeing in the ecosystem. People shipping hundreds of markdown files. A plugin for every framework. An agent for every task. Thousands of instructions competing for context.

A generic "best practices" agent or rule has no idea you prefer a certain pattern. It can't adapt.

claude-evolve goes the other direction. Instead of shipping hundreds of agents, it ships a few meta-agents that create agents, skills, rules etc. based on how you actually work. Smaller footprint. Components earn their place through usage.

*Why bother with all this*

Context window is finite. Everything you load competes for attention.

Skills only load when needed. Agents run isolated so they don't pollute your main conversation. Rules are cheap, always on. Agents are expensive but sandboxed.

You end up with less stuff that's actually relevant instead of a pile of generic instructions. Claude stops losing track halfway through complex tasks.

Last thing. don't get attached to any specific tool or framework. AI tooling moves fast. Claude Code might not stay the best option. But the approach is what matters. Making AI adapt to you instead of the other way around. Building feedback loops. Keeping things lean.

Those patterns transfer. The tool is just the vehicle. 

https://github.com/hknc/claude-evolve

PS - this was mainly created for myself as an experiment, so please treat it more as inspiration for building your own flows than as a finished product. The more interesting takeaway is the approach itself: making AI adapt to how you work, rather than the other way around. (edited)
