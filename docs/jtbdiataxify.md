---
layout: default
title: /jtbdiataxify
---

# /jtbdiataxify

Analyze and restructure documentation using the [Diataxis](https://diataxis.fr/) framework combined with [Jobs to Be Done](https://jobs-to-be-done.com/jobs-to-be-done-a-framework-for-customer-needs-c883cbf61c90). Splits mixed-content files into focused pages typed to a single Diataxis quadrant, with JTBD-aligned titles.

## When to use this skill

Use `/jtbdiataxify` when:

- A document mixes tutorials, how-to guides, reference, and explanatory content
- You want to split a large file into focused pages organized by Diataxis type
- You use the Diataxis framework (common in Quarkus upstream, Antora sites, and other open-source projects)

Do **not** use this skill when:

- You only need to retitle navigation — use [/jtbdtitlesonly](jtbdtitlesonly) instead
- You follow Red Hat modular documentation conventions — use [/jtbdmoddocsify](jtbdmoddocsify) instead

## Invocation

```
/jtbdiataxify review path/to/file.adoc
/jtbdiataxify restructure path/to/file.adoc
```

**Modes:**
- **`review`** — Audit only. Classifies content by Diataxis type, writes JTBD job statements, recommends titles, and produces an AI retrieval analysis. Does not modify any files.
- **`restructure`** — Full workflow. Audits, proposes a plan, gets your approval, then rewrites, verifies content preservation, and commits.

If neither mode is specified, defaults to `restructure`.

## The Diataxis framework

Diataxis organizes documentation along two axes:

|  | **Learning** | **Working** |
|---|---|---|
| **Practical** | Tutorial | How-to guide |
| **Theoretical** | Explanation | Reference |

Each type serves a different user need. Mixing types in a single document weakens all of them.

### Content types at a glance

| Type | Purpose | Voice | JTBD verbs |
|---|---|---|---|
| **Tutorial** | Guided learning experience for beginners | Direct imperatives with explanations at each step | Build, Create, Get started |
| **How-to** | Task-oriented directions for competent users | Concise imperatives, assumes knowledge | Configure, Deploy, Enable, Set up |
| **Reference** | Authoritative lookup information | Neutral, factual, austere | _(Descriptive noun phrase)_ |
| **Explanation** | Background, context, and "why" | Discursive, reflective | Understand, Learn, Explore |

## Workflow

### Review mode

The `review` mode produces a structured audit report with:

1. **Overall assessment** — Summary of Diataxis alignment
2. **Strengths** — What the docs do well, with specific citations
3. **Opportunities** — Diataxis boundary issues with concrete recommendations
4. **Job statements** — JTBD job statements for the content
5. **Title recommendations** — Current vs. recommended JTBD titles
6. **AI retrieval analysis** — How focused pages with JTBD titles improve search discoverability
7. **Content manifest** — Counts of code blocks, admonitions, headings, section IDs, and incoming xrefs
8. **Summary scorecard** — Compliance by Diataxis quadrant

The report is saved to a markdown file alongside the target file.

### Restructure mode

The full workflow proceeds through 10 phases:

| Phase | What happens |
|---|---|
| **1. Audit** | Classify every section by Diataxis type; build a content manifest |
| **2. Plan** | Write job statements, propose pages, run AI retrieval analysis, validate content journeys |
| **3. Rewrite** | Split content into separate files by Diataxis type |
| **4. Navigation** | Update nav files with JTBD-structured hierarchy |
| **5. Cross-references** | Update all incoming xrefs to point to the correct new files |
| **6. Verification** | 4-check content comparison (code blocks, callouts, prose, structural elements) |
| **7. Build** | Run the documentation build to catch broken references |
| **8. Commit** | Commit on a feature branch |
| **9. Copyedit** | Fix anthropomorphic language, voice, and punctuation |
| **10. Pull request** | Push and create a PR with a structured description |

You approve the plan before any files are modified.

## File naming

Files follow the pattern `<topic>-<diataxis-type>.adoc`:

| Suffix | Type | Example |
|---|---|---|
| `-tutorial` | Tutorial | `aesh-tutorial.adoc` |
| `-howto` | How-to guide | `aesh-howto.adoc` |
| `-reference` | Reference | `aesh-reference.adoc` |
| `-concept` | Explanation | `aesh-concept.adoc` |

Each file gets a `:diataxis-type:` attribute in its header.

## Content verification

Before deleting the original file, a verification agent runs four checks:

1. **Code block comparison** — Line-by-line diff of every `[source,...]` block
2. **Callout verification** — Markers, list items, and explanatory text preserved
3. **Prose passage diff** — Every non-structural passage found verbatim, paraphrased, or flagged as missing
4. **Structural element verification** — Headings, admonition counts, and section IDs match

Any content loss must be fixed before proceeding. You are asked explicitly whether to delete the original file.

## Example

**Before:** A single 500-line `security-oidc.adoc` mixing concepts, procedures, and configuration reference.

**After:**

| File | Title | Diataxis type |
|---|---|---|
| `security-oidc-concept.adoc` | Understand OIDC authentication | Explanation |
| `security-oidc-howto.adoc` | Configure OIDC for your application | How-to |
| `security-oidc-auth0-tutorial.adoc` | Build an OIDC application with Auth0 | Tutorial |
| `security-oidc-reference.adoc` | OIDC configuration properties | Reference |

[Back to overview](.)
