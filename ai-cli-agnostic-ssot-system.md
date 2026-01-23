# Single Source of Truth (SSOT) for AI CLI Tools - Complete Guide

## What Problem Are We Solving?

Different AI coding tools (Claude Code, Codex CLI, Gemini CLI) each have their own folders for **skills** (smart helpers), **slash commands** (`/scan`), **agents** (multi-step workflows), and **hooks** (pre-commit checks). Writing these separately for each tool = chaos.

## The Solution:

One master folder (`~/ai-configs/`) that syncs to all tools automatically using Skillshare.

```text
❌ Chaos: 4 tools × 10 skills = 40 files to maintain
✅ SSOT: 1 folder → syncs everywhere instantly
```

## Non-Technical Analogy

Think of **3 chefs** (Claude, Codex, Gemini) who need the same 10 recipes. Instead of 3 separate recipe books, keep **one master cookbook** (`~/ai-configs/`). **Skillshare** is the waiter who copies recipes to each chef's station automatically.

## The Master Folder Structure

```bash
~/ai-configs/          # Your single source of truth
├── skills/            # /scan, /plot, 3d-scan-analyzer
├── agents/            # Multi-step workflows like reno-planner
├── hooks/             # Pre-commit guards, pre-tool checks
├── global/            # Personal defaults
└── local-templates/   # Project starters
```

## Quickstart (90 Seconds)

```bash
# 1. Create your master config
mkdir ~/ai-configs && cd ~/ai-configs

# 2. Skillshare initializes everything
npx skillshare@latest init .

# 3. Add a construction skill
cat > skills/3d-scan.yaml << EOF
name: 3d-scan-analyzer
slash: /scan
description: Analyze 3D scans for construction defects
tools: [mesh_viewer, volume_calc]
EOF

# 4. Deploy everywhere instantly
npx skillshare sync
```

Done. Now `/scan` works in Claude, Codex, and Gemini automatically.

# Global vs Local - When & Why

## Global Config (Personal Mac Defaults)

**Use when:** Solo work, daily commands you use everywhere

```bash
cd ~/ai-configs          # Home directory
npx skillshare sync      # → ~/.claude/, ~/.codex/, ~/.gemini/
```

**Files go to:** `~/.claude/skills/`, `~/.codex/commands/`

**Perfect for:** `/weather`, `/git-status`, personal tools

## Local Config (Project-Specific)

**Use when:** Team projects, specialized skills (Malta reno 3D scanning)

```bash
cd ~/projects/malta-reno/   # Inside git repo
npx skillshare sync         # → ./malta-reno/.claude/
```

**Files go to:** `./project/.claude/skills/` (project only)

**Perfect for:** `/load-bearing`, `/structural-analysis`

## Why Can't We Use Same Sync Method?

Scope conflicts:

```text
Global sync → ~/.claude/            # Your personal tools
Local sync → ./project/.claude/     # Team project tools
```

Skillshare auto-detects context:

```bash
cd ~                    # → Global mode (home dirs)
cd ~/project/           # → Local mode (project dirs)
npx skillshare sync     # Same command, smart deployment
```

Precedence: Local overrides global (Git standard).

# Slash Commands + Agents + Skills (All Supported)

One YAML file = 3 Claude features:

```yaml
# skills/reno-planner.yaml
name: renovation-planner
slash: /reno                # ← Manual: type /reno
description: |              # ← Auto: Claude loads when relevant
  Full renovation workflow with 3D scanning + load bearing analysis
agents:                     # ← Multi-step orchestrator
  - 3d-scan-analyzer
  - load-bearing-calc
  - material-estimator
```

After sync:

*   Type `/reno 5x3m room` → Full workflow
*   Say "plan this renovation" → Claude auto-loads
*   Works in all CLIs instantly

# GitHub Repo for Everything (Recommended)

One repo holds global + local + sync:

```bash
yourusername/ai-configs/
├── global/                # Personal defaults
├── local-templates/       # Project starters
├── skills/                # Universal YAML
├── .github/workflows/     # Auto-sync upstream
└── README.md
```

## Setup:

```bash
git clone https://github.com/yourusername/ai-configs.git ~/ai-configs
cd ~/ai-configs
npx skillshare init .  # Links to your repo
npx skillshare sync
git add . && git commit -m "Initial SSOT"
```

# Updating from Upstream (Matias Schema Changes)

After forking agentic-config templates:

```bash
cd ~/ai-configs
git remote add upstream https://github.com/MatiasComercio/agentic-config.git
git fetch upstream
git merge upstream/main
npx skillshare sync   # Deploys new schema everywhere
```

Or automate weekly (`.github/workflows/sync-upstream.yml`):

```yaml
on: schedule
  - cron: '0 2 * * 1'  # Weekly
jobs:
  sync:
    - uses: mukulkant/automated-sync@v1
      with:
        upstream_repo: "MatiasComercio/agentic-config"
```

## Complete Construction Workflow Example

```text
~/ai-configs/skills/3d-scan.yaml           → /scan everywhere
~/ai-configs/agents/reno-planner.yaml      → /reno everywhere
~/projects/malta-reno/.claude/             → Local overrides

Daily: cd ~/ai-configs && npx skillshare sync
Project: cd ~/malta-reno && npx skillshare sync
```

## Pro Alias (Add to ~/.zshrc)

```bash
echo 'alias ai-sync="npx skillshare sync"' >> ~/.zshrc
source ~/.zshrc
```

Type `ai-sync` anywhere → Skillshare auto-detects global vs local.

## Summary

**90 seconds setup → forever SSOT across all AI CLIs.** Edit one YAML file → `/scan` works everywhere. Global for personal, local for projects. Skillshare handles format conversion, context detection, validation automatically.

Share this repo with your team → everyone gets same `/reno` workflow instantly.

```bash
npx skillshare init ~/ai-configs && cd ~/ai-configs && npx skillshare sync
```

You're done. Welcome to the future of AI CLI workflows.
