# JTBD Titles Only: Apply JTBD titles without full restructuring

Apply Jobs to Be Done (JTBD) titles to existing documentation navigation without restructuring content. This is a lightweight pass that renames titles to be user-centered and action-oriented, leaving structure and content unchanged.

## Input

The user provides one of:
- A file path to a master `.adoc` file, ditamap, or navigation file
- A directory path containing documentation to analyze
- A specific `<navref>` or section from `navigation.ditamap` to update

## Workflow

### Step 1: Read and inventory the content

1. Read the target file(s)
2. For each xref or topic reference, read the corresponding source `.adoc` file
3. Identify the content type using one of these methods:
   - Check for `:diataxis-type:` attribute in the AsciiDoc header
   - Check for `:_mod-docs-content-type:` attribute
   - Analyze the actual content to verify the type
   - **If your content analysis conflicts with the diataxis-type attribute, your analysis overrides the attribute**

### Step 2: Classify each topic by content type

| Content Type | Indicators |
|---|---|
| **Concept** | "Overview", "Introduction", "Architecture" sections; explains how things work; diagrams |
| **Tutorial** | "Prerequisites", "Building your application"; complete working example; step-by-step for learning |
| **How-to** | "Procedure", "Configuration" sections; focused on a specific task; assumes knowledge |
| **Reference** | Lists of properties, configuration options, API docs; tables of settings |

### Step 3: Write JTBD-compliant titles

Apply imperative verbs based on content type:

| Content Type | JTBD Verbs | Examples |
|---|---|---|
| **Concept** | Learn, Understand, Explore | "Understand identity providers" |
| **Tutorial** | Build, Create, Protect, Get started | "Build a secure application" |
| **How-to** | Configure, Enable, Use, Set up, Deploy | "Configure logging" |
| **Reference** | (Descriptive, no verb) | "Configuration properties" |

### Title guidelines

These seven rules define a good JTBD title:

**1. Focus on the desired outcome.** Think: *What is the user trying to get done?*

**2. Avoid naming product-specific tools or features.**
- Bad: "Ansible Playbook Syntax"
- Good: "Define automation workflows"

**3. Industry-standard terminology is fine** (SSL, FTP, HTTP, OIDC, JWT).

**4. Use terms your users use** — not internal product names.
- Bad: "Custom Tekton Hub instance"
- Good: "Discover reusable tasks and pipelines for your CI/CD workflows"

**5. Use imperative verbs** that express action and intent. Never use gerunds (-ing forms).
- Bad: "Using the Analytics Dashboard"
- Good: "Track customer engagement with the analytics dashboard"

**6. Use natural, task-oriented phrasing.** Start with verbs like *Set up*, *Create*, *Configure* — they are intuitive and search-friendly.

**7. State the benefit or context when it helps distinguish similar tasks.**
- Bad: "Managing Roles and Permissions"
- Good: "Control team access with roles and permissions"

**Keep titles concise:** 3-7 words. Avoid titles that wrap to a second line in navigation.

**Reverse-engineer titles from user stories.** Take a user story — "As a project manager, I want to export task reports so I can review team progress" — and extract the title: *"Review team progress by exporting task reports."*

**Avoid anthropomorphic language** in titles and abstracts: do not use "allows you to", "helps you", "enables you to", "lets you". Use active, user-centered phrases instead ("With feature X, you can...", "Use X to...").

### Step 4: Write job statements for categories

For each logical grouping/category, write a job statement in the standard format:

> "When [situation], I want to [motivation], so I can [expected outcome]."

Keep the statement **product-agnostic**. Focus on the problem and the outcome, not the tool. Job statements are planning tools that help identify what content to create — they are not topic descriptions or short introductions.

**Examples:**

> "When I need to deploy a new application quickly and reliably, I want to automate the entire deployment process, so I can reduce manual errors and ensure consistency across environments."

> "When I set up authentication, I want to use my own identity provider to issue tokens, so that I can manage users and groups from a single location and integrate with my company's existing tools."

> "When my application slows down, I need to receive useful alerts immediately so that I can resolve performance issues quickly."

### Step 5: Present the retitled navigation

Output the proposed changes as:
1. A summary table showing: original title → new JTBD title, content type
2. The retitled navigation in the appropriate format (master .adoc, ditamap, etc.)
3. Job statements for each logical category grouping

Do **not** reorganize or restructure the navigation — only retitle. Use `/jtbdiataxify` for full restructuring.

## JTBD vs. user stories

A single JTBD contains multiple user stories. Understanding the distinction helps write better titles:

| | JTBD | User Stories |
| :--- | :--- | :--- |
| **Format** | "When [situation], I want to [motivation], so I can [outcome]." | "As a [user], I want [goal] so that [benefit]." |
| **Focus** | The *what* and *why* — desired outcome and motivation. Product-agnostic. | The *how* — a specific feature satisfying a known need. |
| **Scope** | Broad, overarching goals. | Specific, single tasks. |
| **Example** | "When creating container images, I want to easily define and run a build so that I can create consistent, secure containerized applications without being a container expert." | "As a developer, I want to use the S2I build strategy to create a container image so that I can build reproducible images from source code." |

Titles for user stories, concepts, and reference topics nested under a job can use product-specific terminology. Top-level job titles should use the user's natural language.

## Rules

- Never use gerunds (-ing forms) in titles
- Reference docs get descriptive titles (no action verb)
- Master file H1 title should match the topichead navtitle in navigation.ditamap
- Group related files with comments indicating which guide they belong to
- Keep commented-out future entries (like `<!-- <navref mapref="..."/> -->`)
- Do not modify source content files — only modify navigation/master files unless asked
- Do not reorganize or restructure navigation — only retitle

## Validation checklist

Before presenting changes, verify:
- [ ] All titles use imperative verbs (except references)
- [ ] No gerunds (-ing forms) in titles
- [ ] Content type matches verb choice
- [ ] Titles focus on user outcomes, not features
- [ ] Titles are 3-7 words and do not wrap in navigation
- [ ] No anthropomorphic language in titles or abstracts
- [ ] Job statements are product-agnostic
- [ ] Navigation structure is unchanged (titles only)
- [ ] Navigation is consistent with established patterns
