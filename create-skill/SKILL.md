---
name: create-skill
description: Create a new Claude Code skill (slash command). Use when the user wants to create, scaffold, or set up a new skill.
disable-model-invocation: true
argument-hint: [skill-name]
---

Create a new Claude Code skill named `$ARGUMENTS`.

## Steps

1. Ask the user what the skill should do and when it should be invoked.
2. Create the directory and `SKILL.md` file at the appropriate location based on scope:
   - **Personal** (all projects): `~/.claude/skills/<skill-name>/SKILL.md`
   - **Project-specific**: `.claude/skills/<skill-name>/SKILL.md`
   If the user doesn't specify, default to personal (`~/.claude/`).
3. Write the `SKILL.md` with YAML frontmatter and Markdown instructions following the format below.

## SKILL.md Format

A skill file has two parts: YAML frontmatter for configuration and a Markdown body with instructions.

```yaml
---
name: <skill-name>
description: <What this skill does and when Claude should use it>
# Include any of these optional fields as needed:
# disable-model-invocation: true   — Only manual /command invocation, Claude won't auto-trigger
# user-invocable: false            — Only Claude can invoke (background knowledge, not a command)
# allowed-tools: Read, Grep, Bash  — Tools usable without prompting
# context: fork                    — Run in an isolated subagent
# agent: Explore                   — Subagent type (Explore, Plan, Bash, etc.)
# argument-hint: [args]            — Hint shown in autocomplete
---

<Markdown instructions that Claude follows when the skill is invoked>
```

## Frontmatter Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Lowercase, hyphen-separated display name |
| `description` | string | What the skill does and when to use it |
| `disable-model-invocation` | bool | If `true`, only `/command` invocation works |
| `user-invocable` | bool | If `false`, only Claude can invoke it (not the user) |
| `allowed-tools` | string | Comma-separated list of tools the skill can use freely |
| `context` | string | Set to `fork` to run in an isolated subagent |
| `agent` | string | Subagent type: `Explore`, `Plan`, `Bash`, etc. |
| `argument-hint` | string | Autocomplete hint, e.g. `[filename]` or `[issue-number]` |

## Dynamic Features

- **Argument substitution**: `$ARGUMENTS` is replaced with everything after the `/command`. Use `$0`, `$1`, `$2` for positional args.
- **Shell command injection**: `` !`command` `` runs a shell command and injects its output into the prompt before Claude sees it.

## Guidelines

- Keep the description clear and specific so Claude knows when to auto-invoke (or so the user knows what the command does).
- Use `disable-model-invocation: true` for skills with side effects (deploy, commit, send messages).
- Use `user-invocable: false` for passive background knowledge skills.
- Keep instructions concise and actionable. Use numbered steps or bullet lists.
- If the skill needs arguments, include `argument-hint` and use `$ARGUMENTS` in the body.
