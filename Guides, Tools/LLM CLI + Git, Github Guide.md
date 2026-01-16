### LLM CLI + Git, GitHub Guide

A dense, practical model for using LLM CLIs with Git/GitHub.

---

#### 1) Core model: content, history, assistant/AI

**Repo (concept):** Umbrella term for a directory of files; can exist without Git. 

**Git (`.git/`):** local database that adds **history + coordination** (commits, branches, tags, index, refs, objects). Never store project files inside it.  

**GitHub (Remote Git):** a shared copy of the Git database + Repo + collaboration surface (PRs, reviews, permissions).  

**LLM CLI:** 
- convenience machinery e.g Claude Code, Codex 
- reads/writes files in your working tree
- directories: caches, indexes, session metadata, permissions, optional rules.
- tool-owned data in **global** `~/.claude/`, `~/.codex/` and/or **project** `./.claude/`, `./.codex/`

**Split all AI-related files into:**
- **Policy (shareable intent):** human-authored rules/agents that encode “how we work”.
- **Machinery (local state):** tool-generated caches/indexes/logs/session artifacts.

Git is the ledger; CLIs are power editors. Use Git to review, attribute, revert, and ship.

**Rule:** commit policy; ignore machinery.

---

#### 2) Canonical project layout

```
my-project/
  src/ docs/ tests/ README.md ...
  AI_RULES.md                # project policy (recommended)
  .gitignore
  .git/                      # Git internal database (do not edit)
  .claude/ .codex/            # tool-local machinery (usually ignored)
```

macOS: dotfolders are hidden in Finder by convention (visible in command-line via `ls -a`).

**Never:**
- put project docs/code inside `.git/`, `.claude/`, `.codex/`.
- share or commit caches/indexes “to speed teammates up” (it backfires: churn, conflicts, non-portability).

---

#### 3) Git hygiene: what to commit vs ignore

##### Commit (policy, stable)
- `AI_RULES.md` (project operating rules).
- repo agents/workflows (text, stable, reviewable).
- anything teammates must share and review.

##### Ignore (machinery, churny)
- embeddings, indexes, file-summary caches.
- session metadata, timestamps, logs.
- anything that changes per run without human intent.

##### “Stable vs churny” test
Run the CLI twice with no intentional changes:
- if a file changes anyway → machinery → ignore.
- if it changes only when you edit it → policy → consider commit.

##### `.gitignore` patterns
**Simple (ignore all tool workspaces):**
```gitignore
.claude/
.codex/
```

**Selective (ignore all, allow explicit policy files):**
```gitignore
.claude/*
.codex/*

!.claude/rules.md
!.codex/rules.md
```

Prefer tool-agnostic policy (`AI_RULES.md`) unless a CLI requires a tool-specific path (you can direct tool-specific path to `AI_RULES.md`).

---

#### 4) AI CLIs in folders: scope is chosen by *where you launch*

**Launch:** Right click repo folder you want to work on -> launch terminal -> launch CLI

Most CLIs pick a **workspace root** as either:
- the directory you launched in, or
- the nearest parent that looks like a repo root (often contains `.git/`), or
- a configured root (tool-specific).

##### Parent-folder hazard
Launching in a parent directory containing multiple projects can cause:
- indexing unrelated repos,
- slower runs,
- accidental cross-project edits.

##### Multiple `.claude/` folders
If there are several `.claude/` folders in a tree, the CLI usually uses the one at the chosen workspace root; others are ignored. You typically do **not** need to edit `.claude/` files manually—control behavior via:
- correct launch directory (repo root),
- policy files,
- ignore/scope settings (if supported).

**Best practice:** 
- Launch CLIs from the **single repo root** you intend to modify.
- Control behavior via root choice + policy files; You mostly shouldn't need to hand-edit `.claude/` state

---

#### 5) Global vs project policy (and where it can live)

##### Global policy (your default behavior)
Use for “how the AI should behave everywhere”:
- brevity/information-density rules,
- text formatting,
- tone/style preferences,
- general safety boundaries.

##### Project policy (repo-specific behavior)
Use for “how this repo works”:
- scope (“only touch `src/` unless asked”),
- do-not-touch paths (migrations, vendor, generated files),
- toolchain (`pnpm`, `ruff`, `pytest`, etc.),
- definition of done (tests pass, docs updated, small diffs).

**Layering:** global policy → project policy → task prompt.

Policy files can be anywhere on disk; **tools will not auto-discover arbitrary Finder locations**. You must wire them in.

**Best practice:** 
Do **not** force different CLIs to share the same cache/state directories; formats differ and collisions/corruption are likely. Instead:

- **Global policy (cross-tool):** `~/AI/RULES.md` (any filesystem location is fine)
- **Project policy:** `project/AI_RULES.md` (commit with repo)

CLIs will not “discover” arbitrary Finder locations; they read either:
- fixed default paths (eg. `~/.claude/`, `~/.codex/`), or
- paths you explicitly wire in.

---

#### 6) One source of truth across different CLIs

Goal: maintain one policy once; make each tool read it without sharing tool state.

##### Recommended: canonical policy + adapters
- Canonical: **Global policy (cross-tool):** `~/AI/RULES.md` (any filesystem location is fine); **Project policy:** `project/AI_RULES.md` (commit with repo)
- Adapters: tool-specific expected paths point to the canonical file.

######## An Option: Symlinks (best default; no tool support required)
macOS:
```bash
ln -s /path/to/RULES.md ~/.claude/rules.md
ln -s /path/to/RULES.md ~/.codex/rules.md
```
Each CLI reads its normal file; the filesystem redirects to the canonical source.

**Do not** force CLIs (eg. `~/.claude/`, `~/.codex/`) to share the same cache/state directory; share only policy.

---

#### 7) Agents (programmable workflows): global vs project

Treat **agent definitions** like code if they are text, stable, reviewable.

- **Global agents:** reusable utilities (e.g., “write changelog”, “refactor helper”).
  - Location: `~/.claude/agents/` or your canonical store `~/AI/agents/`.
- **Project agents:** repo workflows (e.g., “update API client”, “run lint+fix+tests”).
  - Location: `my-project/agents/` or a tool-required path.

**Single source of truth for agents across tools:**
- Keep canonical agent specs in one directory (e.g., `~/AI/agents/` or `my-project/agents/`).
- Provide per-tool adapters:
  - symlink expected tool paths to canonical specs, or
  - generate tool-specific formats from canonical specs.
- Never share/generated caches/session state as “agents”.

---

#### 8) Relocating global tool directories on macOS

Most CLIs assume fixed defaults like `~/.claude` and `~/.codex`. If you move them without wiring, tools typically recreate fresh defaults in `~`.

##### Safe relocation: move + symlink back
```bash
mkdir -p ~/AI
mv ~/.claude ~/AI/.claude
mv ~/.codex  ~/AI/.codex
ln -s ~/AI/.claude ~/.claude
ln -s ~/AI/.codex  ~/.codex
```
Result: clean home directory; zero tool reconfiguration; deterministic paths.

If a CLI supports explicit overrides (flags/env), you may use them, but do not assume autodetection.

---

#### 10) Closed-source / regulated environments: why Git might not be the sharing mechanism

In many regulated setups Git is still used (often internally), but constraints may require alternatives:
- no external remotes; internal-only or air-gapped.
- restrictions on cloning/replication; controlled storage.
- approvals enforced via ticketing/CM rather than Git workflows.

When Git isn’t the distribution channel for policy, use controlled delivery:
- managed dotfiles/config distribution,
- internal templates,
- centralized file shares,
- config management tools.
The policy/machinery split still applies.

---

#### 11) Minimal templates (copy-paste)

##### Global rules (`RULES.md`)
```markdown
#### Response style
- Maximize information density; no redundancy.
- Prefer bullets; short paragraphs only when needed.
- Dates/times: ISO-8601 (YYYY-MM-DD) unless user requests otherwise.

#### Behavior
- Ask for missing constraints only if blocking; otherwise make safe assumptions and state them.
- Prefer small diffs; explain tradeoffs; never modify excluded paths.
```

##### Project rules (`AI_RULES.md`)
```markdown
#### Scope
- Default: modify only `src/` and `tests/`.
- Never edit: `db/migrations/`, `vendor/`, generated files.

#### Toolchain
- Use `pnpm` not `npm`.
- Run: `pnpm lint` + `pnpm test` before finishing.

#### Conventions
- TypeScript, async/await, no `any`.
- Keep changes minimal; update docs when behavior changes.
```

##### Selective `.gitignore`
```gitignore
.claude/*
.codex/*
!.claude/rules.md
!.codex/rules.md
```

##### Wrapper pattern (enforcement)
```bash
rules="$(cat /path/to/RULES.md)"
tool --system "$rules" "$@"
```

---

#### Operating rules (one screen)

- Keep **content** in working tree; keep **history** in `.git/`; keep **assistance** in tool dirs.
- Commit **policy**; ignore **machinery**.
- Launch CLIs from **repo root**.
- One canonical rules/agents source; connect per-tool via **symlinks/wrappers**.
- Never share caches as if they were source; GitHub should show intent, not tool exhaust.
