---
name: jtbdmoddocsify
description: Analyze and restructure documentation using Red Hat modular docs + JTBD. Like /modocify but adds job statements, AI retrieval analysis, and user-centered titles to the restructuring plan. Use when the user wants to split a mixed-content file into focused mod-docs modules with JTBD-aligned titles.
argument-hint: "review|restructure file-or-directory"
---

This skill has two modes based on the first argument:

- **`review`** — Audit-only mode. Classify content by mod-docs type, run all compliance checks, write JTBD job statements, recommend titles, and produce an AI retrieval analysis — all without modifying any files.
- **`restructure`** — Full mode. Audit, propose a restructuring plan with JTBD analysis, get user approval, then rewrite, verify, and commit.

If the first argument is neither `review` nor `restructure`, default to `restructure`.

Target: `$ARGUMENTS`

If no target file is given, use the currently open file from the IDE context. If neither is available, ask the user which file to check.

## Output Location

**Always** write audit/review reports to a markdown file in the **same directory** as the target file, named `moddocs-jtbd-audit-<context>.md` where `<context>` is derived from the target filename (e.g., `index.adoc` → `moddocs-jtbd-audit-index.md`). Also display the full report to the user. This applies to both `review` and `restructure` modes (the Phase 1 audit).

## Red Hat Modular Documentation Standard

Red Hat modular documentation organizes content into four module types:

| Module Type | Purpose | File Prefix | Attribute Value |
|---|---|---|---|
| **Concept** | Explains what something is, why it matters, how it works | `con_` or `con-` | `CONCEPT` |
| **Procedure** | Step-by-step instructions for accomplishing a specific task | `proc_` or `proc-` | `PROCEDURE` |
| **Reference** | Lookup information: tables, properties, API specs, options | `ref_` or `ref-` | `REFERENCE` |
| **Assembly** | Combines modules into a complete guide with context | `assembly_` or `assembly-` | `ASSEMBLY` |

Each module type serves a distinct user need. Mixing types within a single module degrades all of them. Content must be separated so each module serves one purpose clearly.

### Concept Modules

**Purpose:** Explain what something is and why it matters. Provide background, architecture, and context that help users understand before they act.

**Characteristics:**
- Explanatory prose, diagrams, and architecture overviews
- No numbered steps or procedural instructions
- Title uses JTBD verb (Understand, Learn, Explore) or a noun phrase
- Links to related procedures and references
- Can include bullet lists, tables, and diagrams when they explain concepts

**Must avoid:**
- Step-by-step instructions (move to procedures)
- Property/option lists (move to reference)
- Gerund titles ("Understanding...") — use JTBD imperative or noun phrase instead

### Procedure Modules

**Purpose:** Guide the user through a specific task with numbered steps.

**Characteristics:**
- Title uses a JTBD imperative verb (Configure, Enable, Use, Set up, Deploy, Build, Create, Install)
- Uses approved section headings only, in this order:
  1. `.Prerequisites` or `.Prerequisite`
  2. `.Procedure` (REQUIRED)
  3. `.Verification`, `.Results`, or `.Result`
  4. `.Troubleshooting`, `.Troubleshooting steps`, or `.Troubleshooting step`
  5. `.Next steps` or `.Next step`
  6. `.Additional resources`
- Multi-step procedures use numbered lists (`. Step`)
- Single-step procedures use an unnumbered bullet (`* Step`)
- Steps are imperative statements ("Create a file...", "Run the command...")
- Prerequisites are a bulleted list, not imperative statements

**Must avoid:**
- Extended explanations (link to concept modules)
- Comprehensive option tables (link to reference modules)
- Unapproved section headings
- Subheadings (`==` level) inside procedure modules except in Additional resources

### Reference Modules

**Purpose:** Authoritative lookup information — tables, property lists, API documentation, command options.

**Characteristics:**
- Structured around the product itself, not around user tasks
- Neutral, factual tone
- Consistent formatting across similar items
- Title is a descriptive noun phrase (no action verb) — reference is the one type that does not use JTBD imperatives
- Tables, labeled lists, and property descriptions

**Must avoid:**
- Procedural steps
- Extended explanations or background context
- Opinion or marketing language

### Assemblies

**Purpose:** Combine concept, procedure, and reference modules into a complete guide with shared context.

**Characteristics:**
- `:context:` variable defined after the title
- Module includes use `include::filename.adoc[leveloffset=+N]`
- Blank line follows each `include::` directive
- Context preservation for nested assemblies
- Title uses a JTBD imperative that reflects the job the assembly serves (e.g., "Secure applications with OIDC")

## Mod-Docs Compliance Checks

Run every check below during audit. For each issue found, record the line number(s), what the standard requires, and what the file actually has.

### 1. Metadata attribute (all modules)

- `:_mod-docs-content-type:` must be present with value `CONCEPT`, `PROCEDURE`, `REFERENCE`, or `ASSEMBLY`
- Must appear before the ID anchor

### 2. ID anchor (all modules)

- Format must be `[id="descriptive-name_{context}"]`
- Must include the `{context}` variable
- Must appear immediately before the H1 title
- The descriptive-name portion should match the filename (minus prefix and extension)
- Do NOT flag `[id="name"]` anchors on subsections — only check the top-level module anchor

### 3. Title format (all modules)

- Must be a single H1 heading (`= Title`)
- **Concept modules:** JTBD imperative verb (Understand, Learn, Explore) or a noun phrase — never a gerund
- **Procedure modules:** JTBD imperative verb (Configure, Enable, Use, Set up, Deploy, Build, Create, Install) — never a gerund
- **Reference modules:** descriptive noun phrase (no action verb)
- **Assembly modules:** JTBD imperative reflecting the job the assembly serves

### 4. Abstract paragraph (all modules)

- `[role="_abstract"]` must appear above the first paragraph after the title
- The paragraph should be 1-4 sentences (target 50-100 words, longer is acceptable)
- Should NOT start with "This topic...", "This section...", "This document..."
- Should avoid anthropomorphic language ("allows you to", "enables you to", "lets you", "helps you")
- Should use active, user-centered voice ("With X, you can...", "Use X to...", "To accomplish Y, ...")

### 5. File naming convention

- Filename should start with the correct prefix for its content type:
  - Concept: `con_` or `con-`
  - Procedure: `proc_` or `proc-`
  - Reference: `ref_` or `ref-`
  - Assembly: `assembly_` or `assembly-`
- Only alphanumerics, hyphens, and underscores in filename
- Extension must be `.adoc`

### 6. Heading levels (all modules)

- Only one H1 (`=`) per module — the title
- Modules (concept, procedure, reference) should NOT use level 3+ headings (`===` or deeper)
- Assemblies use `==` for section headings
- `[discrete]` is acceptable for concept/reference subheadings excluded from TOC

### 7. Procedure-specific checks

Only apply if the module is a PROCEDURE:

- Approved section headings only (see Procedure Modules section above)
- Each approved heading may appear at most once
- Sections must appear in the prescribed order
- `.Procedure` section is REQUIRED
- Multi-step: numbered lists; single-step: unnumbered bullet
- Steps should be imperative statements
- No subheadings inside procedure modules except in Additional resources

### 8. Assembly-specific checks

Only apply if the module is an ASSEMBLY:

- `:context:` variable must be defined after the title
- Module includes must use `include::filename.adoc[leveloffset=+N]`
- Blank line must follow each `include::` directive
- Context preservation for nested assemblies: `ifdef::context[:parent-context: {context}]` at top
- Context restoration at bottom

### 9. Additional resources section (all modules)

If present:

- Must have `[role="_additional-resources"]` attribute above the heading
- Must be a bulleted list
- Links should use `link:` or `xref:` macros
- No explanatory prose — links only

### 10. Images (all modules)

For every `image::` directive found:

- Must have a title (`.Title` line immediately before)
- Must have alt text in straight double quotes: `image::file.png["Alt text here."]`
- Alt text should be a complete sentence describing the essential information

## Jobs to Be Done (JTBD) Framework

JTBD ensures that each proposed module serves a real user need, not just a structural category. Every proposed file must trace to a job statement, and every title must be framed around the user's goal.

A *job* is not a feature or a task — it is the core problem a user is solving or the goal they are pursuing. Jobs are product-agnostic and timeless: technology changes, but the underlying need does not.

### Job statement format

> "When [situation], I want to [motivation], so I can [expected outcome]."

Guidelines:
- **Product-agnostic.** Focus on the problem and the outcome, not the tool.
- **Use terms users actually search for** — not internal product names or feature labels.
- Identify **top-level jobs** (broad desired outcomes) and **sub-jobs** (steps toward the outcome). A single top-level job contains multiple sub-jobs; each sub-job may map to a separate module.
- Job statements are for planning — they are not topic titles or short descriptions.

**Examples:**
- "When I need to deploy a new application quickly and reliably, I want to automate the entire deployment process, so I can reduce manual errors and ensure consistency across environments."
- "When my application slows down, I need to receive useful alerts immediately so that I can resolve performance issues quickly."
- "When I set up authentication, I want to use my own identity provider to issue tokens, so that I can manage users and groups from a single location."

### Jobs vs. user stories

A single job contains multiple user stories. Understanding this distinction prevents splitting at the wrong granularity.

| | JTBD | User Stories |
|---|---|---|
| **Format** | "When [situation], I want to [motivation], so I can [outcome]." | "As a [user], I want [goal] so that [benefit]." |
| **Focus** | The *what* and *why* — desired outcome and motivation. Product-agnostic. | The *how* — a specific feature satisfying a known need. |
| **Scope** | Broad, overarching goals. | Specific, single tasks. |

### JTBD title verb mapping for mod-docs types

JTBD imperative titles replace the traditional mod-docs gerund convention. All titles — module H1s, navigation titles, and xref link text — use the same JTBD imperative form. This eliminates the friction of maintaining two title styles and ensures consistency from source file to published navigation.

| Module Type | JTBD Verbs | Examples |
|---|---|---|
| **Concept** | Understand, Learn, Explore | "Understand Basic authentication", "Learn Quarkus Security architecture" |
| **Procedure** | Configure, Enable, Use, Set up, Deploy, Build, Create | "Configure data sources", "Deploy to OpenShift" |
| **Reference** | _(Descriptive noun phrase, no verb)_ | "OIDC configuration properties", "CLI command reference" |
| **Assembly** | Job-level imperative reflecting the user's goal | "Secure applications with OIDC", "Build container images" |

### Title rules

These rules apply to **all titles** — module H1s, navigation titles, xref link text, and assembly headings. There is no separate gerund convention; JTBD imperatives are the standard.

1. **Focus on the desired outcome.** Think: *What is the user trying to get done?*
2. **Avoid product-specific tools or features in job-level titles.** Job-level titles (assembly headings, navigation groups) use natural language describing the goal. Product-specific terms are expected in individual module titles.
   - Job-level: "Secure your applications" (not "Quarkus Security")
   - Module-level: "Configure quarkus-oidc for multi-tenancy" (product term is necessary context)
3. **Use terms your users use** — not internal product names.
4. **Imperative verbs only** — never gerunds (-ing forms) in any title
5. **3-7 words** — concise enough to avoid line-wrap in navigation
6. **State the benefit or context** when it helps distinguish similar tasks.
7. **No anthropomorphic language** — never "allows you to", "helps you", "enables you to", "lets you"
8. **Reverse-engineer from user stories.** Take a user story and extract the title.

**Industry-standard terms** (SSL, HTTP, OIDC, JWT, REST, gRPC) are acceptable and encouraged for searchability.

## Steps

### Phase 1: Audit

1. Read the target documentation thoroughly.
2. **Detect the content type** using these signals (in priority order):
   a. `:_mod-docs-content-type:` attribute
   b. File naming prefix (`con_`, `proc_`, `ref_`, `assembly_`)
   c. Content analysis — if neither attribute nor prefix exists, infer from structure
3. Run all mod-docs compliance checks (see Mod-Docs Compliance Checks section).
4. For each section or content block, classify it by mod-docs type. Flag blocks that belong to a different type than the file's declared type.
5. **Build a content manifest** by counting structural elements programmatically (use `grep` to count rather than counting by eye):
   - Number of `[source,...]` code blocks (and their line ranges)
   - Number of admonition blocks (`[IMPORTANT]`, `[NOTE]`, `[TIP]`, `[WARNING]`, `[CAUTION]`)
   - All section headings (`==` and `===`)
   - All explicit section IDs (`[id="..."]` and `[[...]]`) that other files may deep-link to
   - **Incoming xrefs**: Grep all `.adoc` files in the project for `xref:original-filename.adoc` references (including fragment links). List each file and line number.
   - Include these counts in the audit report as a "Content Manifest" section
6. Present the audit as a structured report (see Review Mode section for format).

### Phase 2: Restructuring Plan with JTBD Analysis

7. **Early exit: compliant single-type files.** If the Phase 1 audit finds the file already passes all mod-docs checks and contains a single content type, skip the split. Write job statements, apply JTBD titles, and update navigation. Proceed directly to Phase 7.

8. **Write job statements.** Write JTBD job statements that capture the user needs this content serves. Present them in a table:

   | Job statement | Scope | Proposed module |
   |---|---|---|
   | "When ..., I want to ..., so I can ..." | Top-level | `proposed-filename.adoc` |
   | "When ..., I want to ..., so I can ..." | Sub-job | `proposed-filename.adoc` |

   Every proposed module must trace to at least one job statement. If a section doesn't map to any job, flag it as orphaned content.

9. **Propose the restructured organization.** For each proposed module:
   - State its mod-docs type (CONCEPT, PROCEDURE, REFERENCE)
   - State its JTBD title (imperative verb for concepts/procedures, descriptive noun phrase for references)
   - State the proposed filename (using mod-docs prefix convention)
   - Describe its scope and audience
   - List which existing sections/content moves into it
   - Identify gaps where new content is needed

10. **Propose the assembly.** If the restructuring produces 2+ modules on the same topic, propose an assembly that ties them together:
    - Assembly filename: `assembly_<topic>.adoc`
    - Assembly title (JTBD imperative reflecting the job)
    - Include order: concept first, then procedures, then reference
    - `:context:` variable value

11. **AI retrieval analysis.** Show how the proposed split improves discoverability:

    | Query a developer might ask | Current file retrieves? | Proposed module |
    |---|---|---|
    | "getting started with X" | Weak — generic title | **Build your first X** — direct match |
    | "X configuration options" | No — buried in §7 | **X configuration properties** — title match |

12. **Content journey validation.** For each top-level job, trace the user's path:
    - What module types are needed at each step? (concept for orientation, procedure for execution, reference for lookup)
    - Does the proposed split provide all needed types, or are there gaps?
    - Are there duplicate paths where two proposed modules cover the same ground?
    - Does the ordering make sense for a user working through the job sequentially?

13. **Check for existing files.** Before finalizing filenames, glob and grep to verify no proposed filename collides with an existing file.

14. **Present the complete plan** (compliance fixes + job statements + proposed modules + assembly + AI retrieval analysis + content journey findings) to the user and **get approval before rewriting**.

### Phase 3: Rewrite

15. Once approved, rewrite or restructure the documentation:
    - Separate mixed content into distinct modules by type
    - Apply mod-docs structural requirements:
      - Add `:_mod-docs-content-type:` attribute
      - Add `[id="descriptive-name_{context}"]` anchor
      - Add `[role="_abstract"]` on the first paragraph
      - Use approved section headings for procedures
      - Apply correct heading levels
    - Use mod-docs file naming convention (`con_`, `proc_`, `ref_`, `assembly_`)
    - Add cross-references between related modules
    - Create assembly wrapper if proposed in step 10
    - Preserve all substantive information; do not silently drop content
16. For each rewritten module, verify it passes all mod-docs compliance checks.

### Phase 4: Update Navigation

17. Read the project's navigation file (e.g., `navigation.ditamap`, master `.adoc`, `nav.adoc`).
18. If the original file appears in the navigation, replace the single entry with a JTBD-structured group:

    ```
    Category (top level — organizational only, no content)
      └─ Job [Level 1] — the top-level job title (assembly)
           └─ Job or user story [Level 2] — individual modules
                └─ User story [Level 3] — sub-modules if needed
    ```

    Keep nesting to **three levels maximum** (categories don't count). Apply these conventions:
    - Concept modules: "Understand X", "Learn about Y"
    - Procedure modules: "Configure X", "Deploy Y"
    - Reference modules: "X configuration properties" (descriptive, no verb)
    - Assembly entries: job-level imperative title
19. Present the nav changes to the user for review.

### Phase 5: Update Incoming Cross-References

20. Using the incoming xref list from the content manifest (Phase 1 step 5), update every file that references the original filename:
    - For each `xref:original-file.adoc[...]`, determine which new module contains the referenced content and update the xref
    - For fragment links (`xref:original-file.adoc#section-id[...]`), find which new module contains the anchor and update accordingly
    - Preserve all explicit section IDs from the original file in the appropriate new modules
21. Present the xref changes to the user for review.

### Phase 6: Content Verification

22. **Before removing the original file**, launch a verification agent to compare the original against all new modules. The agent must perform all four checks:

    **Check 1 — Code block content comparison:**
    For each `[source,...]` block in the original, find the corresponding block in the new files and do a line-by-line diff. Report each as IDENTICAL, MODIFIED, or MISSING. If the new total code content lines are lower, flag it.

    **Check 2 — Callout annotation verification:**
    For each code block with callout markers, verify markers, list items, and explanatory text are preserved.

    **Check 3 — Prose passage diff:**
    Extract all non-structural prose from the original. For each passage, search all new files and report: FOUND VERBATIM, FOUND PARAPHRASED, or MISSING. Any MISSING passage is content loss that must be fixed.

    **Check 4 — Structural element verification:**
    Confirm every section heading appears (possibly reworded) in one of the new files. Verify admonition block counts match. Verify all explicit section IDs are preserved.

23. **Review findings.** Classify each difference as:
    - **Content loss** — Must be fixed before proceeding.
    - **Intentional editorial change** — Report to user for confirmation.

24. Fix any content losses. Present intentional editorial changes for review. Explicitly ask the user whether to delete the original file.

### Phase 7: Build Verification

25. **Check build dependencies** before running the build.
26. Run the project's documentation build command to verify no broken references.
27. If the build fails, diagnose and fix broken xrefs, missing includes, or syntax errors.
28. Report the build result to the user.

### Phase 8: Commit Restructuring

29. **Verify branch.** If on `main` or a protected branch, create a feature branch named `jtbdmoddocsify-<topic>` and switch to it.
30. Ensure the project's `.gitignore` contains the pattern `moddocs-jtbd-audit-*.md`. If not, add it. Then stage all new files, updated navigation, updated xrefs, the deleted original file, and the `.gitignore` change (if any). Do **not** stage the audit report. Commit with a descriptive message: `Restructure <original-filename> into mod-docs modules with JTBD titles`.

### Phase 9: Copyedit

31. Read each new file and check for:
    - Anthropomorphic language — replace with active, user-centered phrasing
    - Sections that jump into a code block without an introductory sentence
    - Missing or incorrect punctuation
    - Voice and tone consistency for the module's type
    - Mod-docs abstract paragraph quality (no meta-references, user-centered voice)
32. Re-run the documentation build to verify copyedits did not break anything.
33. If copyedits were made, commit separately: `Copyedit <topic> mod-docs modules`.

### Phase 10: Create Pull Request

34. Push the feature branch with `git push -u origin HEAD`.

35. Create the PR using `gh pr create`. Use the title pattern `Restructure <original-filename> into mod-docs modules` and generate the body from this template:

```markdown
This restructuring applies [Red Hat modular documentation](https://redhat-documentation.github.io/modular-docs/) and [Jobs to Be Done](https://jobs-to-be-done.com/jobs-to-be-done-a-framework-for-customer-needs-c883cbf61c90) to `<ORIGINAL-FILENAME>`, splitting a single mixed-content file into focused modules that each serve one user need. Each module traces to a JTBD job statement and follows mod-docs conventions.

### What this PR does

Splits the single <LINE-COUNT>-line `<ORIGINAL-FILENAME>` into <N> focused modules:

| File | Title | Module Type | User job served |
|------|-------|-------------|-----------------|
| `<FILENAME>` | <TITLE> | <TYPE> | <JOB-SUMMARY> |
<!-- repeat for each new module -->

### AI retrieval improvement

| Query | Before (single page) | After (focused modules) |
|-------|---------------------|------------------------|
| "<QUERY>" | <BEFORE> | **<PROPOSED-MODULE>** — direct match |
<!-- repeat for key queries -->

**Navigation** — Modules are grouped under an assembly with JTBD titles, so the reader sees an organized, goal-oriented structure.

**Cross-references** — Updated xrefs in <LIST-OF-FILES> to point to the appropriate new modules.

**Content preservation** — Verified via 4-check content comparison. Zero content losses.

### Commits

1. **<COMMIT-1-MESSAGE>** — <BRIEF-DESCRIPTION>
2. **<COMMIT-2-MESSAGE>** — <BRIEF-DESCRIPTION>
<!-- include all commits -->
```

## Review Mode (audit only)

When the mode is `review`, perform Phase 1 (Audit) and the JTBD analysis portions of Phase 2 (steps 8 and 11), then produce a feedback report. Do NOT rewrite or modify any files.

The report must be GitHub-flavored markdown with the following structure:

1. **Overall Assessment** — 2-3 sentence summary of the file's mod-docs compliance and content type alignment.
2. **Detected Type** — State the detected type and basis (attribute / filename prefix / content analysis). Flag conflicting signals.
3. **Mod-Docs Compliance Issues** — Grouped by severity:
   - **Must fix** — Missing content-type attribute, missing ID anchor, missing `{context}`, missing abstract role, gerund title (should be JTBD imperative)
   - **Should fix** — Wrong filename prefix, heading level issues, unapproved procedure headings, missing image alt text, anthropomorphic language
   - **Consider** — Minor style preferences or optional improvements
   For each issue: line number, what the standard requires, what the file has, concrete fix.
4. **Content Type Analysis** — Sections that belong to a different type than the file's declared type, with specific line ranges and classification.
5. **Job Statements** — JTBD job statements for the content, in a table with columns: Job statement, Scope (top-level or sub-job), Related content.
6. **Title Recommendations** — For each file or major section, recommend a JTBD title. Present as a table: Current title, Recommended JTBD title, Module type, Rationale.
7. **AI Retrieval Analysis** — The before/after query table showing how focused modules with JTBD titles would improve discoverability.
8. **Content Manifest** — Structural element counts: code blocks, admonition blocks, section headings, explicit section IDs, incoming xrefs.
9. **Summary** — Count: `N must-fix, N should-fix, N consider — from N total checks`. One sentence on overall quality.

**Tone:** Supportive and constructive, but direct. Lead with strengths. Frame issues as "opportunities" not "problems." Use "should" for clear standards requirements — being supportive does not mean being noncommittal.

**Factual rigor:** Verify that positive claims are not contradicted by the issues section. Verify line numbers and file names are accurate. Do not overstate improvements.

**Output:** See the Output Location section above for file naming and placement.

## Quality Checklist

Before finalizing, verify each module against these criteria:

- [ ] **Single type**: Each module is one mod-docs type (CONCEPT, PROCEDURE, REFERENCE)
- [ ] **No type mixing**: Concepts don't contain steps, procedures don't contain option tables, references don't explain
- [ ] **Mod-docs metadata**: `:_mod-docs-content-type:` attribute present and correct
- [ ] **ID anchor**: `[id="name_{context}"]` present with `{context}` variable
- [ ] **Abstract paragraph**: `[role="_abstract"]` present, user-centered voice, no meta-references
- [ ] **JTBD title**: Imperative verb for concepts/procedures, noun phrase for references — no gerunds
- [ ] **File naming**: Correct prefix (`con_`, `proc_`, `ref_`, `assembly_`)
- [ ] **Heading levels**: Single H1, no level 3+ in modules
- [ ] **Procedure structure**: Approved headings only, correct order, `.Procedure` required
- [ ] **Cross-references**: Each module links to related modules where appropriate
- [ ] **Job traceability**: Every proposed module traces to at least one JTBD job statement
- [ ] **Assembly**: If 2+ modules, assembly wrapper exists with context variable
- [ ] **Content journey validated**: User's path through the job is complete
- [ ] **No filename collisions**: All proposed filenames verified against existing files
- [ ] **No content lost**: All substantive information preserved

## Writing Style Rules

Follow the writing style guide at `~/.claude/style-guide.md`. That file is the single source of truth for voice, word choices, and punctuation rules.
