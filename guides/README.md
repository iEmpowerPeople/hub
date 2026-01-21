# Tihana's Journey - AI Workflow

## 1. Introduction

This document consolidates resources, notes, and practical guidance around working with LLMs through terminal to create high grade workflows and outpouts.
It is intended as a living reference rather than a step‑by‑step tutorial.

Recommended Starting Steps:
1. Watch video Context Engeenering
2. Install Claude Code, CodeLayer, Humanlayer
3. Watch video from getting accustomed to Claude Code Environment section (Optional)
4. Go and Play
5. Understand Clis, Git + Github

Reference AI Dev Environment Image (view Resources) throughout your learning, to understand different elements and connect dots.

---
<br>

## 2. Mental Model for working with LLMs

### High‑quality podcast
- https://youtube.com/playlist?list=PLi60mUelRAbFqfgymVfZttlkIyt0XHZjt&si=kllhT6YvfAdNP1gv
- Focus: LLM limitations, best practices, Workflow Ideas
- Dex, one of the host, develops the wrapper **HumanLayer** and IDE **Codelayer**.

**Mandatory episodes** - Watch episodes with the visuals, dont only listen to - Conceptual level of Humanlayer and Codelayer workflow
- Context Engineering: https://www.youtube.com/watch?v=42AzKZRNhsk
- Claude for non‑coding tasks: https://youtu.be/NJcph4j9sNg

### Agents
- Main Claude/Codex terminal = main agent.
- Sub‑agents:
  - Have separate context windows.
  - Pass *results only* (handoffs), not full reasoning traces.
  - Enable parallelism, chaining, and evaluation/merging steps.
 
**Multi‑Agent workflows**
- Run the same prompt in 4 instances/agents in parallel.
- 5th agent evaluates outcomes, either picking the best or merging them for the 5th version.

Think of sub‑agents as **additional context windows** you can orchestrate.

---
<br>

## 3. Claude Code

### Installation
- Only read installation instructions, dont bother with the rest of the document: https://docs.google.com/document/d/1fyIBcSlbYNipWNURXOMUQL0Sb2DBHr9qBZKjgnQ7tl8/edit?tab=t.0#heading=h.iicwv6vi9ecb

### Getting Accustomed to Claude Code environment

#### Workflow basics -  To get familiar with the enviroment. How it looks, explicit steps etc.
- https://www.youtube.com/watch?v=rfDvkSkelhg&t=7s
- https://www.youtube.com/watch?v=32xfY8ct6Qw&t=1871s  

### Tips/ Tricks

#### Thinking tuning (increasing):
  - “think” → “think hard” → “think harder” → “ultrathink”  
  Note: *ultrathink often over‑thinks and degrades output quality.*

#### Environment
Anthropic has a nice table for when to use agents, skills, slash commands etc.

View it here (scroll down) -> https://code.claude.com/docs/en/skills  

---

## 4. Terminal, Git + GitHub

GitHub is a high‑efficiency collaborative file management environment. 

GitHub has great documentation: https://docs.github.com/en

When creating repos, think of name carefully. Changing the name later has very tedious side-affects. For folders and files too, but drastcially less.

### 1. Repositories
#### Hub-Public
- My public repo which includes this guide
- Purpose: you can edit the files there

#### AI Tools
- Our public repo for shared tools like agents, comannds, hooks, skills etc.
- Purpose: A main source of truth for shared tools. Due to how git/github works, its important to keep these seperate from our other docs, so that we can easily pull/push/clone into our settings, without including all the other “junk”

### 2. CLIs + GitHub
- Guide written by me - `/llm-cli-git-github.md`
- Explains ONLY how LLM CLIs and Git/GitHub work together - This is NOT a guide on how Git/GitHub work. It does NOT replace a guide specificaly for those

### 3. Edit Markdown with terminal/Codelayer + VS Code
Editing .md files with an AI CLI in terminal/CodeLayer is great.
Viewing the .md files whilst editing + small edits only through terminal/Codelayer chats is not as great.
Solution: Combine with a text editor like VS Code

Mental Model Workflow
```
AI CLI Chat: "code filename.md"
  ↓
VS Code opens (Cmd+K V for preview)
  ↓
Edit → Save → Close
  ↓
Chat: "git status" / "commit this"
```

Guide: `/edit-markdown-with-vs-code.md`

### 4. Data Privacy with CLIs
#### Claude Code
- opt out of data usage to train model in broswer account settings
- disable telemetry - Guide: `/deactivate-telemetry.md`

#### Gemini
- opt out of activity tracking
- disable telemetry - Guide: `/deactivate-telemetry.md`
  
#### Codex
- opt out of data usage to train model in broswer account settings
- disable telemetry - Guide: `/deactivate-telemetry.md`

---
<br>

## 5. Tools & Tips

### MCP Servers

#### Playwright MCP
- Allows the terminal to use the web natively for testing web apps.
- https://github.com/microsoft/playwright-mcp

### HumanLayer/ Codelayer
- Installation: https://www.humanlayer.dev/docs/introduction
- Understanding the step by step system specifics like commands, agents and workflows: "Use a prompt like /research_codebase I want to understand how to set up and use the HumanLayer system based on information you can find in this repo.  Create a setup guide for me. That will get you pretty far."

#### Codelayer for Windows
Codelayer is currently only supported for macos. According to the dev team, they are working on windows compatability.

I spoke with a dev from the humanlayer/codelayer company and they said they run their whole company through codelayer. Coding and non-coding tasks. 

It's fucking great.

Installation guide: `/codelayer-for-windows.md`

---
<br>

## 6. Resources

### AI Dev Environment
During your exploration of the environment, you will likely come across several terminologies. I wanted to provide you an infographic that would allow you to easierly structure them and understand their relationships.

![AI Dev Environment](https://github.com/iEmpowerPeople/Hub/blob/e896f5627592abee80d4b5e044831a86eedf23d6/Guides/Resources/Infographic%3A%20AI%20Pipeline%20Development%20Environment.jpeg)

### Command Line - Command Cheat Sheets
#### git commands
Downloadable PDF: `/resources/git-cheat-sheet.pdf`

#### gh commands
https://cli.github.com/manual/gh

#### Linux/Unix commands
Downloadable PDF: `/resources/linux-unix-cheat-sheet.pdf`

### People to follow
- Dex - Humanlayer/Codelayer Founder - 🦄 ai that works host - https://x.com/dexhorthy?s=20

### Resources
- Humanlayer/Codelayer discord server - very active and supportive devs - https://discord.gg/VJbMU2KC

### Personal AI systems
- Video: https://www.youtube.com/watch?v=Le0DLrn7ta0&t=1190s
- Blog post: https://danielmiessler.com/blog/personal-ai-infrastructure

### Agent use‑case examples from Nate B Jones
- Skim for inspiration:  
  https://docs.google.com/document/d/1iUntmg8Wx_Zx0UJm_JZ2JKLWW-rn76wJwNc6wUnFhiM/edit?tab=t.0#heading=h.fscq0ovl563v

