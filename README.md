## What Is This?

My `AGENTS.md` and `SKILL.md` files provide global agent behavior rules, reusable skills, and project-oriented presets for Node.js, Obsidian plugins, Swift, and SwiftUI for OpenAI Codex and compatible AI coding agents.

## Contents

| Path | Purpose |
| ------ | --- |
| `.codex/AGENTS.md` | Global agent rules for careful reasoning, small changes, and verification. |
| `.codex/create-update-architecture-md/SKILL.md` | Skill for creating or updating `ARCHITECTURE.md`. |
| `.codex/create-update-readme-md/SKILL.md` | Skill for creating or updating README feature sections. |
| `.codex/formatting-swiftui-code-style/SKILL.md` | Skill for SwiftUI code formatting. |
| `nodejs-project/AGENTS.md` | Node.js / NodeJS preset. |
| `obsidian-plugin-project/AGENTS.md` | Obsidian plugin preset. |
| `swift-project/AGENTS.md` | Swift preset. |
| `swiftui-project/AGENTS.md` | SwiftUI preset. |

## How to use

For global Codex rules and reusable skills:

```text
In_Your_OS_Your_Profile_Folder/
└── .codex/
    ├── AGENTS.md
    └── skills/
        ├── create-update-architecture-md/
        │   └── SKILL.md
        ├── create-update-readme-md/
        │   └── SKILL.md
        └── formatting-swiftui-code-style/
            └── SKILL.md
```

For project-specific behavior, copy the matching `AGENTS.md` into your project root folder, then edit it for your own project rules (if need it).

## Self-tests

Use these prompts to confirm that the expected instructions are loaded.

### Global agent

| Prompt | Expected response |
| --- | --- |
| `AGENTS GLOBAL-SELF TEST` | `GLOBAL_AGENTS_LOADED` |

### Project agents

| Project preset | Prompt | Expected response |
| --- | --- | --- |
| Node.js | `AGENTS PROJECT-SELF TEST` | `NODEJS_AGENTS_LOADED` |
| Obsidian plugin | `AGENTS PROJECT-SELF TEST` | `OBSIDIAN_DEV_AGENTS_LOADED` |
| Swift | `AGENTS PROJECT-SELF TEST` | `SWIFT_AGENTS_LOADED` |
| SwiftUI | `AGENTS PROJECT-SELF TEST` | `SWIFTUI_AGENTS_LOADED` |

### Skills

| Skill | Loaded marker |
| ----- | ------- |
| Create / update architecture document | `SKILL_CR_UPD_ARCH_LOADED` |
| Create / update README features | `SKILL_CR_UPD_README_LOADED` |
| SwiftUI code style | `SKILL_SWIFTUI_CODE_STYLE_LOADED` |

## Codex agent limits
Codex has limit (32 KiB by default) of the combined instruction-file size, look at the official Codex documentation for details:
https://developers.openai.com/codex/guides/agents-md
Pay attention at this parameter: `project_doc_max_bytes`

## Credits

The global file `.codex/AGENTS.md` is effectively a 1:1 copy of `CLAUDE.md` by Andrei Karpathy.
https://github.com/forrestchang/andrej-karpathy-skills/blob/main/CLAUDE.md?plain=1
