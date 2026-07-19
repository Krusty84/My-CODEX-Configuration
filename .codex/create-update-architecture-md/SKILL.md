---
name: create-update-architecture-md
description: Use when the task is to create or update an ARCHITECTURE.md document that explains system structure, key modules, data flow, and major technical decisions. Do not use for lightweight README edits or changelog work.
---

If this skill is used,say:SKILL_CR_UPD_ARCH_LOADED

## Goal

Create or update `ARCHITECTURE.md` so it explains the system in a way that is useful for future contributors.

## Inputs

- Current code layout
- Existing docs
- Build or runtime configuration if relevant
- User notes about intended architecture

## Required sections

When appropriate, include these sections:

- Overview
- Repository structure
- Main runtime components
- Data flow
- Key design decisions
- External dependencies and integrations
- Build / validation notes
- Known constraints

## Steps

1. Read the existing architecture document if present.
2. Inspect the repository layout and identify the top-level components.
3. Describe responsibilities, boundaries, and interactions.
4. Prefer concrete structure over abstract buzzwords.
5. Keep implementation details only where they help explain system behavior.
6. Reflect the current state of the codebase, not aspirational future design.
7. Call out uncertainty when the code does not clearly support a strong claim.

## Output rules

- Write for engineers new to the repo.
- Use references to code files and modules where possible.
- Prefer short sections and clear headings.
- Use diagrams only if explicitly requested.
- Preserve existing terminology and naming.

## Do not

- Do not duplicate large parts of the README.
- Do not claim clean layering if the repo does not actually follow it.
- Do not overstate guarantees, scalability, or modularity.
