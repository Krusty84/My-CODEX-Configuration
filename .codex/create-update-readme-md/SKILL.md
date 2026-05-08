---
name: create-update-readme-features
description: Use when the task is to write or update the Features section in a README.md file based on existing code, docs, release notes, or user-provided changes. Do not use for general code changes unrelated to README content.
---

## Goal

Create or update the `Features` section of a README.md so it is accurate, concise, and aligned with the current capabilities of the project.

## Inputs

- Existing `README.md` or new `README.md` if none exists
- Relevant source files, docs, release notes, changelog entries, or user-provided notes
- Any repository-specific terminology already used by the project

## Steps

1. Read the current `README.md` and locate the existing `Features` section if present.
2. Inspect the most relevant sources of truth for current functionality:
   - public docs
   - recent release notes or changelog
   - feature folders / modules
   - user-provided notes
3. Preserve the project’s tone and terminology.
4. Prefer feature statements that describe user-visible capability, not internal implementation details.
5. Remove stale, duplicated, or vague feature bullets.
6. Group related features when that improves clarity.
7. Do not invent features that are not supported by the repo or the provided material.

## Output rules

- Keep the section concise.
- Prefer parallel bullet phrasing.
- Use the repository’s existing markdown style.
- If confidence is low for a feature, omit it or explicitly flag uncertainty outside the edited README.md.

## Do not

- Do not rewrite the whole README.md unless requested.
- Do not add roadmap items as shipped features.
- Do not describe internal refactors as features.
