# Atomic VCS Agent (Grok Build)

You use **Atomic VCS** (not git). A draft view is created for each session automatically by the Atomic hooks.

## Version control rules

- **Never use `git` for repository operations.** Do not run `git status`, `git diff`, `git log`, `git add`, `git commit`, `git checkout`, `git branch`, `git merge`, `git pull`, `git push`, or any other `git` command.
- Use the **Atomic CLI** for version-control context:
  - `atomic status` instead of `git status`
  - `atomic diff` instead of `git diff`
  - `atomic log` instead of `git log`
  - `atomic change <hash>` instead of `git show <hash>`
  - `atomic view list` instead of `git branch`
  - `atomic view switch <name>` instead of `git checkout <name>` when the user explicitly asks to switch views
  - `atomic pull` / `atomic push` instead of `git pull` / `git push`
- In this Grok Build integration, do **not** run `atomic add` or `atomic record`; hooks record the turn automatically with AI provenance.

## Every unit of work follows this sequence

### 1. Research project memory (when supported)

```bash
atomic vault context --help
```

If that succeeds, research durable project memory before creating an intent (see the `atomic-vault` skill). If the CLI reports the command is unavailable, continue with the intent-only workflow and do not invent memory.

### 2. Check for an existing intent

```bash
atomic vault intent list
```

If an existing intent covers the same unit of work, continue it. Do not create a duplicate.

### 3. Create an intent if needed

```bash
atomic vault intent create --title "Short title"
```

### 4. Define the problem

The user's prompt is usually a **solution** ("build me X"). Reframe it as a **problem statement**.

Ask clarifying questions if the problem is ambiguous. Do not guess — ask.

Once the problem is clear, write into the intent file:

- **Problem statement** — what problem are we solving and why
- **Success criteria** — concrete, testable conditions that mean "done"
- **Tasks** — ordered list of work items

Then run `atomic vault sync` so the database reflects your file edits. Present the intent and wait for explicit acceptance before writing code. After acceptance:

```bash
atomic vault sync
atomic vault intent update <ID> --status planned
```

### 5. Execute the tasks

Work through the TODOs in order. After completing each one:

1. **Verify** it meets its criteria.
2. **Edit the intent file** with your file editing tools to mark tasks done (`[ ]` → `[x]`).
3. **Sync**:

```bash
atomic vault sync
```

**Use file editing tools to check off tasks — not bash, sed, or Python rewrites.**

### 6. Finish the intent

```bash
atomic vault sync
atomic vault intent update <ID> --status done
```

**Do NOT run `atomic add` or `atomic record`.** Hooks record automatically with provenance when the turn ends.

## Rules

- **Problem first.** Reframe solution-requests as problems. Ask questions if unclear.
- **Write the intent file before coding.** The plan lives in the file, not only in chat.
- **Do run `atomic vault sync` after editing any `.vault/` file**, and before `intent show`/`update`.
- **Do not run `atomic add` or `atomic record`.** Hooks handle this with provenance.
- **Do not create or switch views.** The session view is created automatically.
- **Do not run `atomic agent enable`.** The integration is already configured.
- Prefer Atomic code intelligence over blind `grep`/`find` for exploration.

## Skills

Load these on demand:

- `atomic-vault` — intent/goal lifecycle and memory
- `atomic-vcs` — status, log, change (`-p` provenance, `-a` AI attestation), diff
- `code-intelligence` — knowledge graph and content search
