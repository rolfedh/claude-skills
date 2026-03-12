# Claude Code Skills for Documentation

Custom [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills for technical writing workflows. These skills automate documentation analysis, restructuring, and editing tasks.

**[View the JTBD skills documentation](https://rolfedh.github.io/claude-skills/)**

## Skills

| Skill | Invoke with | What it does |
|-------|-------------|--------------|
| **copyedit** | `/copyedit [file]` | Apply a writing style guide to fix voice, punctuation, word choice, and list formatting in AsciiDoc or Markdown files. |
| **create-skill** | `/create-skill [name]` | Scaffold a new Claude Code skill with the correct directory structure and SKILL.md frontmatter. |
| **diataxify** | `/diataxify review\|restructure [file]` | Audit or restructure documentation according to the [Diataxis](https://diataxis.fr/) framework (tutorials, how-to guides, reference, explanation). |
| **jtbdiataxify** | `/jtbdiataxify review\|restructure [file]` | Diataxis + JTBD: split mixed-content files into focused Diataxis pages with JTBD job statements, AI retrieval analysis, and user-centered titles. [Docs](https://rolfedh.github.io/claude-skills/jtbdiataxify) |
| **jtbdmoddocsify** | `/jtbdmoddocsify review\|restructure [file]` | Mod-docs + JTBD: split mixed-content files into Red Hat modular documentation modules with JTBD job statements, AI retrieval analysis, and user-centered titles. [Docs](https://rolfedh.github.io/claude-skills/jtbdmoddocsify) |
| **jtbdtitlesonly** | `/jtbdtitlesonly [file]` | Apply JTBD imperative titles to navigation without restructuring content. Lightweight retitling pass. [Docs](https://rolfedh.github.io/claude-skills/jtbdtitlesonly) |
| **modocify** | `/modocify review\|restructure [file]` | Check an AsciiDoc file against the [Red Hat modular documentation](https://redhat-documentation.github.io/modular-docs/) standard and flag issues by severity. |
| **quarkus-pr** | `/quarkus-pr` | Create a pull request for the Quarkus framework repository following contributing guidelines. |
| **rhssg** | `/rhssg [file]` | Audit AsciiDoc files against the [Red Hat Supplementary Style Guide](https://redhat-documentation.github.io/supplementary-style-guide/) for grammar, formatting, structure, code examples, links, accessibility, release notes, and support terminology. |
| **status-pr** | `/status-pr [github-url]` | Generate a one-line manager-ready status bullet from a GitHub PR, issue, or discussion URL. |
| **usecasify** | `/usecasify [directory-or-file]` | Analyze a documentation directory and produce a JTBD use-case report with job statements, content classification, title recommendations, proposed TOC, parent topic outlines, and content gap analysis. |

## Install

Copy the skills into your Claude Code configuration directory:

```bash
git clone https://github.com/rolfedh/claude-skills.git
cp -r claude-skills/*/ ~/.claude/skills/
```

Or, to use this repo directly as your skills directory:

```bash
git clone https://github.com/rolfedh/claude-skills.git ~/.claude/skills
```

## How skills work

Each skill is a directory containing a `SKILL.md` file with two parts:

1. **YAML frontmatter** — name, description, allowed tools, and invocation settings.
2. **Markdown body** — step-by-step instructions that Claude follows when the skill is invoked.

Place skill directories in `~/.claude/skills/` for personal (all-project) access, or in `.claude/skills/` within a project for project-specific access.

For details on creating new skills, run `/create-skill` or see the [Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code).

## Related frameworks

These skills apply established documentation frameworks:

- [Diataxis](https://diataxis.fr/) — A systematic approach to organizing documentation into four content types along two axes (action/cognition, learning/working).
- [Jobs to Be Done](https://strategyn.com/outcome-driven-innovation-process/jobs-to-be-done/) — A framework for writing user-centered titles that focus on outcomes, not features.
- [Red Hat modular docs](https://redhat-documentation.github.io/modular-docs/) — A standard for structuring technical content as reusable concept, procedure, and reference modules.
- [Red Hat Supplementary Style Guide](https://redhat-documentation.github.io/supplementary-style-guide/) — Style and language guidance that supplements the IBM Style guide for Red Hat product documentation.
