---
name: modocify
description: Check an AsciiDoc file against the Red Hat modular documentation standard and flag the most important issues. Use when the user wants to audit a document for mod-docs compliance, or restructure a document to follow modular docs conventions.
argument-hint: [review|restructure] [file-path]
allowed-tools: Read, Grep, Glob, Write, Edit
---

This skill has two modes based on the first argument:

- **`review`** — Audit-only mode. Read the file, detect the content type, run all checks, and produce a prioritized issue report. Do NOT rewrite or modify any files.
- **`restructure`** — Full mode. Audit, propose a restructuring plan to bring the file into mod-docs compliance, get user approval, then rewrite.

If the first argument is neither `review` nor `restructure`, default to `review`.

Target: `$ARGUMENTS`

If no target file is given, use the currently open file from the IDE context. If neither is available, ask the user which file to check.

## Output Location

**Always** write audit/review reports to a markdown file in the **same directory** as the target file, named `modocs-audit-<context>.md` where `<context>` is derived from the target filename (e.g., `index.adoc` → `modocs-audit-index.md`). Also display the full report to the user. This applies to both `review` and `restructure` modes (the audit phase).

## Content Type Detection

Determine the module type using these signals (in priority order):

1. **`:_mod-docs-content-type:` attribute** — look for `CONCEPT`, `PROCEDURE`, `REFERENCE`, or `ASSEMBLY`
2. **File naming prefix** — `con_` / `con-`, `proc_` / `proc-`, `ref_` / `ref-`, `assembly_` / `assembly-`
3. **Content analysis** — if neither attribute nor prefix exists, infer from structure:
   - Procedure indicators: numbered steps, `.Procedure` heading, `.Prerequisites`
   - Concept indicators: explanatory prose, no steps, "Overview" sections
   - Reference indicators: tables, property lists, labeled lists
   - Assembly indicators: `include::` directives, `:context:` variable

State the detected type at the top of the report. If signals conflict (e.g., prefix says concept but content is procedural), flag that as an issue.

## Checks to Perform

Run every check below. For each issue found, record the line number(s), what the standard requires, and what the file actually has.

### 1. Metadata attribute (all modules)

- `:_mod-docs-content-type:` must be present with value `CONCEPT`, `PROCEDURE`, `REFERENCE`, or `ASSEMBLY`
- Must appear before the ID anchor

### 2. ID anchor (all modules)

- Format must be `[id="descriptive-name_{context}"]`
- Must include the `{context}` variable
- Must appear immediately before the H1 title
- The descriptive-name portion should match the filename (minus prefix and extension)
- Do NOT flag `[id="name"]` anchors that appear on subsections — only check the top-level module anchor

### 3. Title format (all modules)

- Must be a single H1 heading (`= Title`)
- **Concept / Reference modules:** title should be a noun phrase (not a gerund)
- **Procedure modules:** title should start with a gerund (-ing verb: Creating, Configuring, Installing, etc.)
- **Assembly modules:** gerund if it contains procedure includes; noun phrase if concept/reference only

### 4. Abstract paragraph (all modules)

- `[role="_abstract"]` must appear above the first paragraph after the title
- The paragraph should be 1-4 sentences (target 50-100 words, longer is acceptable)
- Should NOT start with "This topic...", "This section...", "This document...", or similar meta-references
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
- Assemblies use `==` for section headings (Prerequisites, Next steps, Additional resources)
- `[discrete]` is acceptable for concept/reference subheadings excluded from TOC

### 7. Procedure-specific checks

Only apply these if the module is a PROCEDURE:

- **Approved section headings only.** The following are the ONLY permitted `.Heading` labels:
  - `.Prerequisites` or `.Prerequisite`
  - `.Procedure`
  - `.Verification`, `.Results`, or `.Result`
  - `.Troubleshooting`, `.Troubleshooting steps`, or `.Troubleshooting step`
  - `.Next steps` or `.Next step`
  - `.Additional resources`
- Each approved heading may appear at most once
- Sections must appear in this order: Prerequisites, Procedure, Verification, Troubleshooting, Next steps, Additional resources
- `.Procedure` section is REQUIRED
- Multi-step procedures use numbered lists (`. Step`)
- Single-step procedures use an unnumbered bullet (`* Step`)
- Steps should be imperative statements ("Create a file...", "Run the command...")
- Prerequisites should be a bulleted list, not imperative statements
- No subheadings (no `==` level headings) inside procedure modules except in Additional resources

### 8. Assembly-specific checks

Only apply these if the module is an ASSEMBLY:

- `:context:` variable must be defined after the title
- Module includes must use `include::filename.adoc[leveloffset=+N]`
- Blank line must follow each `include::` directive
- Context preservation for nested assemblies: `ifdef::context[:parent-context: {context}]` at top
- Context restoration at bottom: `ifdef::parent-context[:context: {parent-context}]` and `ifndef::parent-context[:!context:]`

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

## Review Mode (audit only)

When the mode is `review`, run all checks above and produce the report. Do NOT modify any files.

### Report Format

Output the report as GitHub-flavored markdown with this structure:

**Header:**

```
## Mod-docs audit: `filename.adoc`

**Detected type:** CONCEPT | PROCEDURE | REFERENCE | ASSEMBLY
**Basis:** (attribute / filename prefix / content analysis)
```

**Issues (grouped by severity):**

- **Must fix** — The file will not process correctly in Pantheon/DITA, or violates a hard structural requirement (missing content-type attribute, missing ID anchor, missing `{context}`, missing abstract role, wrong title form for content type)
- **Should fix** — Deviates from the standard in a way that degrades quality or reusability (wrong filename prefix, heading level violations, unapproved procedure headings, missing image alt text, anthropomorphic language in abstract)
- **Consider** — Minor style preferences or optional improvements (abstract word count, discrete heading suggestions)

For each issue:

```
N. **[Line NN]** Brief description
   - **Standard:** What the mod-docs manual requires
   - **Found:** What the file actually has
   - **Fix:** Concrete suggestion
```

**Summary:** A one-line count: `N must-fix, N should-fix, N consider — from N total checks`

**Output:** See the Output Location section above for file naming and placement.

## Restructure Mode

When the mode is `restructure`:

### Phase 1: Audit

Run all checks and produce the review report (same as review mode).

### Phase 2: Restructuring Plan

Based on the audit, propose a plan to bring the file into full mod-docs compliance:

1. For each must-fix and should-fix issue, describe the concrete change
2. If the file needs to be split (e.g., mixed content types), propose the new file structure:
   - State each new file's name (following mod-docs naming: `con_`, `proc_`, `ref_`, `assembly_`)
   - State its content type
   - List which content from the original moves into it
3. If the file needs an assembly wrapper, describe the assembly structure
4. Present the plan to the user and get approval before making changes

### Phase 3: Rewrite

Once approved:

1. Apply all structural fixes (metadata, ID anchor, abstract role, heading levels, etc.)
2. Split files if the plan calls for it
3. Create assembly wrapper if needed
4. Ensure all new/modified files pass the full check suite
5. Preserve all substantive content — do not silently drop information

## Guidelines

- Be precise about line numbers. Read the file carefully.
- If the file passes all checks, say so clearly — don't invent issues.
- Focus on the most impactful issues first. A file missing its content-type attribute matters more than a slightly long abstract.
- When in doubt about content type, explain the conflicting signals and let the user decide.
- For files that are clearly NOT modular docs (e.g., upstream Quarkus community docs in `asciidoc/`), note that they follow a different convention and the mod-docs checks may not all apply. Still run the checks but contextualize the results.
