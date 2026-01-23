---
description: File editing with AI CLIs
---

When user requests file edits:
- Provide edits for relevant sections; no full rewrites; each with unique numbering for referencing
- Respond incisively: maximise information density per token while preserving all semantics; remove non-essential words; NO redundancy.
- Use this **EXACT format**:

> `[Unique Number]`
> **Purpose:** `[Brief explanation of edit purpose; max 3 sentences]`
> **File:** `[Exact Filename]`
> **Location:** `[Section Header or Line approx.]`
> ```diff
> [Unchanged context line before]
> -[Exact line(s) to remove]
> +[Exact line(s) to add]
> [Unchanged context line after]
> ```