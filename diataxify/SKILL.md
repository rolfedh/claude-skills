---
name: diataxify
description: Analyze and restructure documentation according to the Diataxis framework. Use when the user wants to classify, audit, restructure, or improve documentation by applying Diataxis principles (tutorials, how-to guides, reference, explanation).
argument-hint: "review|restructure file-or-directory"
---

This skill has two modes based on the first argument:

- **`review`** — Audit-only mode. Read the documentation, classify each file by Diataxis type, and produce a supportive feedback report in GitHub-flavored markdown. Do NOT rewrite or restructure any files. Focus on what the docs do well and where small improvements could strengthen Diataxis alignment. Output a summary scorecard and numbered opportunities.
- **`restructure`** — Full mode. Audit, propose a restructuring plan, get user approval, then rewrite.

If the first argument is neither `review` nor `restructure`, default to `restructure`.

Target: `$ARGUMENTS`

## Output Location

**Always** write audit/review reports to a markdown file in the **same directory** as the target file, named `diataxis-audit-<context>.md` where `<context>` is derived from the target filename (e.g., `index.adoc` → `diataxis-audit-index.md`). Also display the full report to the user. This applies to both `review` and `restructure` modes (the Phase 1 audit).

## The Diataxis Framework

Diataxis organizes documentation into four distinct types along two axes:

|  | **Acquisition** (learning) | **Application** (working) |
|---|---|---|
| **Action** (practical) | Tutorial | How-to Guide |
| **Cognition** (theoretical) | Explanation | Reference |

Each type serves a fundamentally different user need. Mixing types within a single document degrades all of them. Content must be separated so each piece serves one purpose clearly.

### 1. Tutorials (Learning-oriented)

**Purpose:** A guided learning experience where a tutor leads a beginner through meaningful exercises to acquire skills and knowledge.

**Characteristics:**
- Learning-oriented, not task-oriented; what the learner *does* is a vehicle for what they *learn*
- The teacher bears nearly all responsibility for learner success
- Produces early, visible results at every step so learners see cause-and-effect
- Uses second person ("you") and direct imperatives ("First, do x. Now, do y")
- Includes observational prompts ("Notice that..." / "You will see that...")
- Provides actual expected outputs so learners confirm they are on track
- Follows a single, focused narrative path toward a complete experience
- Must work reliably every time; learners lose confidence when steps fail

**Must avoid:**
- Extended explanations (link to Explanation docs instead)
- Abstractions, generalizations, and alternatives
- Offering choices or optional paths
- Teaching concepts before the learner has concrete experience

### 2. How-to Guides (Task-oriented)

**Purpose:** Directions that guide an already-competent user through solving a real-world problem or achieving a specific goal.

**Characteristics:**
- Goal-oriented: framed around what the *user* needs to accomplish, not around tools or features
- Assumes existing competence; does not teach fundamentals
- Steps follow a logical sequence based on dependencies and natural thinking flow
- Titles state exactly what the guide helps achieve (e.g., "How to configure TLS for production")
- Addresses real-world complexity: forking paths, multiple entry points, user judgment
- Starts and ends at meaningful, practical places
- Links to Reference for comprehensive option lists rather than inlining them

**Must avoid:**
- Teaching or explaining concepts (link to Tutorials or Explanation instead)
- Excessive digression or context-setting
- Enumerating every option when only relevant ones matter
- Conflating with tutorials (how-to guides assume competence; tutorials build it)

### 3. Reference (Information-oriented)

**Purpose:** Authoritative, neutral technical descriptions of the machinery and how to operate it. Serves users who need accurate information while actively working.

**Characteristics:**
- Structured around the product itself, not around user tasks
- Austere, factual, and neutral: describes without explaining, instructing, or opining
- Consistent formatting and patterns so users can predict where to find information
- Mirrors the product's own logical structure in its organization
- Includes illustrative examples that clarify without becoming instructional
- Provides "truth and certainty" as firm platforms for practitioners to stand on

**Distinguishing illustrative examples from procedural creep:**
Code snippets in reference docs are legitimate when they show configuration or API surface *in context* — e.g., a properties block with callout annotations explaining what each property does, or a short class demonstrating an annotation's effect. This is not procedural creep. Flag code as procedural only when it forms a sequence of steps the user is meant to follow in order to achieve a goal. A useful test: if you removed the code, would the remaining prose still read as a self-contained reference entry? If yes, the code is illustrative. If the prose only makes sense as instructions around the code, it's a how-to guide.

**Must avoid:**
- Explanation, instruction, opinion, or marketing language
- Inconsistent formatting across similar items
- Assuming auto-generated docs are sufficient without editorial discipline
- Mixing in how-to steps or tutorial content

### 4. Explanation (Understanding-oriented)

**Purpose:** Discursive treatment of a subject that permits reflection, deepens comprehension, and provides context, background, and the "why" behind decisions.

**Characteristics:**
- Broader scope and higher altitude than the other three types
- Answers implicit "why" questions and "Can you tell me about...?" inquiries
- Makes connections across topics to reveal the bigger picture
- Provides design rationale, historical context, technical constraints, and trade-offs
- Considers alternatives, counter-examples, and multiple approaches
- Readable away from the product; does not require hands-on context
- Titles use "About..." or topic-focused framing

**Must avoid:**
- Absorbing instructional content (keep separate from tutorials and how-to guides)
- Absorbing reference content (keep separate from technical descriptions)
- Becoming unbounded; scope each piece around a specific "why" question

## Steps

### Phase 1: Audit

1. Read the target documentation thoroughly.
2. For each section, paragraph, or distinct content block, classify it into one of the four Diataxis types (Tutorial, How-to Guide, Reference, Explanation) or flag it as mixed/unclear.
3. Use the Diataxis compass to classify: Ask two questions about each block:
   - **Action or Cognition?** Does this guide practical steps, or convey theoretical knowledge?
   - **Acquisition or Application?** Does this serve someone learning, or someone applying existing skill?
4. **Build a content manifest** by counting structural elements in the original file programmatically (use `grep` to count rather than counting by eye, which is error-prone on large files):
   - Number of `[source,...]` code blocks (and their line ranges)
   - Number of admonition blocks (`[IMPORTANT]`, `[NOTE]`, `[TIP]`, `[WARNING]`, `[CAUTION]`)
   - All section headings (`==` and `===`)
   - All explicit section IDs (`[id="..."]`) that other files may deep-link to
   - **Incoming xrefs**: Grep all `.adoc` files in the project for `xref:original-filename.adoc` references (including fragment links like `xref:original-filename.adoc#section[...]`). List each file and line number.
   - Include these counts in the audit report as a "Content Manifest" section
5. Present the audit as a structured report:
   - Current structure overview
   - Classification of each major section/block
   - Content manifest (element counts from step 4)
   - Identified problems: mixed types, misclassified content, missing types, boundary violations
   - Specific passages that bleed across type boundaries

### Phase 2: Restructuring Plan

6. Propose a restructured organization that cleanly separates content into the four types.
7. For each proposed document or section:
   - State its Diataxis type
   - Describe its scope and audience
   - List which existing content moves into it
   - Identify gaps where new content is needed
8. Present the plan to the user and get approval before rewriting.

### Phase 3: Rewrite

9. Once approved, rewrite or restructure the documentation:
   - Separate mixed content into distinct documents/sections by type
   - Apply the voice, tone, and structural conventions for each type (see characteristics above)
   - Insert a `:diataxis-type:` attribute into each file's header with the correct value (`tutorial`, `howto`, `reference`, or `concept`). See the File Naming and Metadata Conventions section for placement details.
   - Add cross-references between related documents of different types (e.g., a tutorial linking to the relevant reference and explanation)
   - Preserve all substantive information; do not silently drop content
10. For each rewritten piece, verify it passes the Diataxis compass test (both axes point to the intended quadrant).

### Phase 3.5: Update Navigation

11. Read the project's navigation file (e.g., `nav.adoc` for Antora sites, `navigation.ditamap` for DITA sites, or equivalent).
12. If the original file appears in the navigation:
    - Replace the single entry with entries for each new file, using appropriate nesting
    - Use descriptive link text that reflects each file's Diataxis type (e.g., concept pages get topic-focused titles, how-to pages get action-oriented titles, reference pages get neutral descriptive titles)
    - Maintain the same position in the navigation hierarchy as the original
13. If the original file does NOT appear in the navigation, add the new files in a logical location near related content.
14. Present the nav changes to the user for review.

### Phase 3.7: Update Incoming Cross-References

15. Using the incoming xref list from the content manifest (Phase 1 step 4), update every file that references the original filename:
    - For each `xref:original-file.adoc[...]`, determine which new file contains the referenced content and update the xref to point there (e.g., `xref:original-file-concept.adoc[...]`)
    - For fragment links (`xref:original-file.adoc#section-id[...]`), find which new file contains the `[id="section-id"]` anchor and update accordingly
    - Preserve all explicit `[id="..."]` anchors from the original file in the appropriate new files so that any remaining deep links resolve correctly
16. Present the xref changes to the user for review.

### Phase 4: Content Verification

17. **Before removing the original file**, launch a verification agent to compare the original against all new files. The agent must perform all four checks below and report findings for each:

    **Check 1 — Code block content comparison:**
    For each `[source,...]` block in the original (identified by line range), find the corresponding block in the new files and do a line-by-line diff. Report each block as IDENTICAL, MODIFIED (list every changed/removed line), or MISSING. Compute total code content lines (lines between `----` delimiters, not counting the delimiters) for the original vs all new files combined. If the new total is lower, flag it — something may have been dropped. A higher count is acceptable (e.g., splitting one block into two adds scaffolding).

    **Check 2 — Callout annotation verification:**
    For each code block that contains callout markers (`// <1>`, `// <2>`, etc.), verify: (a) every marker inside the code is preserved in the corresponding new file's block, (b) every callout list item (lines starting with `<1>`, `<2>`, etc.) after the closing `----` is preserved, (c) the callout explanatory text matches or any changes are flagged. Report any missing, renumbered, or rewritten callouts with exact text comparison.

    **Check 3 — Prose passage diff:**
    Extract all non-structural prose from the original — connecting sentences between sections and code blocks, content inside admonition blocks, bullet lists, and links. Exclude headings, code block contents/delimiters, source annotations, and callout list items. For each prose passage, search all new files and report: FOUND VERBATIM, FOUND PARAPHRASED (show both versions), or MISSING. Any MISSING passage is a content loss that must be fixed.

    **Check 4 — Structural element verification:**
    Confirm every section heading (`==` and `===`) from the original appears (possibly reworded) in one of the new files. Verify the total count of admonition blocks across all new files matches the original. Verify all explicit `[id="..."]` anchors from the original are preserved in the new files.

18. **Review findings.** Classify each difference as:
    - **Content loss** — lines, passages, or callouts dropped with no counterpart anywhere. Must be fixed before proceeding.
    - **Intentional editorial change** — deliberate rewording, restructuring, or splitting (e.g., removing step comments when a combined code block is split into separate blocks with prose headings). Report to user for confirmation but do not treat as a defect.

19. Fix any content losses. Present intentional editorial changes to the user for review. Only after all losses are fixed and editorial changes are acknowledged may the original file be removed. Explicitly ask the user whether to delete the original file; do not delete it silently or leave its disposition ambiguous.

### Phase 5: Build Verification

20. **Check build dependencies** before running the build. For Antora sites, verify `node_modules/` exists and run `npm install` if needed (use `--ignore-scripts` to skip problematic postinstall steps like phantomjs). For Maven-based docs, verify the Maven wrapper is available.
21. Run the project's documentation build command to verify no broken references:
    - For Antora sites: `npx antora antora-playbook.yml` (or the project's equivalent)
    - For Maven-based docs: `./mvnw -DquicklyDocs` or similar
    - For other build systems: identify and run the appropriate build command
22. If the build fails, diagnose and fix broken xrefs, missing includes, or syntax errors introduced during restructuring.
23. Report the build result to the user.

### Phase 5.5: Commit Restructuring

24. Ensure the project's `.gitignore` contains the pattern `diataxis-audit-*.md`. If not, add it. Then stage all new files, updated navigation, updated xrefs, the deleted original file, and the `.gitignore` change (if any). Do **not** stage the audit report — it is a working artifact, not a deliverable. Commit with a descriptive message following this pattern: `Restructure <original-filename> into <N> Diataxis-aligned files` (e.g., `Restructure tool-guardrails.adoc into four Diataxis-aligned files`). This commit captures the restructuring as a single, verified unit of work — both content-verified (Phase 4) and build-verified (Phase 5).

### Phase 5.7: Copyedit

25. Read each new file and check for:
    - Anthropomorphic language ("This guide provides...", "This feature allows you to...", "This enables...") — replace with active, user-centered phrasing per the Writing Style Rules section
    - Sections that jump directly into a code block without an introductory sentence — add a brief context-setting sentence
    - Missing or incorrect punctuation (especially commas in nonrestrictive clauses)
    - Voice and tone consistency for the file's Diataxis type: tutorials use direct "you" imperatives with observational prompts; how-to guides use concise task-oriented imperatives; reference is neutral and austere; concept is discursive and reflective
    - Any remaining instances of "violations" (should be "issues"), "abort"/"aborting" (never use), or passive constructions that could be active
26. Re-run the documentation build to verify copyedits did not introduce broken references or syntax errors. If the build fails, fix the issues before committing.
27. If copyedits were made, commit them separately with message pattern: `Copyedit <topic> docs` (e.g., `Copyedit tool guardrails docs`). If no copyedits were needed, skip this commit.

### Phase 6: Create Pull Request

28. Create a feature branch (if not already on one) named `diataxify-<topic>` and push it to the remote with `git push -u origin HEAD`.

29. Create the PR using `gh pr create`. Use the title pattern `Restructure <original-filename> into Diataxis-aligned files` and generate the body from the template below, populating the placeholders from the actual restructuring results.

**PR body template:**

```markdown
This restructuring applies the [Diataxis](https://diataxis.fr/) content architecture to `<ORIGINAL-FILENAME>`, splitting a single mixed-content file into focused pages that each serve one user need.

### Why Diataxis?

Diataxis organizes documentation along two axes — *action vs. cognition* and *learning vs. working* — producing four content types that each serve a distinct user need:

| | **Learning** | **Working** |
|---|---|---|
| **Practical** | Tutorial | How-to guide |
| **Theoretical** | Explanation | Reference |

**For users:** When content types are mixed in a single page, every need is served poorly: a digression into explanation interrupts a how-to guide, tutorial hand-holding frustrates a practitioner looking for a quick recipe, and reference details buried in prose are hard to scan. Separating them keeps each page focused on one user mode, so readers find the right content at the right moment. As the [Diataxis docs](https://diataxis.fr/quality/) put it, separating content also makes quality gaps "apparent that were obscured before" — missing information becomes visible when each type stands on its own.

**For documentation systems:** Separating content types into distinct files produces the file-level modularity that documentation toolchains depend on. Single-purpose files can be composed into different navigation structures for different audiences, included or excluded by product variant without surgical editing, and translated as coherent units. When a how-to guide changes, the stable concept explanation that used to live on the same page no longer needs retranslation.

### What this PR does

Splits the single <LINE-COUNT>-line `<ORIGINAL-FILENAME>` into <N> focused files with user-oriented titles, so readers can jump directly to the page that matches their intent:

| File | Title | What readers get |
|------|-------|------------------|
| `<FILENAME>` | <TITLE> | <BRIEF-DESCRIPTION> |
<!-- repeat for each new file -->

**Navigation** — The files are grouped under a single "<TOPIC>" heading in the navigation, so from the reader's perspective the structure is *more* organized, not more sprawling.

**Cross-references** — Updated xrefs in <LIST-OF-FILES> to point to the appropriate new files. Each new file cross-references the others in a "Related Resources" section.

**Content preservation** — Verified via a 4-check content comparison (code block line-by-line diff, callout annotation verification, prose passage diff, structural element verification). Zero content losses; all differences are intentional editorial changes from the split.

### Commits

1. **<COMMIT-1-MESSAGE>** — <BRIEF-DESCRIPTION>
2. **<COMMIT-2-MESSAGE>** — <BRIEF-DESCRIPTION>
<!-- include all commits on the branch -->
```

### Review Mode (audit only)

When the mode is `review`, perform only Phase 1 (Audit), then produce a feedback report. Do NOT proceed to Phase 2 or 3. The report must be GitHub-flavored markdown with the following structure:

1. **Overall Assessment** — 2-3 sentence summary of the documentation's Diataxis alignment.
2. **What the Docs Do Well** — Numbered list of strengths, with specific file names and line references. For each strength, cite the Diataxis principle it satisfies.
3. **Opportunities to Strengthen Diataxis Alignment** — Numbered list of improvements. For each opportunity:
   - Name the file and line range
   - Identify the Diataxis boundary issue (e.g., "how-to guide contains explanation," "concept doc has procedural steps")
   - Make a concrete, opinionated recommendation. Use "should" (not "could" or "consider") when the change directly improves Diataxis alignment. Reserve hedging language for genuinely optional or judgment-call items where multiple valid approaches exist.
4. **Summary Scorecard** — Table with columns: Quadrant, Files, Compliance (Strong/Very strong/Mixed), Notes.
5. **Closing** — One sentence affirming the overall quality.

**Tone:** Supportive and constructive, but direct. Lead with strengths. Frame issues as "opportunities" not "problems." Acknowledge that the work under review is an improvement. When a recommendation follows directly from Diataxis principles, state it clearly with "should" — being supportive does not mean being noncommittal. Hedging ("could," "consider," "might want to") undermines the value of the audit by leaving the author unsure whether the recommendation matters.

**Factual rigor:** Before finalizing the report, critically review every claim. Verify that positive claims are not contradicted by your own opportunities section. Verify that line numbers and file names are accurate. Do not overstate improvements (e.g., don't say duplication is "eliminated" if it's only reduced). When flagging code in reference docs as "procedural creep," apply the illustrative-vs-procedural test from the Reference section above — short code snippets with callout annotations that document configuration or API surface in context are standard reference practice, not procedural content. Only flag code as misplaced when it forms a step-by-step procedure aimed at achieving a user goal.

**Output:** See the Output Location section above for file naming and placement.

## Quality Checklist

Before finalizing, verify each document against these criteria:

- [ ] **Single type**: Each document serves exactly one Diataxis purpose
- [ ] **No boundary bleeding**: Tutorials don't explain, how-to guides don't teach, reference doesn't instruct, explanation doesn't include steps
- [ ] **Appropriate voice**: Tutorials and how-to guides use "you" and direct imperatives (never "we"); reference is neutral and austere; explanation is discursive and reflective
- [ ] **Cross-references**: Each document links to related documents of other types where appropriate
- [ ] **Completeness**: All four types are represented where the subject matter warrants it
- [ ] **User-centered framing**: How-to guides are named for user goals, not product features
- [ ] **No content lost**: All substantive information from the original is preserved somewhere in the restructured output
- [ ] **`:diataxis-type:` attribute**: Every file has a `:diataxis-type:` attribute in its header with the correct value (`tutorial`, `howto`, `reference`, or `concept`)

## File Naming and Metadata Conventions

Diataxis does not prescribe file naming or metadata conventions, but both practices make the documentation structure explicit and maintainable.

### File naming pattern: `<extension>-<diataxis-type>.adoc`

Use the extension (or topic) name as the filename **prefix** and the Diataxis type as the **suffix**. This groups all documentation for a given extension together in directory listings while making the content type immediately visible.

**Pattern:** `<extension>[-<descriptor>]-<type>.adoc`

- `<extension>` — the extension or topic name (e.g., `aesh`, `grpc`, `security-oidc`)
- `<descriptor>` — optional extra words when multiple files share the same extension and type (e.g., `aesh-remote-access-howto.adoc` vs `aesh-howto.adoc`)
- `<type>` — the Diataxis type suffix

| Suffix | Diataxis Type | `:diataxis-type:` value | Example |
|--------|---------------|-------------------------|---------|
| `-tutorial` | Tutorial | `tutorial` | `aesh-tutorial.adoc` |
| `-howto` | How-to Guide | `howto` | `aesh-howto.adoc` |
| `-reference` | Reference | `reference` | `aesh-reference.adoc` |
| `-concept` | Explanation | `concept` | `aesh-concept.adoc` |

**Multiple files of the same type:** When an extension has more than one file of a given Diataxis type, add descriptive words between the extension prefix and the type suffix:

| File | Description |
|------|------------|
| `security-oidc-howto.adoc` | Main OIDC how-to |
| `security-oidc-auth0-tutorial.adoc` | Auth0-specific OIDC tutorial |
| `grpc-client-howto.adoc` | gRPC client how-to |
| `grpc-server-howto.adoc` | gRPC server how-to |

### `:diataxis-type:` attribute

The main Quarkus documentation uses a `:diataxis-type:` AsciiDoc attribute in each file's header to declare its content type. Add this attribute to each file. The attribute goes in the document header after the `include` for shared attributes:

```asciidoc
= Page Title
include::_attributes.adoc[]
:diataxis-type: howto
```

Valid values: `tutorial`, `howto`, `reference`, `concept`.

### When to apply these conventions

During the restructuring plan (Phase 2), always apply the `<extension>-<type>.adoc` naming pattern and the `:diataxis-type:` attribute. If the original file does not follow this pattern, include a rename in the plan.

## Writing Style Rules

Follow the writing style guide at `~/.claude/style-guide.md`. That file is the single source of truth for voice, word choices, and punctuation rules.
