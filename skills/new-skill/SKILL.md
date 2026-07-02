---
name: new-skill
description: >
  Create and formalize a new reusable slash command or skill from user input, install
  it into the vault's skills directory, and document it in the vault README so all
  agents follow the same protocol. Triggers on: "/new-skill", "create a new skill",
  "make this a slash command", "turn this workflow into a skill".
allowed-tools: Read Write Edit Glob Grep
---

# new-skill

Turn whatever follows `/new-skill` into a formal, reusable skill. This is how the vault grows its own capabilities over time.

## Trigger

- User explicitly says `/new-skill ...`
- User asks to create a new reusable slash command or skill for agents

## Input Pattern

`/new-skill <name and intent>`

Example:
`/new-skill weekly-review summarize the week's ingests and surface open questions`

## Required Workflow

1. Parse the user intent that follows `/new-skill`. If the intent is vague, ask 2-3 targeted questions (trigger, inputs, expected output) before writing anything.
2. Normalize the skill name to a filesystem-safe slug (lowercase, hyphenated).
3. Create the skill file at `.claude/skills/<slug>/SKILL.md` (relative to the vault root) with:
   - YAML frontmatter: `name`, `description` (including trigger phrases), `allowed-tools`
   - Sections: Trigger, Purpose, Required Workflow, Constraints, Output Expectation
4. Update the vault `README.md`: add the new skill to the Skills table with a one-line description.
5. Append a brief signed entry to `LIVE_CONTEXT.md` recording what was created.
6. Report the skill name, slug, file path, and README update. Note any conflicts.

## Constraints

- Never overwrite an existing skill without explicit user direction. If the path exists with different content, report the conflict and pause.
- Keep the skill actionable and specific; avoid vague placeholder instructions.
- New skills inherit the vault rules: README.md §STRICT DATA LOCKDOWN binds every skill. A skill may never instruct an agent to share vault data externally.

## Output Expectation

At completion, report:
- Skill name + slug
- Path created
- README and LIVE_CONTEXT updates completed
- Suggested first invocation so the user can test it immediately
