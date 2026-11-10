
---
name: create-update-readme-md
description: Use when the task is to write or update the README.md file based on existing code, docs, release notes, or user-provided changes.
---

If this skill is used, say `SKILL_CR_UPD_README_LOADED` in the agent's response, not in README.md.

## Goal

Create or update the **actual project-root `README.md`**. Enforce the required headings, their exact spelling, and their order. Document only verified project capabilities and instructions.

## Required reference

Read `references/README.example.md` (relative to this `SKILL.md`) before editing. It is the **structural and style reference**, not evidence of the target project's features, prerequisites, or commands. Do not copy project-specific content from the example.

If the example is unavailable, use the mandatory structure below and report that the reference could not be read.

## Mandatory README.md structure

Use exactly this top-level order:

```markdown
# <Project name>

<One-sentence purpose>

## What Is This?

<Short explanation of what the project does and its scope>

## Features

- <Verified user-visible capability>

## Who is it for?

<Primary users and use cases>

## How to install

<Verified prerequisites and installation steps>

## How to use

<Verified minimal usage instructions>
```

### Structural rules

1. Include **all seven elements**: the H1 project title, purpose sentence, and the five required H2 sections.
2. Keep H2 headings **exactly** as shown, including capitalization and punctuation, and in the prescribed order. Do not rename, reorder, merge, or omit them.
3. Do not add other H2 sections unless the user explicitly requests them. Use H3 subheadings only when necessary inside an existing section.
4. Place optional screenshots or images **inside `What Is This?`**, after the description, following the example's style. Include only real, existing image references. Do not add `[screenshot]` placeholders.
5. Keep each section concise. Use parallel bullet points for `Features` and fenced code blocks with appropriate language identifiers for commands.
6. Preserve correct project-specific information and useful links from the existing README, but reorganize them into the mandatory structure. Do not delete important accurate information merely to copy the example's length.

## Sources of truth

Inspect, as applicable:

- Existing project-root `README.md`.
- Package manifests, build scripts, CLI entry points, and configuration files.
- Public documentation, changelog, and release notes.
- Relevant source modules and tests.
- User-provided notes and terminology.

Prefer repository evidence over the example. If sources conflict, use the most direct and current evidence and flag unresolved discrepancies.

## Workflow

1. Locate the project root and read its `README.md`, if present.
2. Read `references/README.example.md` and identify its organization and writing style.
3. Inspect the sources needed to verify the project's purpose, features, audience, installation, and usage.
4. Create `README.md` if absent, otherwise update the existing file **in place**.
5. Rewrite or reorganize the file to match the mandatory structure. Remove stale, duplicate, vague, or unsupported feature statements.
6. Check installation and usage commands against repository scripts and documented prerequisites; do not invent commands or versions.
7. Validate the result against the checklist below and save the `README.md` file. Do not stop at suggesting text or showing a draft.

## Missing information

Never borrow unverified claims or commands from the example. If a mandatory section cannot be supported by available evidence, retain its heading, add a short HTML TODO comment describing what must be verified, and report that gap **outside** the README. Do not present uncertain capabilities as shipped features.

## Validation checklist

- The project-root `README.md` exists and has been saved.
- The file has one H1 title and a purpose sentence.
- All five required H2 headings are present **once**, with exact spelling and in the exact order.
- The content use verified project-specific sources.
- Features describe user-visible behavior, not internal refactors or roadmap items.
- Installation and usage instructions match the project.
- No example-project facts, the features that are not supported by the repo or the provided material, unsupported claims remain.

## Output rules

- Edit the repository's `README.md` directly.
- Write in ASD-STE100. Be so brief every word you write costs you $1,000.

## Do not

- Do not modify application code unless explicitly requested.
- Do not copy the example project's name, features, dependencies, or commands.
- Do not invent features, prerequisites, examples, or screenshots.
- Do not silently omit mandatory sections.
- Do not replace the file with a chat-only draft.
- Do not add roadmap items as shipped features.
- Do not describe internal refactors as features.
