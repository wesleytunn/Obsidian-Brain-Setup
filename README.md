# Obsidian Brain Setup

Turn an empty folder into an agent-ready Obsidian "second brain" with one pasted line.

If you have (1) Obsidian installed and (2) an AI CLI agent set up in your terminal
(Claude Code, Codex, Gemini CLI, Cursor CLI, etc.), that is all the setup you need.

## How to use

Open your CLI agent and paste this one line:

```
Fetch https://raw.githubusercontent.com/wesleytunn/Obsidian-Brain-Setup/main/SETUP.md and follow its instructions exactly to set up my new Obsidian vault.
```

The agent will ask you four short questions (vault name, location, purpose, and what
you'd like to name the agent), then build the whole system for you.

## What gets built

- **README.md** — the vault's constitution: confidentiality rules every agent must
  uphold, the ongoing-context protocol, and conventions.
- **AGENT_SIGNUP.md** — a permanent identity register. Every agent that works in the
  vault signs in once, under a name you give it, and signs all future actions with it.
- **LIVE_CONTEXT.md** — the shared ongoing-context file. Every agent reads it before
  acting and logs a signed entry after acting, so context carries across sessions and
  across different agents. Capped at 200,000 characters with agent-managed compaction.
- **AGENTS.md** (+ CLAUDE.md / GEMINI.md / .cursorrules symlinks) — agent config that
  loads automatically in every session, for every major CLI agent.
- **Five skills** installed into the vault (`.claude/skills/`):
  - `second-brain-ingest` — read a source (file, URL, image) and file it into a
    cross-referenced wiki.
  - `second-brain-query` — answer questions from the wiki, with citations, and file
    good answers back.
  - `second-brain-lint` — health-check the wiki: orphans, dead links, contradictions,
    frontmatter gaps.
  - `save` — file any good conversation or insight as a permanent wiki note.
  - `new-skill` — let the vault grow its own new skills over time.
- **Wiki skeleton** — `raw/` for incoming sources, `wiki/` with index, log, and hot
  cache, ready to be pointed at whatever your goal is.

The vault starts as an operational but empty skeleton. It contains no content from
anyone else's vault — it compounds around whatever you feed it.

## Recommended: install the Obsidian Web Clipper

The vault runs on a simple loop: sources land in `raw/`, then you ingest them. The
fastest way to feed `raw/` from the web is the **Obsidian Web Clipper** browser
extension. It captures the article you're reading — stripped of ads, nav, and
clutter — and saves it straight into your vault as a clean markdown note, one click,
no copy-paste. Configure it to drop clips into your vault's `raw/` folder and every
page you find while browsing becomes ready-to-ingest material: hit `/second-brain-ingest`
and it's filed into the wiki with cross-references.

Install it here:
https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf

(Works in Chrome and other Chromium browsers. Optional, but it's the single biggest
quality-of-life upgrade for keeping the vault fed.)

## Repository contents

| File | Purpose |
|---|---|
| `SETUP.md` | The full instruction set the agent fetches and follows |
| `skills/*/SKILL.md` | The five skills the agent installs into the new vault |
