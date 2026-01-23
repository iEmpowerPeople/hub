# Edit Markdown with VS Code

## Workflow with AI CLI Chat

```
Chat: "code filename.md"
  ↓
VS Code opens (Cmd+K V for preview)
  ↓
Edit → Save → Close
  ↓
Chat: "git status" / "commit this"
```

**One command center, multiple tools.**


## Workflow

1. Edit left pane (raw markdown)
2. Preview right pane (GitHub-styled render)
3. Scroll syncs automatically
4. Save: Cmd+S
5. Return to chat for git operations


## Usage

```bash
# Open file
code filename.md

# Side-by-side preview
# Press: Cmd+K then V
```


## Install

```bash
# macOS
brew install --cask visual-studio-code

# Enable terminal command
# In VS Code: Cmd+Shift+P → "Shell Command: Install 'code' command in PATH"
```


## Extensions (GitHub Similarity)

```bash
code --install-extension yzhang.markdown-all-in-one
code --install-extension bierner.markdown-emoji
code --install-extension bierner.markdown-mermaid
code --install-extension bierner.markdown-preview-github-styles
code --install-extension bierner.github-markdown-preview
```

**What each adds:**
- **markdown-all-in-one**: Auto-lists, table formatting, TOC
- **markdown-emoji**: `:rocket:` → 🚀
- **markdown-mermaid**: Diagram rendering
- **github-styles**: GitHub CSS (fonts, colors, spacing)
- **github-markdown-preview**: Task lists, GFM features

**Remaining differences from GitHub:**
- Alerts (`> [!NOTE]`)
- GitHub-specific links (`#123`, `@user`)
- Exact CSS pixel-perfect matching

**Final verification:** `git push` → view on GitHub


## VS Code vs Alternatives

| Editor | Pros | Cons | GitHub Accuracy | Cost | Use Case |
|--------|------|------|-----------------|------|----------|
| **VS Code** | Side-by-side editing+preview, extensions, Git integration, AI CLI workflow | Not pixel-perfect | ~95% | Free | **Best all-around** |
| **Typora** | WYSIWYG, seamless editing | Paid, no code view | ~90% | $15 | Quick visual edits |
| **grip** | 100% GitHub rendering | Browser only, no editing, requires separate editor | 100% | Free | Final verification |
| **github.dev** | 100% GitHub accurate, VS Code UI in browser | Remote only, sync issues with local clone | 100% | Free | Web-only workflows |
