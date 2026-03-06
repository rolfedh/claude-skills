---
name: copyedit
description: Copyedit documentation files using the writing style guide. Use when the user wants to check or fix style, punctuation, voice, or word choice issues in AsciiDoc or Markdown files.
argument-hint: [file-path or glob]
allowed-tools: Read, Grep, Glob, Edit
---

Copyedit the target file(s) by applying every rule in the writing style guide at `~/.claude/style-guide.md`.

Target: `$ARGUMENTS`

If no target is given, use the currently open file from the IDE context. If neither is available, ask the user which file to edit.

## Steps

1. **Read the style guide** at `~/.claude/style-guide.md` to load the current rules.
2. **Read the target file(s).**
3. **Apply every rule** from the style guide. Check for:
   - Anthropomorphic language ("allows you to", "enables you to", "lets you", "helps you", "provides")
   - Passive or feature-centered phrasing that should be user-centered
   - Use of "we" (should be "you" or imperative)
   - Banned words ("violations" → "issues", "abort"/"aborting")
   - Em-dashes in prose
   - List punctuation: check every list (bullet, numbered, and callout) and apply the period rule consistently across all items in each list
4. **Make the edits** directly in the file(s) using the Edit tool.
5. **Present a summary** of all changes to the user, grouped by rule. Include the filename and line numbers.

## Rules for List Punctuation

The period rule must be applied per-list, not per-item:

1. Identify each distinct list in the file (a contiguous sequence of `*`, `.`, or `<N>` items).
2. Determine whether **any** item in that list is a complete sentence.
3. If yes, **every** item in that list gets a period.
4. If no (all items are fragments, bare nouns, or short labels), omit periods from all items.

Pay special attention to callout lists (`<1>`, `<2>`, etc.) that follow `[source]` code blocks. These are easy to overlook.

## Scope

- Edit only style and punctuation. Do not change technical content, restructure sections, or rewrite for clarity unless a style rule requires it.
- Do not add comments, docstrings, or annotations.
- Do not change code inside `[source]` blocks.
- Preserve all AsciiDoc structural elements (admonitions, anchors, xrefs, attributes, includes).
