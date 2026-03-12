---
layout: default
title: /jtbdtitlesonly
nav_order: 2
---

# /jtbdtitlesonly

Apply JTBD titles to existing documentation navigation without restructuring content. This is the lightest-weight JTBD skill — it renames titles to be user-centered and action-oriented, leaving structure and content unchanged.

## When to use this skill

Use `/jtbdtitlesonly` when:

- You want to improve navigation titles without changing document structure or content
- You need a quick pass to replace gerund titles ("Configuring...") with JTBD imperatives ("Configure...")
- You want job statements for navigation categories but do not need a full audit or restructuring plan

Do **not** use this skill when:

- The document mixes content types and needs to be split — use [/jtbdiataxify](jtbdiataxify) or [/jtbdmoddocsify](jtbdmoddocsify) instead
- You need mod-docs compliance checks or Diataxis classification

## Invocation

```
/jtbdtitlesonly path/to/file-or-directory
```

**Accepted inputs:**
- A master `.adoc` file, ditamap, or navigation file
- A directory containing documentation to analyze
- A specific `<navref>` or section from `navigation.ditamap`

## What it does

### Step 1: Inventory the content

Reads the target file and every referenced source `.adoc` file. Determines each topic's content type by checking:

1. `:diataxis-type:` attribute
2. `:_mod-docs-content-type:` attribute
3. Actual content analysis (overrides attributes when they conflict)

### Step 2: Classify each topic

| Content type | Indicators |
|---|---|
| **Concept** | "Overview," "Introduction," "Architecture" sections; explains how things work |
| **Tutorial** | "Prerequisites," complete working example, step-by-step for learning |
| **How-to** | "Procedure," "Configuration" sections; focused task, assumes knowledge |
| **Reference** | Property lists, configuration options, API docs, tables of settings |

### Step 3: Write JTBD titles

Applies imperative verbs based on content type:

| Content type | JTBD verbs | Example |
|---|---|---|
| **Concept** | Learn, Understand, Explore | "Understand identity providers" |
| **Tutorial** | Build, Create, Protect, Get started | "Build a secure application" |
| **How-to** | Configure, Enable, Use, Set up, Deploy | "Configure logging" |
| **Reference** | _(Descriptive, no verb)_ | "Configuration properties" |

### Step 4: Write job statements

For each logical grouping, writes a job statement:

> "When [situation], I want to [motivation], so I can [expected outcome]."

Job statements are product-agnostic and focus on the user's problem and desired outcome.

### Step 5: Present proposed changes

Outputs:
1. A summary table: original title, new JTBD title, content type
2. The retitled navigation in the appropriate format (master `.adoc`, ditamap, etc.)
3. Job statements for each category grouping

## Title guidelines

1. **Focus on the desired outcome** — what is the user trying to get done?
2. **Avoid product-specific terms in job-level titles** — use natural language for parent headings; product terms are fine in individual topic titles
3. **Use terms your users use** — not internal product names
4. **Imperative verbs only** — never gerunds (-ing forms)
5. **3-7 words** — concise enough to avoid line-wrap in navigation
6. **State the benefit or context** when it distinguishes similar tasks
7. **No anthropomorphic language** — never "allows you to," "helps you," "enables you to," "lets you"

Industry-standard terms (SSL, HTTP, OIDC, JWT, REST, gRPC) are acceptable and encouraged for searchability.

## Rules

- Never modify source content files — only navigation/master files
- Never reorganize or restructure navigation — only retitle
- Keep commented-out future entries unchanged
- Master file H1 title should match the topichead navtitle in `navigation.ditamap`

## Example

**Before:**
```
Configuring data sources
Securing applications with OIDC
Getting started with Quarkus
OIDC configuration properties reference
```

**After:**
```
Configure data sources
Secure applications with OIDC
Build your first Quarkus application
OIDC configuration properties
```