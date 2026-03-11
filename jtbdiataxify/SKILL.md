---
name: jtbdiataxify
description: Analyze and restructure documentation using Diataxis + JTBD. Like /diataxify but adds job statements, AI retrieval analysis, and user-centered titles to the restructuring plan. Use when the user wants to split a mixed-content file into focused Diataxis pages with JTBD-aligned titles.
argument-hint: "review|restructure file-or-directory"
---

This skill has two modes based on the first argument:

- **`review`** — Audit-only mode. Classify content by Diataxis type, write JTBD job statements, recommend titles, and produce an AI retrieval analysis — all without modifying any files.
- **`restructure`** — Full mode. Audit, propose a restructuring plan with JTBD analysis, get user approval, then rewrite, verify, and commit.

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

**How a tutorial differs from a how-to procedure:**
Both contain step-by-step instructions, but a tutorial adds pedagogical scaffolding that a procedure omits:

- **Explanation at each step** — A procedure says "Run `mvn quarkus:create`." A tutorial adds *why*: "This creates the project scaffold because Quarkus uses build-time augmentation..."
- **Result verification** — A procedure may optionally say "Verify the service is running." A tutorial *always* confirms: "You should see this output, which means..."
- **Error anticipation** — A procedure mentions errors only if likely. A tutorial anticipates them: "If you see X, that means Y."
- **Progressive building** — Procedure steps are independent; skip to what you need. Tutorial steps build on each other; skipping breaks the learning path.
- **No assumed context** — A procedure assumes the user has a real project and existing knowledge. A tutorial sets up a safe sandbox and assumes nothing.

In short: a tutorial is a procedure *plus* the why, the what-to-expect, and the what-you-just-learned. A how-to strips all of that away because the reader doesn't need it.

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
- Titles state exactly what the guide helps achieve (e.g., "Configure TLS for production")
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

## Jobs to Be Done (JTBD) Framework

JTBD is used in this skill to ensure that each proposed page serves a real user need, not just a structural category. Every proposed file must trace to a job statement, and every title must be framed around the user's goal.

A *job* is not a feature or a task — it is the core problem a user is solving or the goal they are pursuing. Jobs are product-agnostic and timeless: technology changes, but the underlying need does not.

### Job statement format

> "When [situation], I want to [motivation], so I can [expected outcome]."

Guidelines:
- **Product-agnostic.** Focus on the problem and the outcome, not the tool. A good job statement works regardless of which product solves the problem.
- **Use terms users actually search for** — not internal product names or feature labels.
- Identify **top-level jobs** (broad desired outcomes) and **sub-jobs** (steps toward the outcome). A single top-level job contains multiple sub-jobs; each sub-job may map to a separate page.
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

For example, the job *"improve application performance"* might contain user stories for configuring autoscaling, setting resource limits, and analyzing metrics. Those user stories become the individual pages nested under the job in navigation.

**When decomposing the original file into pages:** each proposed page should map to a sub-job or user story, not to a top-level job (which is too broad for a single page) unless the file is small enough to remain a single focused tutorial or how-to.

### JTBD title verb mapping

| Content Type | Verbs | Examples |
|---|---|---|
| **Concept** | Learn, Understand, Explore | "Understand identity providers", "Explore the build lifecycle" |
| **Tutorial** | Build, Create, Protect, Get started | "Build a secure application", "Create your first endpoint" |
| **How-to** | Configure, Enable, Use, Set up, Deploy | "Configure data sources", "Deploy to OpenShift" |
| **Reference** | _(Descriptive, no verb)_ | "OIDC configuration properties", "CLI command reference" |

### Title rules

1. **Focus on the desired outcome.** Think: *What is the user trying to get done?*
2. **Avoid product-specific tools or features in job-level titles** (parent topic headings, navigation group labels). These titles should use natural language that describes the goal. Product-specific terms (extension names, API names, CLI commands) are expected and correct in individual page titles where they are the topic being documented.
   - Job-level: "Secure your applications" (not "Quarkus Security")
   - Page-level: "Configure quarkus-oidc for multi-tenancy" (product term is necessary context)
3. **Use terms your users use** — not internal product names.
   - No: "Custom Tekton Hub instance" — Yes: "Discover reusable tasks and pipelines"
4. **Imperative verbs only** — never gerunds (-ing forms like "Configuring...", "Managing...")
5. **3-7 words** — concise enough to avoid line-wrap in navigation
6. **State the benefit or context** when it helps distinguish similar tasks.
   - "Control access with RBAC" vs. "Control access with LDAP"
7. **No anthropomorphic language** — never "allows you to", "helps you", "enables you to", "lets you"
8. **Reverse-engineer from user stories.** Take a user story — "As a developer, I want to export logs so I can debug production issues" — and extract the title: "Debug production issues with exported logs."

**Industry-standard terms** (SSL, HTTP, OIDC, JWT, REST, gRPC) are acceptable and encouraged for searchability.

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
   - All explicit section IDs (`[id="..."]` and `[[...]]`) that other files may deep-link to
   - **Incoming xrefs**: Grep all `.adoc` files in the project for `xref:original-filename.adoc` references (including fragment links like `xref:original-filename.adoc#section[...]`). List each file and line number.
   - Include these counts in the audit report as a "Content Manifest" section
5. Present the audit as a structured report:
   - Current structure overview
   - Classification of each major section/block
   - Content manifest (element counts from step 4)
   - Identified problems: mixed types, misclassified content, missing types, boundary violations
   - Specific passages that bleed across type boundaries

### Phase 2: Restructuring Plan with JTBD Analysis

6. **Early exit: single-type files.** If the Phase 1 audit finds the file is already a single Diataxis type with no boundary issues, skip the split. Write job statements (see step 7 format, using "Related section" instead of "Proposed page" since there is only one file), apply JTBD title and filename conventions (rename if needed), update navigation, and if the file was renamed, update incoming cross-references from other files (grep for `xref:original-filename.adoc`). Proceed directly to Phase 7 (Build Verification). Present the proposed title, filename, job statements, and any nav/xref changes to the user for approval before making changes.

7. **Write job statements.** Write JTBD job statements that capture the user needs this content serves. Present them in a table:

   | Job statement | Scope | Proposed page |
   |---|---|---|
   | "When ..., I want to ..., so I can ..." | Top-level | `proposed-filename.adoc` |
   | "When ..., I want to ..., so I can ..." | Sub-job | `proposed-filename.adoc` |

   Every proposed page must trace to at least one job statement. If a section of the original file doesn't map to any job, flag it as orphaned content that belongs elsewhere.

8. **Propose the restructured organization.** For each proposed document:
   - State its Diataxis type
   - State its JTBD-aligned title (using the verb mapping above)
   - State the proposed filename (using the `<topic>-<type>.adoc` naming convention)
   - Describe its scope and audience
   - List which existing sections/content moves into it
   - Identify gaps where new content is needed

9. **AI retrieval analysis.** Show how the proposed split improves discoverability by listing common queries users might ask and which proposed page would match:

   | Query a developer might ask | Current file retrieves? | Proposed page |
   |---|---|---|
   | "getting started with X" | Weak — generic title | **Build your first X** — direct match |
   | "X live reload" | No — buried in §7 | **Understand X dev mode** — title match |

   This table validates that the split produces strong retrieval signals rather than one weak one.

10. **Parent topic (when splitting produces 3+ pages on the same topic).** If the restructured output has three or more files that belong to a single topic, propose a parent topic — a concept page that serves as the entry point for users pursuing that job. A parent topic includes:
    - **A natural-language title** reflecting the user's goal, not product terminology
    - **An outcome description** (2-4 sentences): what the user is trying to achieve and why it matters
    - **A product-to-goal mapping**: how the product's capabilities help achieve the goal (bridges user perspective to product features)
    - **High-level steps**: ordered list of major steps, each linking to the child topics that provide detail
    - **Related content**: links to related jobs, external resources, or deeper reference

    If the split produces only 2 files, skip the parent topic — a cross-reference section in each file is sufficient.

11. **Check for existing files.** Before finalizing filenames, glob and grep the project to verify no proposed filename collides with an existing file. If a collision is found, adjust the proposed filename and note why.

12. **Content journey validation.** For each top-level job, trace the user's path from start to finish:
    - What content types are needed at each step? (concept for orientation, tutorial for learning, how-to for execution, reference for lookup)
    - Does the proposed split provide all needed types, or are there gaps?
    - Are there duplicate paths where two proposed pages cover the same ground?
    - Does the ordering make sense for a user working through the job sequentially?

    This often reveals sub-jobs missed in the initial analysis, gaps where content is needed but doesn't exist in the original, and duplication where two sections cover the same task from different angles. Update the plan if the content journey reveals issues.

13. **Present the complete plan** (job statements + proposed pages + parent topic if applicable + AI retrieval analysis + content journey findings) to the user and **get approval before rewriting**.

### Phase 3: Rewrite

14. Once approved, rewrite or restructure the documentation:
    - Separate mixed content into distinct documents/sections by type
    - Apply the voice, tone, and structural conventions for each type (see characteristics above)
    - Insert a `:diataxis-type:` attribute into each file's header with the correct value (`tutorial`, `howto`, `reference`, or `concept`). See the File Naming and Metadata Conventions section for placement details.
    - Add cross-references between related documents of different types (e.g., a tutorial linking to the relevant reference and explanation)
    - Preserve all substantive information; do not silently drop content
    - If a parent topic was proposed in step 10, write it following the parent topic pattern (title, outcome description, product-to-goal mapping, high-level steps, related content)
15. For each rewritten piece, verify it passes the Diataxis compass test (both axes point to the intended quadrant).

### Phase 4: Update Navigation

16. Read the project's navigation file (e.g., `nav.adoc` for Antora sites, `navigation.ditamap` for DITA sites, or equivalent).
17. If the original file appears in the navigation, replace the single entry with a JTBD-structured group following this hierarchy pattern:

    ```
    Category (top level — organizational only, no content)
      └─ Job [Level 1] — the top-level job title (parent topic if one was created)
           └─ Job or user story [Level 2] — individual pages
                └─ User story [Level 3] — sub-pages if needed
    ```

    Keep nesting to **three levels maximum** (categories don't count as a level). Apply these conventions:
    - Concept pages get topic-focused titles ("Understand X", "Learn about Y")
    - How-to pages get action-oriented titles ("Configure X", "Deploy Y")
    - Tutorial pages get outcome-oriented titles ("Build your first X")
    - Reference pages get neutral descriptive titles ("X configuration properties")
    - Maintain the same position in the navigation hierarchy as the original
18. If the original file does NOT appear in the navigation, add the new files in a logical location near related content.
19. Present the nav changes to the user for review.

### Phase 5: Update Incoming Cross-References

20. Using the incoming xref list from the content manifest (Phase 1 step 4), update every file that references the original filename:
    - For each `xref:original-file.adoc[...]`, determine which new file contains the referenced content and update the xref to point there (e.g., `xref:original-file-concept.adoc[...]`)
    - For fragment links (`xref:original-file.adoc#section-id[...]`), find which new file contains the `[id="section-id"]` anchor and update accordingly
    - Preserve all explicit section IDs (`[id="..."]` and `[[...]]`) from the original file in the appropriate new files so that any remaining deep links resolve correctly
21. Present the xref changes to the user for review.

### Phase 6: Content Verification

22. **Before removing the original file**, launch a verification agent to compare the original against all new files. The agent must perform all four checks below and report findings for each:

    **Check 1 — Code block content comparison:**
    For each `[source,...]` block in the original (identified by line range), find the corresponding block in the new files and do a line-by-line diff. Report each block as IDENTICAL, MODIFIED (list every changed/removed line), or MISSING. Compute total code content lines (lines between `----` delimiters, not counting the delimiters) for the original vs all new files combined. If the new total is lower, flag it — something may have been dropped. A higher count is acceptable (e.g., splitting one block into two adds scaffolding).

    **Check 2 — Callout annotation verification:**
    For each code block that contains callout markers (`// <1>`, `// <2>`, etc.), verify: (a) every marker inside the code is preserved in the corresponding new file's block, (b) every callout list item (lines starting with `<1>`, `<2>`, etc.) after the closing `----` is preserved, (c) the callout explanatory text matches or any changes are flagged. Report any missing, renumbered, or rewritten callouts with exact text comparison.

    **Check 3 — Prose passage diff:**
    Extract all non-structural prose from the original — connecting sentences between sections and code blocks, content inside admonition blocks, bullet lists, and links. Exclude headings, code block contents/delimiters, source annotations, and callout list items. For each prose passage, search all new files and report: FOUND VERBATIM, FOUND PARAPHRASED (show both versions), or MISSING. Any MISSING passage is a content loss that must be fixed.

    **Check 4 — Structural element verification:**
    Confirm every section heading (`==` and `===`) from the original appears (possibly reworded) in one of the new files. Verify the total count of admonition blocks across all new files matches the original. Verify all explicit section IDs (`[id="..."]` and `[[...]]`) from the original are preserved in the new files.

23. **Review findings.** Classify each difference as:
    - **Content loss** — lines, passages, or callouts dropped with no counterpart anywhere. Must be fixed before proceeding.
    - **Intentional editorial change** — deliberate rewording, restructuring, or splitting (e.g., removing step comments when a combined code block is split into separate blocks with prose headings). Report to user for confirmation but do not treat as a defect.

24. Fix any content losses. Present intentional editorial changes to the user for review. Only after all losses are fixed and editorial changes are acknowledged may the original file be removed. Explicitly ask the user whether to delete the original file; do not delete it silently or leave its disposition ambiguous.

### Phase 7: Build Verification

25. **Check build dependencies** before running the build. For Antora sites, verify `node_modules/` exists and run `npm install` if needed (use `--ignore-scripts` to skip problematic postinstall steps like phantomjs). For Maven-based docs, verify the Maven wrapper is available.
26. Run the project's documentation build command to verify no broken references:
    - For Antora sites: `npx antora antora-playbook.yml` (or the project's equivalent)
    - For Maven-based docs: `./mvnw -DquicklyDocs` or similar
    - For other build systems: identify and run the appropriate build command
27. If the build fails, diagnose and fix broken xrefs, missing includes, or syntax errors introduced during restructuring.
28. Report the build result to the user.

### Phase 8: Commit Restructuring

29. **Verify branch.** Check the current branch. If on `main` or a protected branch, create a feature branch named `jtbdiataxify-<topic>` and switch to it before committing. If already on a feature branch, continue.
30. Ensure the project's `.gitignore` contains the pattern `diataxis-audit-*.md`. If not, add it. Then stage all new files, updated navigation, updated xrefs, the deleted original file, and the `.gitignore` change (if any). Do **not** stage the audit report — it is a working artifact, not a deliverable. Commit with a descriptive message following this pattern: `Restructure <original-filename> into <N> Diataxis-aligned files` (e.g., `Restructure tool-guardrails.adoc into four Diataxis-aligned files`). This commit captures the restructuring as a single, verified unit of work — both content-verified (Phase 6) and build-verified (Phase 7).

### Phase 9: Copyedit

31. Read each new file and check for:
    - Anthropomorphic language ("This guide provides...", "This feature allows you to...", "This enables...") — replace with active, user-centered phrasing per the Writing Style Rules section
    - Sections that jump directly into a code block without an introductory sentence — add a brief context-setting sentence
    - Missing or incorrect punctuation (especially commas in nonrestrictive clauses)
    - Voice and tone consistency for the file's Diataxis type: tutorials use direct "you" imperatives with observational prompts; how-to guides use concise task-oriented imperatives; reference is neutral and austere; concept is discursive and reflective
    - Any remaining instances of "violations" (should be "issues"), "abort"/"aborting" (never use), or passive constructions that could be active
32. Re-run the documentation build to verify copyedits did not introduce broken references or syntax errors. If the build fails, fix the issues before committing.
33. If copyedits were made, commit them separately with message pattern: `Copyedit <topic> docs` (e.g., `Copyedit tool guardrails docs`). If no copyedits were needed, skip this commit.

### Phase 10: Create Pull Request

34. Push the feature branch to the remote with `git push -u origin HEAD`.

35. Create the PR using `gh pr create`. Use the title pattern `Restructure <original-filename> into Diataxis-aligned files` and generate the body from the template below, populating the placeholders from the actual restructuring results.

**PR body template:**

```markdown
This restructuring applies [Diataxis](https://diataxis.fr/) content architecture and [Jobs to Be Done](https://jobs-to-be-done.com/jobs-to-be-done-a-framework-for-customer-needs-c883cbf61c90) to `<ORIGINAL-FILENAME>`, splitting a single mixed-content file into focused pages that each serve one user need. Each file traces to a JTBD job statement and is typed to a single Diataxis quadrant.

### What this PR does

Splits the single <LINE-COUNT>-line `<ORIGINAL-FILENAME>` into <N> focused files:

| File | Title | Diataxis type | User job served |
|------|-------|---------------|-----------------|
| `<FILENAME>` | <TITLE> | <TYPE> | <JOB-SUMMARY> |
<!-- repeat for each new file -->

### AI retrieval improvement

| Query | Before (single page) | After (focused pages) |
|-------|---------------------|----------------------|
| "<QUERY>" | <BEFORE> | **<PROPOSED-PAGE>** — direct match |
<!-- repeat for key queries -->

**Navigation** — The files are grouped under a single "<TOPIC>" heading in the navigation, so from the reader's perspective the structure is *more* organized, not more sprawling.

**Cross-references** — Updated xrefs in <LIST-OF-FILES> to point to the appropriate new files. Each new file cross-references the others in a "Related Resources" section.

**Content preservation** — Verified via a 4-check content comparison (code block line-by-line diff, callout annotation verification, prose passage diff, structural element verification). Zero content losses; all differences are intentional editorial changes from the split.

### Commits

1. **<COMMIT-1-MESSAGE>** — <BRIEF-DESCRIPTION>
2. **<COMMIT-2-MESSAGE>** — <BRIEF-DESCRIPTION>
<!-- include all commits on the branch -->
```

### Review Mode (audit only)

When the mode is `review`, perform Phase 1 (Audit) and the JTBD analysis portions of Phase 2 (steps 7 and 9), then produce a feedback report. Do NOT rewrite or restructure any files. The report must be GitHub-flavored markdown with the following structure:

1. **Overall Assessment** — 2-3 sentence summary of the documentation's Diataxis alignment.
2. **What the Docs Do Well** — Numbered list of strengths, with specific file names and line references. For each strength, cite the Diataxis principle it satisfies.
3. **Opportunities to Strengthen Diataxis Alignment** — Numbered list of improvements. For each opportunity:
   - Name the file and line range
   - Identify the Diataxis boundary issue (e.g., "how-to guide contains explanation," "concept doc has procedural steps")
   - Make a concrete, opinionated recommendation. Use "should" (not "could" or "consider") when the change directly improves Diataxis alignment. Reserve hedging language for genuinely optional or judgment-call items where multiple valid approaches exist.
4. **Job Statements** — JTBD job statements for the content, following the format in the JTBD Framework section. Present in a table with columns: Job statement, Scope (top-level or sub-job), Related content (which sections serve this job).
5. **Title Recommendations** — For each file or major section, recommend a JTBD-aligned title using the verb mapping. Present as a table: Current title, Recommended title, Content type, Rationale.
6. **AI Retrieval Analysis** — The before/after query table from step 9, showing how the current structure performs vs. how focused pages with JTBD titles would perform.
7. **Content Manifest** — The structural element counts from step 4: code blocks, admonition blocks, section headings, explicit section IDs, and incoming xrefs.
8. **Summary Scorecard** — Table with columns: Quadrant, Files, Compliance (Strong/Very strong/Mixed), Notes.
9. **Closing** — One sentence affirming the overall quality.

**Tone:** Supportive and constructive, but direct. Lead with strengths. Frame issues as "opportunities" not "problems." Acknowledge that the work under review is an improvement. When a recommendation follows directly from Diataxis principles, state it clearly with "should" — being supportive does not mean being noncommittal. Hedging ("could," "consider," "might want to") undermines the value of the audit by leaving the author unsure whether the recommendation matters.

**Factual rigor:** Before finalizing the report, critically review every claim. Verify that positive claims are not contradicted by your own opportunities section. Verify that line numbers and file names are accurate. Do not overstate improvements (e.g., don't say duplication is "eliminated" if it's only reduced). When flagging code in reference docs as "procedural creep," apply the illustrative-vs-procedural test from the Reference section above — short code snippets with callout annotations that document configuration or API surface in context are standard reference practice, not procedural content. Only flag code as misplaced when it forms a step-by-step procedure aimed at achieving a user goal.

**Output:** See the Output Location section above for file naming and placement.

## Quality Checklist

Before finalizing, verify each document against these criteria:

- [ ] **Single type**: Each document serves exactly one Diataxis purpose
- [ ] **No boundary bleeding**: Tutorials don't explain, how-to guides don't teach, reference doesn't instruct, explanation doesn't include steps
- [ ] **Appropriate voice**: Tutorials and how-to guides use "you" and direct imperatives (never "we"); reference is neutral and austere; explanation is discursive and reflective
- [ ] **Cross-references**: Each document links to related documents of other types where appropriate
- [ ] **Completeness**: All four Diataxis types are represented where the subject matter warrants it; gaps are noted in the content journey findings but not filled with invented content
- [ ] **User-centered framing**: How-to guides are named for user goals, not product features
- [ ] **No content lost**: All substantive information from the original is preserved somewhere in the restructured output
- [ ] **`:diataxis-type:` attribute**: Every file has a `:diataxis-type:` attribute in its header with the correct value (`tutorial`, `howto`, `reference`, or `concept`)
- [ ] **Job traceability**: Every proposed page traces to at least one JTBD job statement
- [ ] **Parent topic**: If 3+ pages were produced for the same topic, a parent topic entry point exists
- [ ] **Content journey validated**: The user's path through the top-level job is complete — no missing content types at any step
- [ ] **No filename collisions**: All proposed filenames verified against existing files in the project

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
