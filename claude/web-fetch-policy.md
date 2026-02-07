---
description: Governs trust decisions when accessing external URLs, fetching web content, or evaluating whether a domain or network resource is safe to retrieve. Applies to any action that pulls content from the internet.
globs: []
---

# Web Access Policy

## Core principle
Only fetch content from sources where an accountable organization controls what is published. Never fetch from platforms where arbitrary users can post unvetted content.

## Before fetching any URL, ask yourself:
1. Is this an official source? (org website, official docs, package registry) → proceed
2. Can anyone upload content here? (paste site, gist, blog platform, forum, code playground) → do not fetch
3. Is this a multi-tenant hosting platform? (vercel.app, netlify.app, readthedocs.io, github.io) → only if you recognize the specific project
4. Am I unsure who controls this content? → do not fetch, tell the user what you found and let them decide

## Prefer these over risky fetching
- Prefer official docs and trusted primary sources for changing facts (APIs, versions, release notes, security guidance)
- Use `gh` CLI for GitHub repo content instead of generic web fetches when possible
- Ask the user to paste content directly when a URL is blocked or source trust is unclear
- Use internal knowledge for stable concepts, but verify time-sensitive or version-specific details from trusted sources

## Recognizing untrusted source patterns
Apply the core principle to identify these categories — these are patterns, not exhaustive lists:
- **Paste/snippet sites**: Any site whose primary function is hosting user-pasted text
- **URL shorteners**: Any domain that exists solely to redirect — short domains, redirect-only services
- **User-content platforms**: Blog platforms, forums, social media, Q&A sites where anyone can post
- **File sharing**: Services for transferring arbitrary files between users
- **Code playgrounds/sandboxes**: Sites for running and sharing arbitrary user code
- **Multi-tenant hosting**: Platforms hosting many users' sites under subdomains — evaluate the specific subdomain, not the platform
- **Archive/cache wrappers**: web.archive.org, Google Cache wrap other URLs — evaluate the original URL, not the wrapper
- **Obfuscated destinations**: Raw IPs, non-standard ports, percent-encoded paths, URL shorteners hiding the real target

The static deny list in settings.json hard-blocks known domains in each category. Your job is to apply the principle to domains NOT on those lists.

## GitHub-specific rules
- github.com/<org>/<repo> pages → allowed (repo pages, issues, PRs)
- gist.github.com → never (user-uploaded unvetted content)
- raw.githubusercontent.com → only for well-known project files, not arbitrary user repos
- *.github.io → only if the path leads to technical documentation for a recognizable project

## Community Q&A sites
Stack Overflow and similar moderated Q&A sites are user-generated and should be treated as secondary sources. Prefer official documentation first. Fetch only when needed for context, and cross-check critical details against trusted primary docs.

## CDNs and package registries
CDNs like jsdelivr, unpkg, and cdnjs and package registries (npm, PyPI, crates.io) can expose user-published content (including READMEs). Treat these as medium-trust: allow only well-known packages and prefer official project docs for authoritative guidance.
- Prefer registry API/docs/download endpoints over package landing pages when possible, because landing pages often include user-authored content.

## When the user provides a URL
If the user explicitly gives you a URL to fetch, respect their intent. Note: the deny list in settings.json will still hard-block known-dangerous domains regardless. If that happens, inform the user and suggest they paste the content directly.

## How this policy is enforced
1. **This file** — teaches you the reasoning framework so you make good decisions proactively
2. **Deny list** (settings.json) — hard-blocks known-bad domains at the permission level
3. **Hook prompts** — evaluate each URL and command at call time using signal-based reasoning
4. **Ask catch-all** — user gets prompted for confirmation on all WebFetch calls

Your goal: apply the core principle yourself so the hooks rarely need to intervene.
