# JTBDize: Apply Jobs to Be Done framework to documentation

Apply the Jobs to Be Done (JTBD) framework to the specified documentation content. This skill analyzes existing documentation structure and rewrites titles, navigation, and organization to be user-centered and action-oriented.

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

**Title guidelines:**
- Use imperative verbs (not gerunds like "Configuring...")
- Focus on user outcomes, not features
- Keep titles concise: 3-7 words
- Avoid anthropomorphic language ("allows you to", "helps you", "enables you to")
- Use active, user-centered phrases ("With feature X, you can...", "Use X to...")

### Step 4: Write job statements for categories

For each logical grouping/category, write a job statement:

> "When [situation], I want to [motivation], so I can [expected outcome]."

Example:
> "When I need to deploy a new application quickly, I want to automate the deployment process, so I can reduce manual errors."

### Step 5: Organize into categories

Group related topics into categories with JTBD-compliant category titles. Order categories by user priority:

1. Concepts/Overview (understand what it is)
2. Getting started (first experience)
3. Core features (primary tasks)
4. Advanced features (power-user tasks)
5. Integration (connecting with other systems)
6. Reference (lookup information)

### Step 6: Present the restructured navigation

Output the proposed changes as:
1. A summary table showing: original title -> new JTBD title, content type, category
2. The restructured navigation in the appropriate format (master .adoc, ditamap, etc.)
3. Any identified content gaps (jobs without existing documentation)

## Rules

- Never use gerunds (-ing forms) in titles
- Reference docs get descriptive titles (no action verb)
- Master file H1 title should match the topichead navtitle in navigation.ditamap
- Group related files with comments indicating which guide they belong to
- Keep commented-out future entries (like `<!-- <navref mapref="..."/> -->`)
- Do not modify source content files - only modify navigation/master files unless asked

## Validation checklist

Before presenting changes, verify:
- [ ] All titles use imperative verbs (except references)
- [ ] No gerunds (-ing forms) in titles
- [ ] Content type matches verb choice
- [ ] Titles focus on user outcomes, not features
- [ ] Categories ordered by user priority
- [ ] Navigation is consistent with established patterns
- [ ] No anthropomorphic language in titles or abstracts
