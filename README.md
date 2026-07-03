# Obsidian Brain Setup

Turn an empty folder into an agent-ready Obsidian "second brain" with one pasted line.

If you have (1) an AI agent set up (Claude Code, Codex, Gemini, Cursor, etc.) and
(2) Obsidian installed, that is all the setup you need.

## Step 1: Set Up an Agent

This whole system is driven by an AI agent. If you don't have one yet, set one up
first — you'll paste the one-line vault instructions into its window and it does the
rest. Two good options:

- **Anthropic's Claude Code** — https://claude.com/product/claude-code
- **OpenAI's Codex** — https://chatgpt.com/codex

Either works, and either the **app** or the **command-line (CLI)** version is fine —
use whichever you prefer. Once your agent is installed and you can type prompts into
it, continue on.

## Step 2: Install Obsidian

Obsidian is the free, local-first notes app your second brain lives in. It stores
everything as plain markdown files in a folder on your own machine — nothing locked in
the cloud — and gives you linked notes, backlinks, search, and graph views over them.
This project builds the vault (that folder) for you; Obsidian is how you read, edit,
and navigate it. Download it here:

- **Obsidian** — https://obsidian.md/download

Install it before running the setup. After the agent builds your vault, you'll open
that folder in Obsidian (File -> Open Vault).

**Make a folder for it first.** Create a new, empty folder somewhere on your machine
and name it for whatever this vault will focus on — e.g. `Health`, `AI-Research`,
`My-Novel`, `Competitive-Intel`. This is the folder you'll point Obsidian at when it
asks you to open a vault during first launch, and it's the same location and name you
give the agent in Step 3 (it builds the vault right into that folder). Naming it for
your focus now keeps everything oriented around your goal from the very first step.

## Step 3: Build Your Vault

Open your agent (app or CLI) and paste this one line:

```
Fetch https://raw.githubusercontent.com/wesleytunn/Obsidian-Brain-Setup/main/SETUP.md and follow its instructions exactly to set up my new Obsidian vault.
```

The agent will ask you four short questions (vault name, location, purpose, and what
you'd like to name the agent), then build the whole system for you.

## What Gets Built

- **README.md** — the vault's constitution: confidentiality rules every agent must
  uphold, the ongoing-context protocol, and conventions.
- **AGENT_SIGNUP.md** — a permanent identity register. Every agent that works in the
  vault signs in once, under a name you give it, and signs all future actions with it.
- **LIVE_CONTEXT.md** — the shared ongoing-context file. Every agent reads it before
  acting and logs a signed entry after acting, so context carries across sessions and
  across different agents. Capped at 200,000 characters with agent-managed compaction.
- **AGENTS.md** (+ CLAUDE.md / GEMINI.md / .cursorrules symlinks) — agent config that
  loads automatically in every session, for every major CLI agent.
- **Three skills** installed into the vault (`.claude/skills/`):
  - `second-brain-ingest` — read a source (file, URL, image) and file it into a
    cross-referenced wiki.
  - `second-brain-query` — answer questions from the wiki, with citations, and file
    good answers back.
  - `second-brain-lint` — health-check the wiki: orphans, dead links, contradictions,
    frontmatter gaps.
- **Wiki skeleton** — `raw/` for incoming sources, `wiki/` with index, log, and hot
  cache, ready to be pointed at whatever your goal is.

The vault starts as an operational but empty skeleton. It contains no content from
anyone else's vault — it compounds around whatever you feed it.

## Recommended: Install the Obsidian Web Clipper

The vault runs on a simple loop: sources land in `raw/`, then you ingest them. The
fastest way to feed `raw/` from the web is the **Obsidian Web Clipper** browser
extension. It captures the article you're reading — stripped of ads, nav, and
clutter — and saves it straight into your vault as a clean markdown note, one click,
no copy-paste. Every page you find while browsing becomes ready-to-ingest material:
hit `/second-brain-ingest` and it's filed into the wiki with cross-references.

> **Important — set the clipper's save location to your vault's `raw/` folder.** The
> setup already built that `raw/` folder for you, but the Web Clipper won't know about
> it until you tell it. In the extension's settings, set the note-save location (the
> vault and folder it writes to) to your vault's `raw/` directory. If clips land
> anywhere else, `/second-brain-ingest` won't find them — `raw/` is the one place it
> looks. Do this once and every future clip drops straight into the ingest queue.

Install it here:
https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf

(Works in Chrome and other Chromium browsers. Optional, but it's the single biggest
quality-of-life upgrade for keeping the vault fed.)

## What Is a Skill?

A **skill** is a reusable instruction file (plain markdown) that teaches your agent a
repeatable workflow — think of it as a saved command. You invoke one by name: in
Claude Code, type a slash and the skill name (e.g. `/second-brain-ingest`); with other
agents (Codex, Gemini, Cursor), just ask it to run the skill by name ("run
second-brain-ingest on raw/"). Because they're plain markdown, any agent can follow
them — nothing is locked to one tool.

## The Three Skills That Run the Vault

Day to day, the vault is driven by three core skills. Together they let you operate,
maintain, and learn from your second brain:

- **`second-brain-ingest`** — *operate.* This is how material enters the vault. Point
  it at a source in `raw/` (an article, PDF, note, transcript, image, or URL) and it
  reads the source, extracts the key entities and ideas, and files them into the wiki
  as cross-referenced pages. This is the action you run most. **Tip: feed it in
  batches.** Rather than ingesting one file at a time, let several sources pile up in
  `raw/` (clip or drop a batch), then invoke the skill once on the whole set — the
  agent has far more to cross-reference and connect in a single pass, so the wiki comes
  out richer and more interlinked than drip-feeding one source at a time.
- **`second-brain-query`** — *learn from.* This is how you get value back out. Ask a
  question and it synthesizes an answer from everything filed so far, with citations
  back to the source pages — and files the good answers back into the wiki so the
  next question starts from a smarter base.
- **`second-brain-lint`** — *maintain.* This is how the vault stays healthy. Run it
  periodically (every 10-15 ingests is a good rhythm) to catch orphan pages, dead
  links, contradictions, and missing frontmatter before they compound into rot.

The loop is simple: **ingest** sources, **query** to think with them, **lint** to keep
the whole thing clean. Everything compounds through the wiki.

## Once You're Set Up: Recommended First Moves

The vault ships empty on purpose. Here's a good way to start compounding value:

1. **Pick a goal.** Decide what this vault is *for* — a research topic, a project, a
   domain you're going deep on, your health, a book you're writing. A clear goal is
   what everything else orients around.
2. **Have the agent write the goal into the README.** Tell the agent working in the
   vault to add your goal to `README.md`. Every future agent session reads that file,
   so the goal becomes durable context that steers all later work.
3. **Fill it with secondary sources via the Web Clipper.** As you read around your
   goal, clip articles and pages straight into `raw/`, then ingest them. This is the
   fast way to build broad background knowledge the vault can reason over.
4. **Add your own data as primary sources.** Drop your real project files, notes, and
   personal material into `raw/` and ingest them too. This is what makes the vault
   *yours* — it starts connecting your own work to everything else it has learned.

From there, keep the loop turning: ingest what's new, query when you need to think,
and lint every so often to keep it clean.

## Repository Contents

| File | Purpose |
|---|---|
| `SETUP.md` | The full instruction set the agent fetches and follows |
| `skills/*/SKILL.md` | The three skills the agent installs into the new vault |
