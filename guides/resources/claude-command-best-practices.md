# Claude Command Best Practices Guide

Based on analysis of the [humanlayer commands](https://github.com/humanlayer/humanlayer/tree/main/.claude/commands), these patterns and best practices emerge for creating effective Claude Code commands.

## Command Structure

### 1. Frontmatter
Every command should start with YAML frontmatter:

```yaml
---
description: Brief, action-oriented description (< 80 chars)
model: opus  # Optional: specify if command needs more powerful model
---
```

The description should be imperative and specific (e.g., "Generate comprehensive PR descriptions" not "PR tool").

### 2. Command Purpose Statement
Start with a clear statement of what the command does:

```markdown
You are tasked with [specific task] [context about why/when].
```

Example: "You are tasked with generating a comprehensive pull request description following the repository's standard template."

### 3. Process Steps Structure
Use numbered sections with clear hierarchy:

```markdown
## Steps to follow:

1. **Action verb phrase:**
   - Sub-step with specific instruction
   - Another sub-step
   - What to do if X happens

2. **Next major step:**
   - Implementation details
   - Error handling
```

## Core Principles

### Read Before Acting
**Pattern**: Always read files FULLY before taking action.

```markdown
1. **Read the [relevant file]:**
   - Check if `path/to/file` exists
   - Read it FULLY (without limit/offset)
   - If doesn't exist, inform user about [setup requirement]
```

From `/commit`: "Review conversation history, run `git status` and `git diff`"
From `/create_plan`: "Read all mentioned files immediately and FULLY"

**Anti-pattern**: Don't assume file contents or make changes without reading first.

### AI Transparency vs. User Authorship
**Two opposing patterns found:**

**Built-in Claude Code approach** (from general instructions):
- Adds "Co-Authored-By: Claude Sonnet 4.5"
- Includes "Generated with Claude Code" in outputs

**Humanlayer approach** (from `/commit`):
- "NEVER add co-author information or Claude attribution"
- "Commits should be authored solely by the user"
- No "Generated with Claude" messages

**Best practice**: Follow the user's preference. Default to transparency (Claude's approach), but allow commands to override for user-authored work (humanlayer's approach for commits).

### Plan, Present, Confirm, Execute
**Pattern**: Interactive approval workflow

```markdown
1. Gather information
2. Analyze and create plan
3. Present plan to user (show exactly what will happen)
4. Get explicit approval
5. Execute
6. Report results
```

Example from `/commit`:
1. Review changes
2. Plan commits (identify groupings, draft messages)
3. Present to user (list files, show messages, request confirmation)
4. Execute (add files, create commits, display results)

### Atomic, Focused Operations
**Pattern**: Break work into smallest logical units

From `/commit`: "Commits should be focused and atomic when possible"
- Use targeted `git add path/to/file` not `git add .` or `git add -A`
- Group related changes logically
- One concern per commit

### Graceful Degradation & Error Handling
**Pattern**: Handle missing dependencies and errors gracefully

```markdown
1. **Check for dependency:**
   - If exists, proceed
   - If missing, inform user about setup: "Your [system] is incomplete, you need to [action]"
   - Provide specific path or command to fix

2. **If command fails:**
   - If error is [specific type], instruct: "Run [command] to fix"
   - Otherwise, show error and suggest resolution
```

Example from `/describe_pr`:
- Checks for template file, guides user to create if missing
- Handles "no default remote" error with specific fix: `gh repo set-default`

### Verification & Quality Assurance
**Pattern**: Separate automated from manual verification

```markdown
### Success Criteria:

#### Automated Verification:
- [ ] Tests pass: `make test`
- [ ] Linting passes: `make lint`
- [ ] Build succeeds: `npm run build`

#### Manual Verification:
- [ ] Feature works as expected in UI
- [ ] Performance is acceptable
- [ ] Edge cases verified

**Implementation Note**: After automated verification passes, pause for manual confirmation before proceeding.
```

From `/create_plan`: Always include both types, with checkboxes and specific commands.

### Parallel Execution When Possible
**Pattern**: Gather information concurrently

```markdown
2. **Spawn parallel research tasks:**
   - Use Task agent for [aspect A]
   - Use Task agent for [aspect B]
   - Use Task agent for [aspect C]

3. **Wait for ALL tasks to complete** before proceeding
```

From `/create_plan`: Spawns multiple specialized agents (codebase-locator, codebase-analyzer, etc.) in parallel.

### Be Skeptical & Interactive
**Pattern**: Question, verify, iterate - don't assume

From `/create_plan`:
```markdown
1. **Be Skeptical**:
   - Question vague requirements
   - Ask "why" and "what about"
   - Don't assume - verify with code

2. **Be Interactive**:
   - Don't write the full plan in one shot
   - Get buy-in at each major step
   - Allow course corrections
```

### Explicit Scope Definition
**Pattern**: State what's NOT included

```markdown
## What We're NOT Doing

[Explicitly list out-of-scope items to prevent scope creep]
```

This prevents misunderstandings and feature creep.

## Command Anti-Patterns

### Don't Use Placeholders
**Anti-pattern**:
```markdown
1. Create commit: `git commit -m "[YOUR MESSAGE HERE]"`
```

**Better**:
```markdown
1. Draft commit message based on changes
2. Present message to user
3. After approval, create commit: `git commit -m "actual message"`
```

### Don't Skip Context Gathering
**Anti-pattern**: Jump straight to execution without reading files/state

**Better**: Always gather full context first (git status, file reads, etc.)

### Don't Make Assumptions About File Locations
**Pattern**: Check existence, provide setup guidance if missing

```markdown
1. Check if `expected/path/to/file` exists
2. If not, inform user: "You need to create [file] at [path]"
3. Otherwise, proceed
```

### Don't Leave Unresolved Questions
**Pattern**: Resolve all questions before finalizing output

From `/create_plan`: "No Open Questions in Final Plan - If you encounter open questions during planning, STOP. Research or ask for clarification immediately."

## Formatting Conventions

### File Paths
- Use backticks: \`path/to/file.ext\`
- Include full paths when relevant
- Reference line numbers when useful: \`file.ts:42\`

### Commands
- Use backticks: \`make test\`
- Include full command with flags: \`gh pr view --json url,number,title\`
- Show error handling: \`command 2>/dev/null\` when errors are expected

### Code Blocks
Use fenced code blocks with language specification:

````markdown
```typescript
// Actual code to add/modify
```
````

### Checklists
Use GitHub-style checkboxes:
```markdown
- [ ] Unchecked item
- [x] Checked item
```

## Template Structure

Here's a complete command template following these patterns:

````markdown
---
description: Action-oriented description of what this does
model: opus  # Optional: only if needed
---

# Command Name

You are tasked with [specific goal] [when/why context].

## Steps to follow:

1. **Gather context:**
   - Check if [required file/state] exists
   - If missing, inform user: "[Setup guidance]"
   - Read [relevant files] FULLY
   - Run [status commands] to understand current state

2. **Analyze and plan:**
   - [What to analyze]
   - [What decisions to make]
   - [What questions to ask user if needed]

3. **Present plan for approval:**
   ```
   [Show user exactly what will happen]
   ```
   - Ask: "[Specific approval question]"

4. **Execute approved plan:**
   - [Specific action with exact command]
   - [Another action]
   - Handle errors: If [specific error], [specific fix]

5. **Verify and report:**
   - Check that [expected outcome] occurred
   - Show user: "[Results summary]"

## Important notes:
- [Critical guideline about behavior]
- [Another important consideration]
- [Edge case handling]

## Examples:

```bash
# Example usage
/command-name parameter
```

This will:
- [What happens step 1]
- [What happens step 2]
- [Expected outcome]
````

## Summary

The best Claude commands follow this philosophy:

1. **Read first, act later** - Gather full context before taking action
2. **Plan, present, confirm** - Show user exactly what will happen before doing it
3. **Be atomic and focused** - Do one thing well, break complex tasks into phases
4. **Handle errors gracefully** - Guide users to fix issues, don't just fail
5. **Verify thoroughly** - Separate automated from manual verification
6. **Question assumptions** - Be skeptical, interactive, and iterative
7. **Be specific** - Exact commands, paths, and instructions - no placeholders
8. **Define scope explicitly** - State what IS and ISN'T included

These patterns create commands that are reliable, user-friendly, and maintain high quality standards.
