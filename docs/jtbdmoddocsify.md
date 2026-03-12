---
layout: default
title: /jtbdmoddocsify
nav_order: 4
---

# /jtbdmoddocsify

Analyze and restructure documentation using [Red Hat modular documentation](https://redhat-documentation.github.io/modular-docs/) combined with [Jobs to Be Done](https://jobs-to-be-done.com/jobs-to-be-done-a-framework-for-customer-needs-c883cbf61c90). Splits mixed-content files into focused mod-docs modules (concept, procedure, reference) with JTBD-aligned titles.

## When to use this skill

Use `/jtbdmoddocsify` when:

- A document mixes conceptual, procedural, and reference content that should be separated
- You follow Red Hat modular documentation conventions (downstream product docs, Pantheon publishing)
- You want mod-docs compliance checks combined with JTBD job statements and AI retrieval analysis

Do **not** use this skill when:

- You only need to retitle navigation — use [/jtbdtitlesonly](jtbdtitlesonly) instead
- You use the Diataxis framework instead of mod-docs — use [/jtbdiataxify](jtbdiataxify) instead
- You want a mod-docs compliance check without JTBD analysis — use `/modocify` instead

## Invocation

```
/jtbdmoddocsify review path/to/file.adoc
/jtbdmoddocsify restructure path/to/file.adoc
```

**Modes:**
- **`review`** — Audit only. Runs all mod-docs compliance checks, classifies content by module type, writes JTBD job statements, recommends titles, and produces an AI retrieval analysis. Does not modify any files.
- **`restructure`** — Full workflow. Audits, proposes a plan, gets your approval, then rewrites, verifies content preservation, and commits.

If neither mode is specified, defaults to `restructure`.

## Mod-docs module types

Red Hat modular documentation organizes content into four types:

| Module type | Purpose | File prefix | JTBD verbs |
|---|---|---|---|
| **Concept** | Explains what something is, why it matters | `con_` or `con-` | Understand, Learn, Explore |
| **Procedure** | Step-by-step task instructions | `proc_` or `proc-` | Configure, Enable, Deploy, Build, Create, Install |
| **Reference** | Lookup information: tables, properties, options | `ref_` or `ref-` | _(Descriptive noun phrase)_ |
| **Assembly** | Combines modules into a complete guide | `assembly_` or `assembly-` | Job-level imperative |

### JTBD replaces gerunds

Traditional mod-docs uses gerund titles for procedures ("Configuring data sources"). This skill replaces gerunds with JTBD imperatives everywhere — module H1 titles, navigation titles, and xref link text all use the same imperative form.

| Traditional mod-docs | JTBD mod-docs |
|---|---|
| Configuring data sources | Configure data sources |
| Installing the extension | Install the extension |
| Basic authentication | Understand Basic authentication |

## Workflow

### Review mode

The `review` mode produces a structured audit report with:

1. **Overall assessment** — Summary of mod-docs compliance and content type alignment
2. **Detected type** — How the content type was determined (attribute, filename prefix, or content analysis)
3. **Compliance issues** — Grouped by severity (must fix, should fix, consider)
4. **Content type analysis** — Sections that belong to a different type than the file's declared type
5. **Job statements** — JTBD job statements for the content
6. **Title recommendations** — Current vs. recommended JTBD titles
7. **AI retrieval analysis** — How focused modules with JTBD titles improve search discoverability
8. **Content manifest** — Counts of code blocks, admonitions, headings, section IDs, and incoming xrefs
9. **Summary** — Issue counts and overall quality assessment

The report is saved to a markdown file alongside the target file.

### Restructure mode

The full workflow proceeds through 10 phases:

| Phase | What happens |
|---|---|
| **1. Audit** | Classify content, run all 10 mod-docs compliance checks, build a content manifest |
| **2. Plan** | Write job statements, propose modules and assembly, run AI retrieval analysis, validate content journeys |
| **3. Rewrite** | Split content into separate modules by type with full mod-docs metadata |
| **4. Navigation** | Update nav files with JTBD-structured hierarchy |
| **5. Cross-references** | Update all incoming xrefs to point to the correct new modules |
| **6. Verification** | 4-check content comparison (code blocks, callouts, prose, structural elements) |
| **7. Build** | Run the documentation build to catch broken references |
| **8. Commit** | Commit on a feature branch |
| **9. Copyedit** | Fix anthropomorphic language, voice, and punctuation |
| **10. Pull request** | Push and create a PR with a structured description |

You approve the plan before any files are modified.

## Mod-docs compliance checks

The skill runs 10 compliance checks during audit:

| Check | What it verifies |
|---|---|
| 1. Metadata attribute | `:_mod-docs-content-type:` present with correct value |
| 2. ID anchor | `[id="name_{context}"]` format with `{context}` variable |
| 3. Title format | JTBD imperative (concepts/procedures) or noun phrase (references) — no gerunds |
| 4. Abstract paragraph | `[role="_abstract"]` present, user-centered voice, no meta-references |
| 5. File naming | Correct prefix (`con_`, `proc_`, `ref_`, `assembly_`) |
| 6. Heading levels | Single H1, no level 3+ in modules |
| 7. Procedure structure | Approved headings only, correct order, `.Procedure` required |
| 8. Assembly structure | `:context:` variable, `include::` directives, context preservation |
| 9. Additional resources | `[role="_additional-resources"]` attribute, bulleted links only |
| 10. Images | Title, alt text in straight double quotes |

Issues are categorized as:

- **Must fix** — Missing content-type attribute, missing ID anchor, missing `{context}`, missing abstract role, gerund title
- **Should fix** — Wrong filename prefix, heading level issues, unapproved procedure headings, missing image alt text, anthropomorphic language
- **Consider** — Minor style preferences or optional improvements

## Content verification

Before deleting the original file, a verification agent runs four checks:

1. **Code block comparison** — Line-by-line diff of every `[source,...]` block
2. **Callout verification** — Markers, list items, and explanatory text preserved
3. **Prose passage diff** — Every non-structural passage found verbatim, paraphrased, or flagged as missing
4. **Structural element verification** — Headings, admonition counts, and section IDs match

Any content loss must be fixed before proceeding. You are asked explicitly whether to delete the original file.

## Example

**Before:** A single 400-line `security-basic-auth.adoc` mixing concepts, steps, and configuration reference.

**After:**

| File | Title | Module type |
|---|---|---|
| `con_basic-authentication.adoc` | Understand Basic authentication | Concept |
| `proc_enable-basic-authentication.adoc` | Enable Basic authentication | Procedure |
| `ref_basic-authentication-properties.adoc` | Basic authentication configuration properties | Reference |
| `assembly_basic-authentication.adoc` | Authenticate with Basic authentication | Assembly |

## Comparison with /jtbdiataxify

| Feature | /jtbdmoddocsify | /jtbdiataxify |
|---|---|---|
| Framework | Red Hat modular docs | Diataxis |
| Module types | Concept, Procedure, Reference, Assembly | Tutorial, How-to, Reference, Explanation |
| File naming | `con_`, `proc_`, `ref_`, `assembly_` prefixes | `<topic>-<type>` suffix |
| Metadata | `:_mod-docs-content-type:` | `:diataxis-type:` |
| Required structure | ID anchors, context variables, approved headings, abstract paragraphs | Minimal structural requirements |
| Best for | Red Hat downstream product docs | Upstream open-source docs, Antora sites |