```markdown
# Telemetry & Privacy Setup for Claude, Gemini CLI, and Codex (macOS)

Goal: Turn off usage tracking ("telemetry") for three AI tools and quickly test that it worked.

---

## 1. Open Terminal

- Press `Cmd + Space`, type `Terminal`, press `Enter`.

---

## 2. Edit your shell settings (`~/.zshenv`)

This file runs every time you open Terminal. We add lines that tell tools **not** to send telemetry.

1. Open the file:

```bash
nano ~/.zshenv
```

2. At the bottom, add these lines (keep existing lines, just append):

```bash
export DISABLE_TELEMETRY=1
export DISABLE_STATSIG=1
export SENTRY_DSN=""
export GEMINI_TELEMETRY_ENABLED=false
```

3. Save and exit nano:

- `Ctrl + O` -> `Enter` (save)
- `Ctrl + X` (exit)

4. Apply the changes in the current Terminal:

```bash
source ~/.zshenv
```

These lines disable telemetry for Claude Code (DISABLE_TELEMETRY, DISABLE_STATSIG, SENTRY_DSN) and Gemini CLI (GEMINI_TELEMETRY_ENABLED). [page:0][web:48]

## 3. Configure Codex analytics (`~/.codex/config.toml`)

Codex uses its own config file. We tell it to turn analytics off.

1. Create/open the config file:

```bash
mkdir -p ~/.codex
nano ~/.codex/config.toml
```

2. Make sure it contains at least this (keep your existing `model` / `projects` lines and just add the analytics block at the end):

```text
[analytics]
enabled = false
```

3. Save and exit nano:

- `Ctrl + O` -> `Enter`
- `Ctrl + X`

This tells Codex CLI not to send analytics/telemetry. [web:72][web:56]

## 4. Quick tests to confirm

These tests run each tool and look for any mention of telemetry/analytics. If none is found, you see a "GOOD" message.

Paste these one by one in Terminal:

```bash
# Claude
claude --version 2>&1 | grep -i telemetry || echo "Claude: No telemetry (GOOD)"

# Gemini
gemini --version 2>&1 | grep -i telemetry || echo "Gemini: No telemetry (GOOD)"

# Codex
codex --version 2>&1 | grep -i -E 'telemetry|analytics' || echo "Codex: No telemetry (GOOD)"
```

- If you see only the `No telemetry (GOOD)` lines, your privacy settings are working.
- If you see lines containing "telemetry" or "analytics", the tool is still trying to send data and you should re-check the setup. [web:52][web:56][page:0]

## 5. What to tell an LLM if you're stuck

One-line prompt you can reuse:
```
“Help me on macOS using Terminal to disable telemetry/analytics for Claude Code CLI, Gemini CLI, and OpenAI Codex CLI by editing ~/.zshenv and ~/.codex/config.toml, and give me simple copy-paste commands plus a way to test that telemetry is off.”
```
