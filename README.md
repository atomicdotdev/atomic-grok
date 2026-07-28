# atomic-grok

[Atomic VCS](https://atomic.dev) integration for [Grok Build](https://grok.x.ai) (xAI).

Automatic turn recording with AI provenance, intent tracking, and knowledge graph skills.

> **Definitive source:** this repository lives on Atomic storage at
> `https://atomic.atomic.storage/workspaces/oss/projects/atomic-grok/code`.
> A GitHub mirror is optional.

## What it does

- **1 session = 1 view** — a draft view is created automatically when you start Grok in an Atomic repo
- **Every turn records with provenance** — vendor (xAI), model (when present), session, turn timing
- **Tool executions tracked** — shell, file, and search tool calls feed a causal decision graph
- **Intent workflow** — `~/.grok/rules/atomic.md` guides problem-first development with vault intents
- **Skills on demand** — `atomic-vault`, `atomic-vcs`, and `code-intelligence`

## Requirements

- Atomic VCS CLI **>= 0.12.0** on your PATH (`atomic --version`)
- A project with an `.atomic/` repository (`atomic init`)
- [Grok Build](https://grok.x.ai) installed (`grok`)

## Install

### Quick start

```bash
atomic agent enable --agent grok
```

The enable command syncs this package from Atomic storage and installs:

1. **Hooks** — `~/.grok/hooks/atomic.json` (SessionStart, UserPromptSubmit, PreToolUse, PostToolUse, Stop, SessionEnd)
2. **Rules** — `~/.grok/rules/atomic.md` (system prompt for Atomic workflow)
3. **Skills** — `~/.grok/skills/{atomic-vault,atomic-vcs,code-intelligence}/SKILL.md`

### Development install

From a local checkout (no storage push required):

```bash
cd atomic-grok
atomic agent enable --agent grok --from .
```

Clean removal:

```bash
atomic agent disable --agent grok
```

## Usage

```bash
cd my-project
atomic init          # if not already an Atomic repo
grok                 # start Grok Build — hooks activate automatically
```

Global hooks under `~/.grok/hooks/` are always trusted by Grok. Project-local hooks would require `/hooks-trust`; this package installs globally so no extra trust step is needed.

You never need to run `atomic add` or `atomic record` — the hooks handle it.

## Lifecycle map

| Grok event         | Atomic verb            | Effect                                      |
|--------------------|------------------------|---------------------------------------------|
| `SessionStart`     | `session-start`        | Fork draft view for the session             |
| `UserPromptSubmit` | `user-prompt-submit`   | Capture prompt / open decision-graph Goal   |
| `PreToolUse`       | `pre-tool`             | Tool bookkeeping                            |
| `PostToolUse`      | `post-tool`            | Append tool node to decision graph          |
| `Stop`             | `stop`                 | Record change with AI provenance            |
| `SessionEnd`       | `session-end`          | Attestation + restore parent view           |

## Verify

```bash
# After a Grok session that edited files in an Atomic repo:
atomic agent attest
atomic change -p <hash>
atomic change -a <hash>
```

## Uninstall

```bash
atomic agent disable --agent grok
```

Receipt-driven: removes installed files and strips Atomic hook commands from `~/.grok/hooks/atomic.json`.
