---
name: second-brain-query
description: "Answer questions using the Obsidian wiki vault. Reads hot cache first, then index, then relevant pages. Synthesizes answers with citations. Files good answers back as wiki pages. Supports quick, standard, and deep modes. Triggers on: what do you know about, query:, what is, explain, summarize, find in wiki, search the wiki, based on the wiki, wiki query quick, wiki query deep, second-brain-query."
allowed-tools: Read Glob Grep
---

# second-brain-query: Query the Wiki

The wiki has already done the synthesis work. Read strategically, answer precisely, and file good answers back so the knowledge compounds.

---

## Query Modes

Three depths. Choose based on the question complexity.

| Mode | Trigger | Reads | Token cost | Best for |
|------|---------|-------|------------|---------|
| **Quick** | `query quick: ...` or simple factual Q | hot.md + index.md only | ~1,500 | "What is X?", date lookups, quick facts |
| **Standard** | default (no flag) | hot.md + index + 3-5 pages | ~3,000 | Most questions |
| **Deep** | `query deep: ...` or "thorough", "comprehensive" | Full wiki + optional web | ~8,000+ | "Compare A vs B across everything", synthesis, gap analysis |

---

## Quick Mode

Use when the answer is likely in the hot cache or index summary.

1. Read `wiki/hot.md`. If it answers the question, respond immediately.
2. If not, read `wiki/index.md`. Scan descriptions for the answer.
3. If found in index summary, respond and do not open any pages.
4. If not found, say "Not in quick cache. Run as standard query?"

Do not open individual wiki pages in quick mode.

---

## Standard Query Workflow

1. **Read** `wiki/hot.md` first. It may already have the answer or directly relevant context.
2. **Read** `wiki/index.md` to find the most relevant pages (scan for titles and descriptions).
3. **Read** those pages. Follow wikilinks to depth-2 for key entities. No deeper.
4. **Synthesize** the answer in chat. Cite sources with wikilinks: `(Source: [[Page Name]])`.
5. **Offer to file** the answer: "This analysis seems worth keeping. Should I save it as `wiki/questions/answer-name.md`?"
6. If the question reveals a **gap**: say "I don't have enough on X. Want to find a source?"

---

## Deep Mode

Use for synthesis questions, comparisons, or "tell me everything about X."

1. Read `wiki/hot.md` and `wiki/index.md`.
2. Identify all relevant sections (concepts, entities, sources, comparisons).
3. Read every relevant page. No skipping.
4. If wiki coverage is thin, offer to supplement with web search.
5. Synthesize a comprehensive answer with full citations.
6. Always file the result back as a wiki page. Deep answers are too valuable to lose.

---

## Token Discipline

Read the minimum needed:

| Start with | Cost (approx) | When to stop |
|------------|---------------|--------------|
| hot.md | ~500 tokens | If it has the answer |
| index.md | ~1000 tokens | If you can identify 3-5 relevant pages |
| 3-5 wiki pages | ~300 tokens each | Usually sufficient |
| 10+ wiki pages | expensive | Only for synthesis across the entire wiki |

If hot.md has the answer, respond without reading further.

---

## Query-Cost Logging (MANDATORY)

Every query invocation appends ONE line to `wiki/meta/query-cost-log.md` **after** the answer is filed. This is instrumentation — without it, there is no way to catch token-cost regressions before the query loop quietly becomes tens of thousands of tokens per question.

### Where

- File: `wiki/meta/query-cost-log.md`
- If file does not exist, create it with this header:
  ```markdown
  ---
  type: meta
  title: Query Cost Log
  created: <today>
  tags: [meta, instrumentation, cost, query]
  description: "One append-only line per query invocation. Used to detect token-cost regressions and index/hot-cache drift."
  ---

  # Query Cost Log

  | date | mode | question-slug | pages-read | ~tokens | session |
  | :-- | :-- | :-- | --: | --: | :-- |
  ```

### Append format (one row per query)

```markdown
| YYYY-MM-DD | quick\|standard\|deep | <short-question-slug> | <N> | <approx-tokens> | [[session-YYYY-MM-DD-slug]] |
```

Where:
- `date` — today (YYYY-MM-DD)
- `mode` — which query mode ran (quick / standard / deep)
- `question-slug` — 3-6-word kebab-case summary of the question
- `pages-read` — count of wiki pages opened (include hot.md + index + each wiki page; exclude unused skill-scaffolding reads)
- `~tokens` — rough token estimate: (pages-read x ~300) + ~1500 for hot/index + question + answer tokens. Round to nearest 500. Mark with `~` prefix to signal it's an estimate.
- `session` — wikilink to the session page filed under Session Logging below. If no session was filed (quick-mode and no follow-up), leave blank.

### When to act on this data

- If average `~tokens` per standard-mode query trends above 10k, investigate index/hot-cache drift — the index is not narrowing the search well enough.
- Flag any single query above 20k tokens: something was wrong (too many pages read, wrong mode chosen, index miss).
- If the log crosses ~200 entries, archive to `wiki/meta/query-cost-log-archive-<YYYY-MM-DD>.md` and start fresh.

Do **not** treat this as optional or skip it under time pressure. The instrumentation gap is exactly what lets costs creep invisibly.

---

## Index Format Reference

The master index (`wiki/index.md`) looks like:

```markdown
## Domains
- [[Domain Name]]: description (N sources)

## Entities
- [[Entity Name]]: role (first: [[Source]])

## Concepts
- [[Concept Name]]: definition (status: developing)

## Sources
- [[Source Title]]: author, date, type

## Questions
- [[Question Title]]: answer summary
```

Scan the section headers first to determine which sections to read.

---

## Domain Sub-Index Format

Each domain folder has a `_index.md` for focused lookups:

```markdown
---
type: meta
title: "Entities Index"
updated: YYYY-MM-DD
---
# Entities

## People
- [[Person Name]]: role, org

## Organizations
- [[Org Name]]: what they do

## Products
- [[Product Name]]: category
```

Use sub-indexes when the question is scoped to one domain. Avoid reading the full master index for narrow queries.

---

## Filing Answers Back

Good answers compound into the wiki. Don't let insights disappear into chat history.

When filing an answer:

```yaml
---
type: question
title: "Short descriptive title"
question: "The exact query as asked."
answer_quality: solid
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [question, <domain>]
related:
  - "[[Page referenced in answer]]"
sources:
  - "[[wiki/sources/relevant-source.md]]"
status: developing
---
```

Then write the answer as the page body. Include citations. Link every mentioned concept or entity.

After filing, add an entry to `wiki/index.md` under Questions and append to `wiki/log.md`.

---

## Canonical-Question Promotion

When the same question (same slug, or same substantive content asked in different words) recurs **3 or more times** across sessions, the session-level audit trail is no longer the right home for the answer. Promote it to a **canonical question page**.

### When to promote

Promote if any of:
- The same question slug appears in 3+ session files under `wiki/sessions/`
- The user explicitly says "promote this", "canonical answer", "file this permanent", or equivalent
- The question is evergreen reference material the agent will need to answer repeatedly
- The answer has been stable (unchanged across the recurring asks) for 2+ weeks

Do NOT promote if:
- The question is scoped to an active decision still being made (stays as sessions until decided)
- The answer differs materially each time (not canonical; keep as sessions — the divergence *is* the signal)
- The answer is fully contained in an existing canonical page (just point queries there; no new question page needed)

### How to promote

1. Create `wiki/questions/<slug>.md` with this frontmatter:
   ```yaml
   ---
   type: question
   title: "Short descriptive title (in answer-form if possible)"
   question: "The canonical form of the question."
   answer_quality: solid
   status: canonical
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   last-confirmed: YYYY-MM-DD
   tags: [question, canonical, <domain>]
   related:
     - "[[Page(s) the answer draws from]]"
   sources:
     - "[[wiki/sources/relevant-source.md]]"
   promoted_from:
     - "[[session-YYYY-MM-DD-slug-1]]"
     - "[[session-YYYY-MM-DD-slug-2]]"
     - "[[session-YYYY-MM-DD-slug-3]]"
   ---
   ```

2. Body: the current best answer in one-pass-readable form, with wikilink citations.

3. Update the originating session pages: add `promoted_to: [[wiki/questions/<slug>]]` to their frontmatter. Session pages stay as audit trail — how the answer evolved.

4. Add the canonical question to `wiki/index.md` under the Questions section.

5. Append promotion event to `wiki/log.md`:
   ```
   ## [YYYY-MM-DD] question-promoted | <title>
   - Promoted from: <N> sessions over <time window>
   - Canonical answer: [[wiki/questions/<slug>]]
   ```

### After promotion

- Future queries on this question should read the canonical `wiki/questions/<slug>.md` first (it's now in the index, so an index-read finds it early)
- If the answer changes, update the canonical page + bump `last-confirmed:`. Don't let sessions override the canonical silently.

### Why this convention exists

The answer should already exist before anyone asks. Promoting recurring answers to canonical pages is how the vault operationalizes that principle — session pages are the *audit trail*; canonical question pages are the *current answer*. Both exist; neither replaces the other.

---

## Gap Handling

If the question cannot be answered from the wiki:

1. Say clearly: "I don't have enough in the wiki to answer this well."
2. Identify the specific gap: "I have nothing on [subtopic]."
3. Suggest: "Want to find a source on this? I can help you search or process one."
4. Do not fabricate. Do not answer from training data if the question is about the specific domain in this wiki.

---

## Session Logging (MANDATORY)

**Every query invocation MUST be logged as a session page.** This is not optional — sessions compound into a decision audit trail for the vault's domain over time.

### Where sessions live

- Session pages: `wiki/sessions/session-YYYY-MM-DD-<slug>.md`
- Session index: `wiki/sessions/_index.md`

### Output order (IMPORTANT)

**File the session FIRST, then output the answer to the user last.** The user reads the answer in the chat window; putting all the file-writing tool calls before the final answer means the answer is the last thing on screen. Do all session-page writes, index updates, and log appends **before** the final synthesized answer to the user.

The sequence is:
1. Do research (read hot.md, index.md, relevant pages)
2. Create the session page at `wiki/sessions/session-YYYY-MM-DD-<slug>.md`
3. Update `wiki/sessions/_index.md` (thread entry + open action items + supersedes if applicable)
4. Append to `wiki/log.md`
5. **Then** output the synthesized answer to the user as the final message

### After researching, always:

1. **Create a session page** at `wiki/sessions/session-YYYY-MM-DD-<slug>.md` with this frontmatter:

```yaml
---
type: session
title: "Short descriptive title"
question: "The exact query as asked."
decision: "One-sentence verdict or direction. Use 'Deferred — <reason>' if no clear call yet."
thread: <topic-slug>
answer_quality: solid | partial | gap
supersedes: null  # link to earlier session if this one refines/overrides it
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [session, <relevant-domain-tags>]
related:
  - "[[Page referenced in answer]]"
sources:
  - "[[wiki/sources/relevant-source.md]]"
status: open | decided | superseded
---
```

2. Write the session body in this order (tight, scannable — not essay paragraphs):

```markdown
# <Title>

**Question:** <exact query>

## Decision
<1-2 sentence verdict. If deferred, say what's needed to decide.>

## Evidence
- **Finding 1** — one-line summary (Source: [[page]])
- **Finding 2** — one-line summary (Source: [[page]])
- ...add as many as needed, but keep each to 1-2 lines max

## Caveats
- Bullet any risks, conditions, or "yes but" qualifications

## Action Items
- [ ] Concrete next step (from: this session)
- [ ] ...

## Open Questions
- Question that emerged but wasn't answered
- ...
```

3. **Update `wiki/sessions/_index.md`** — four places to update:

   a. Add the session under the correct **Thread** heading (create the heading if new). Format:
   ```
   - [[session-slug]] — `decided` | One-sentence verdict
   ```

   b. Add any new open action items to the **Open Action Items** section with a backlink:
   ```
   - [ ] Action item text (from: [[session-slug]])
   ```

   c. When a session **supersedes** an earlier one, update the old entry's status to `superseded` and add a pointer:
   ```
   - [[old-session]] — `superseded` by [[new-session]] | Original verdict
   ```

   d. When an action item gets completed (in any session or conversation), check it off:
   ```
   - [x] ~~Action item text~~ (from: [[session-slug]]) — done YYYY-MM-DD
   ```

4. **Append to `wiki/log.md`** with format:
   ```
   ## [YYYY-MM-DD] session | <title>
   - Question: "<question>"
   - Decision: "<verdict>"
   - Thread: <topic>
   - Answer filed: [[session-YYYY-MM-DD-slug]]
   ```

### Thread slugs

Threads group related sessions by topic so the record builds by theme, not just by date. Derive thread slugs from the vault's actual domain (for example a startup vault might use `pricing`, `marketing`, `operations`; a research vault might use `methods`, `literature`, `experiments`). Keep the list of active threads at the top of `wiki/sessions/_index.md` and check it before inventing a new slug.

### Why this matters

Session logs compound into a working record of how thinking in this domain evolved. The threads group related decisions so the picture builds by topic; the open action items rollup means you never have to hunt through old sessions to find what's still undone. Over time, the sessions index becomes a decision audit trail — including which earlier decisions got refined or overridden.
