### LLM CLI + Git, GitHub Guide

A dense, practical model for using LLM CLIs with Git/GitHub.

---

#### 1) Core model: content, history, assistant/AI

**Repo (concept):** A storage location for a project that tracks its version history and changes over time (usually with a version control system like Git).

**Directory:** A folder in your file system that can contain files and other folders.

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

#### 4) Repo Editing best Practices

##### Universal Workflow (Team + Solo)

**Always branch. Always PR.** One workflow everywhere, consistent muscle memory, preserves discussion history.

##### Standard Pattern (CLI)

```bash
# 1. Create branch
git checkout -b feature-name

# 2. Make changes, stage, commit
git add .
git commit -m "Add feature"

# 3. Push branch
git push -u origin feature-name

# 4. Create PR (requires gh CLI)
gh pr create --title "Add feature" --body "Description here"

# 5. Merge PR
gh pr merge --squash  # or --merge, --rebase
```

##### Branch Timing Visual

```
Timeline:  Edit Files → Stage → Branch? → Commit → Push
           ├─────────┼────────┼──────────┼────────┼─────→
           │         │        │          │        │
Can branch?│   ✓     │   ✓    │    ✓     │   ✓*   │  ✗
           │         │        │          │        │
Changes:   │ Unstaged│ Staged │  Staged  │ Locked │ Remote
           │ (moves) │(moves) │  (moves) │(fixed*)│(fixed)
```
*Post-commit: `git reset HEAD~1` → rebranch

##### When Staged Changes Move With You

✓ `git checkout -b new-branch` (creates + switches)
✓ `git switch -c new-branch` (modern syntax)
✓ `git checkout existing-branch` (if no conflicts)

✗ After `git commit` (locked to current branch)

##### Late Branch Creation (Valid Until Commit)

```bash
git add file.md              # Stage first
git checkout -b feature      # Then branch (staged changes move)
git commit -m "Add feature"
```

##### Team vs Solo (Same Workflow, Different Approval)

| Aspect | Team | Solo |
|--------|------|------|
| **Branching** | Required | Required |
| **PR creation** | Required | Required |
| **Approval** | Others review | Self-approve |
| **CI/CD** | Blocks merge if fails | Blocks merge if fails |
| **History** | Discussion preserved | Notes/context preserved |

##### Why Always PR (Even Solo)

- Searchable history ("Why did I change X?")
- Context preservation (link issues, screenshots)
- CI/CD validation before merge
- Practice for team workflows
- GitHub/GitLab timeline visualization

##### Summary

**Pattern:** Branch → Commit → Push → PR → Merge
**PR tools:** `gh pr create` (CLI) or GitHub UI
**Merge options:** `gh pr merge` (CLI) or UI button
**Staged changes:** Move with you until committed

```
Golden Rule: Branch everything. PR everything. Zero exceptions.
```

---

#### 5) AI CLIs in folders: scope is chosen by *where you launch*

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

#### 6) Global vs project policy (and where it can live)

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

#### 8) One source of truth across different AI CLIs

Mental Model: Have one source of truth for agents, slash commands, skills, hooks, scripts etc. + Adapter (all AI CLIs require different formatting)

Guide: `/AI CLI agnostic SSOT system.md`

---

#### 9) File Examples

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
- One canonical rules/agents source
- Never share caches as if they were source; GitHub should show intent, not tool exhaust.
