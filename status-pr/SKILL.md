---
name: status-pr
description: Generate a one-line manager-ready status item from a GitHub URL (PR, issue, or discussion).
argument-hint: "[github-url]"
disable-model-invocation: true
allowed-tools: Bash
---

# Generate a one-line status item

Given one or more GitHub URLs (PRs, issues, or discussions), fetch the metadata and produce a concise one-line status bullet for each, suitable for a manager update.

## Input

`$ARGUMENTS`

## Steps

1. Parse each URL to determine the type:
   - `*/pull/*` → PR: use `gh pr view <URL> --json number,title,body,url`
   - `*/issues/*` → Issue: use `gh issue view <URL> --json number,title,body,url`
   - `*/discussions/*` → Discussion: use `gh api` to fetch the discussion
2. Read the title and body (summary section if present).
3. Write a single status bullet per URL in this format:

```
- <owner/repo>#<number> <one-line description in past or present tense>
```

If the repo owner is `quarkusio`, omit the owner prefix (use `repo#number`). Otherwise include the owner (use `owner/repo#number`).

## Rules

- **One line per URL.** No multi-line output, no headers, no explanation.
- **Action-oriented.** Start the description with a verb (e.g., "Adds", "Generates", "Created", "Filed", "Opened", "Fixes").
- **Focus on outcomes**, not implementation details. What does this item deliver or track?
- **Keep it under 120 characters** (after the bullet prefix).
- **No title copy-paste.** Rewrite the title into a natural status update based on the body/summary.
- If the body has a `## Summary` or `## Description` section, prioritize that for understanding the item's purpose.
- If multiple URLs are provided (space-separated), produce one bullet per URL.
