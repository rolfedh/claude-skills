---
layout: default
title: Home
nav_order: 1
permalink: /
---

# JTBD Documentation Skills

Three Claude Code skills for applying [Jobs to Be Done](https://jobs-to-be-done.com/jobs-to-be-done-a-framework-for-customer-needs-c883cbf61c90) (JTBD) to technical documentation. Each skill targets a different scope — from lightweight title changes to full document restructuring.

## Choose the right skill

| Skill | Scope | When to use |
|-------|-------|-------------|
| [/jtbdtitlesonly](jtbdtitlesonly) | Titles only | Rename navigation titles to JTBD imperatives without changing content or structure |
| [/jtbdiataxify](jtbdiataxify) | Diataxis + JTBD | Split mixed-content files into focused pages typed to Diataxis quadrants, with JTBD titles |
| [/jtbdmoddocsify](jtbdmoddocsify) | Mod-docs + JTBD | Split mixed-content files into Red Hat modular documentation modules, with JTBD titles |

## What is JTBD?

Jobs to Be Done frames documentation around user goals rather than product features. Instead of "Configuring data sources," a JTBD title says "Configure data sources" — an imperative that tells users exactly what they can accomplish.

A *job statement* captures the user need behind each piece of content:

> "When [situation], I want to [motivation], so I can [expected outcome]."

Job statements are product-agnostic and outcome-focused. They drive title choices, content organization, and navigation hierarchy.

## Common concepts

All three skills share these principles:

**Imperative titles, never gerunds.** "Configure logging" not "Configuring logging." Titles use verbs matched to content type:

| Content type | JTBD verbs | Example |
|---|---|---|
| Concept / Explanation | Understand, Learn, Explore | "Understand identity providers" |
| Tutorial / Procedure | Configure, Deploy, Build, Create, Enable | "Deploy to OpenShift" |
| Reference | _(Descriptive noun phrase)_ | "OIDC configuration properties" |

**No anthropomorphic language.** Never "allows you to," "helps you," "enables you to," or "lets you." Use "With X, you can..." or "Use X to..." instead.

**AI retrieval analysis.** `/jtbdiataxify` and `/jtbdmoddocsify` include a retrieval analysis showing how focused, well-titled pages improve search and AI discoverability compared to a single mixed-content page.

## Install

Copy the skills into your Claude Code configuration directory:

```bash
git clone https://github.com/rolfedh/claude-skills.git
cp -r claude-skills/*/ ~/.claude/skills/
```

Or use this repo directly as your skills directory:

```bash
git clone https://github.com/rolfedh/claude-skills.git ~/.claude/skills
```
