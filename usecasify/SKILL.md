---
name: usecasify
description: Analyze a documentation directory and produce a JTBD use-case report. Use when the user wants to reorganize docs around user jobs, get title recommendations, create parent topic outlines, or identify content gaps. Works on any directory of AsciiDoc or Markdown files.
disable-model-invocation: true
argument-hint: [directory-or-file]
---

# Usecasify: JTBD use-case analysis for documentation

Analyze a documentation collection and produce a markdown report that restructures it around Jobs to Be Done (JTBD) use-case categories.

## Input

`$ARGUMENTS` is a path to:
- A **directory** containing `.adoc` and/or `.md` files (most common)
- A **single file** to analyze in isolation (produces a minimal report)

## Step 1: Discover and inventory content

1. **Glob** recursively for `*.adoc` and `*.md` files in the given path.
2. **Antora detection:** If the directory contains a `pages/` subdirectory, analyze only files under `pages/`. Skip `partials/`, `examples/`, `assets/`, and `attachments/`.
3. **Navigation file detection (optional):** Look for `nav.adoc`, `antora.yml`, `navigation.ditamap`, or `_sidebar.md`. If found, note the current navigation order. If not found, proceed without it — it is never required.
4. **Read each file** and extract:
   - Title: AsciiDoc `= Title` or Markdown `# Title`
   - Metadata: `:diataxis-type:`, `:_mod-docs-content-type:`, `:description:`, or YAML frontmatter fields
   - Content body (for classification in Step 2)

## Step 2: Classify content types

For each file, determine its content type. Use metadata attributes as a starting hint, but always verify by analyzing the actual content. **Your content analysis overrides metadata attributes if they conflict.**

| Content Type | Indicators |
|---|---|
| **Concept** | Sections titled "Overview", "Introduction", "Architecture", or "About"; explains how something works; contains diagrams showing architecture or flow; describes relationships between components |
| **Tutorial** | Sections titled "Prerequisites", "Building your application", or "What you will learn"; provides a complete working example from start to finish; step-by-step instructions designed for learning; includes a downloadable quickstart or complete code example |
| **How-to** | Sections titled "Procedure", "Configuration", or "Steps"; focused on accomplishing a specific task; assumes existing knowledge; gets straight to the task without extended explanation |
| **Reference** | Lists of properties, configuration options, or API specifications; tables of settings with descriptions; generated configuration documentation; primarily lookup information |

## Step 3: Analyze jobs and map to categories

### Write job statements

For the document collection as a whole, write job statements in JTBD format:

> "When [situation], I want to [motivation], so I can [expected outcome]."

Guidelines for job statements:
- Focus on the user's goal, not the product or feature
- Use terms users would actually search for
- Identify **top-level jobs** (broad desired outcomes) and **sub-jobs** (steps toward the outcome)
- A sub-job can belong to multiple top-level jobs
- Job statements are for planning — they are not topic titles or short descriptions

### Map content to categories

Assign each topic to one of these standard categories. **Use only categories that have content — not every category applies to every doc set.**

| Category | Purpose | Typical content types |
|---|---|---|
| **Discover** | Overviews, architecture, conceptual explanations | Concept |
| **What's New** | Release notes, change summaries, migration guides | Reference |
| **Get Started** | First experience, tutorials, quickstarts | Tutorial |
| **Install** | Installation, initial setup, environment configuration | How-to |
| **Configure** | Customization, tuning, ongoing settings | How-to |
| **Observe** | Monitoring, logging, troubleshooting | How-to, Reference |
| **Extend** | Integration, automation, plugins, advanced customization | How-to, Tutorial |
| **Reference** | Properties, API docs, specifications, glossaries | Reference |

These categories are suggested defaults. If the content doesn't fit neatly, adapt the categories to match what is actually in the collection. You may rename, merge, or omit categories. The goal is to group content under headings that match how users think about their work, not to force content into predetermined boxes.

## Step 4: Recommend titles

For every topic, recommend a JTBD-compliant title based on its content type.

### Verb mapping

| Content Type | Verbs | Examples |
|---|---|---|
| **Concept** | Learn, Understand, Explore | "Understand identity providers", "Explore the build lifecycle" |
| **Tutorial** | Build, Create, Protect, Get started | "Build a secure application", "Create your first endpoint" |
| **How-to** | Configure, Enable, Use, Set up, Deploy | "Configure data sources", "Deploy to OpenShift" |
| **Reference** | _(Descriptive, no verb)_ | "OIDC configuration properties", "CLI command reference" |

### Title rules

- **Imperative verbs only** — never gerunds (-ing forms like "Configuring...", "Managing...")
- **3-7 words** — concise enough to avoid line-wrap in navigation
- **Focus on user outcomes**, not product internals
- **Top-level job titles** use natural user language, not product-specific terms
- **Child topics** (user stories) may use product-specific terminology where it aids clarity
- **No anthropomorphic language** — never "allows you to", "helps you", "enables you to", "lets you"
- **State benefit or context** when it helps distinguish similar-sounding tasks ("Control access with RBAC" vs "Control access with LDAP")
- **Industry-standard terms** (SSL, HTTP, OIDC, JWT, REST, gRPC) are acceptable and encouraged for searchability

## Step 5: Outline parent topics

**Skip this step for small collections (fewer than 6 files).** For larger collections, outline a JTBD parent topic for each top-level job. A parent topic is a concept page that serves as the entry point for users pursuing that job.

Each parent topic outline includes:

1. **Title** — Natural language describing the user's goal, not product or feature names
2. **Outcome description** — 2-4 sentences: what the user is trying to achieve and why it matters. Include both "the what" (the goal) and "the why" (why the outcome is important). This helps both human users and AI find the right content.
3. **Product-to-goal mapping** — Brief explanation of how the product's capabilities help the user achieve their goal. This bridges the user's perspective ("I need to secure my API") to the product's features ("OIDC, JWT, Basic auth").
4. **High-level steps** — Ordered list of the major steps to achieve the goal, each referencing the child topic(s) that provide the detail.
5. **Related content** — References to related jobs, external resources, or deeper reference material.

## Step 6: Write the report

Write all results to a markdown file at `<input-directory>/usecasify-report.md`. The report is a reorganization plan — it recommends how to restructure and retitle existing content around jobs, not a mandate to rewrite everything from scratch.

If the input is a single file, write to the same directory as that file.

Use this structure:

```markdown
# Usecasify report: <topic area or directory name>

## Job statements

| Job statement | Scope | Related categories |
|---|---|---|
| "When ..., I want to ..., so I can ..." | Top-level | Get Started, Configure |
| "When ..., I want to ..., so I can ..." | Sub-job | Configure |

## Content inventory

| File | Current title | Content type | Current category | Recommended title |
|---|---|---|---|---|
| `path/file.adoc` | Original Title | How-to | Configure | Recommended JTBD title |

## Current TOC

_(Include only if a navigation file was found in Step 1.)_

Reproduce the current navigation structure as a nested markdown list, so the reader can compare before and after.

## Proposed TOC

Nested markdown list showing the restructured navigation. Categories are top-level headings. Maximum 3 levels of nesting within a category (categories themselves don't count as a level).

- **Category**
  - Level 1: Job to be done
    - Level 2: Smaller job or user story
      - Level 3: User story

Order categories by user journey: Discover > Get Started > Install > Configure > Observe > Extend > Reference.

## Parent topic outlines

_(Omitted for collections with fewer than 6 files.)_

For each top-level job, provide the 5-part outline from Step 5.

## Content gaps

Bulleted list of:
- Jobs with no existing documentation
- Topics that don't map to any identified job (orphaned content)
- Parent topics that need to be written
- Missing content types (e.g., a how-to area with no supporting concept)
```

## Rules

- **Never modify source files.** The report is a recommendation document.
- **Every title recommendation must trace back to a job statement.** If a topic doesn't fit any job, flag it as orphaned in the gaps section.
- **Scale to collection size.** For single files or very small collections (< 6 files), produce a streamlined report: content inventory, title recommendations, and gaps. Skip parent topic outlines and simplify the proposed TOC.
- **Adapt categories to the content.** Do not force all 8 categories if the content doesn't warrant them.
- **If no navigation file is found**, note this and build the proposed TOC purely from content analysis.
