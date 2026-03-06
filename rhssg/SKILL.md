---
name: rhssg
description: Check documentation against the Red Hat Supplementary Style Guide for product documentation. Use when the user wants to audit AsciiDoc files for compliance with Red Hat style conventions on grammar, formatting, structure, code examples, links, accessibility, release notes, and support terminology.
argument-hint: [file-path or glob]
allowed-tools: Read, Grep, Glob, Edit
---

Audit and fix the target file(s) by applying the rules from the [Red Hat Supplementary Style Guide](https://redhat-documentation.github.io/supplementary-style-guide/).

Target: `$ARGUMENTS`

If no target is given, use the currently open file from the IDE context. If neither is available, ask the user which file to check.

## Steps

1. **Read the target file(s).**
2. **Run every check category** listed below against the file content.
3. **Present a report** of all issues found, grouped by category and severity:
   - **Must fix** — Violations that cause build failures, legal issues, or serious style problems.
   - **Should fix** — Deviations from the guide that degrade quality or consistency.
   - **Consider** — Minor style preferences.
4. **Ask the user** whether to apply fixes automatically.
5. If approved, **make the edits** directly using the Edit tool.
6. **Present a summary** of all changes made.

---

## Style Guide Hierarchy

The Red Hat SSG supplements the IBM Style guide. Consult in this order:

1. Product-specific style guide (if applicable)
2. Red Hat Supplementary Style Guide (this skill)
3. IBM Style guide

---

## Check Categories

### 1. Conscious Language

Replace problematic terms:

| Do not use | Preferred alternatives |
|---|---|
| blacklist / whitelist | blocklist / allowlist (preferred), denylist / allowlist, blocklist / passlist |
| master / slave | primary / secondary, source / replica, controller / worker, initiator / responder |

When it is not possible to rewrite, use the alternatives above. Product teams may agree on specific replacement terms with engineering leadership.

### 2. Contractions

Do not use contractions in product documentation. Contractions are acceptable only in quick starts or content that uses a fairly conversational style.

### 3. Conversational Style

Default to "less conversational" style per the IBM Style guide. Cloud services documentation uses "fairly conversational" tone (with contractions).

### 4. Homographs

Flag sentences where the same homograph appears with different meanings in close proximity. Common homographs: application, attribute, block, coordinates, number, object, project.

### 5. Minimalism

Check for adherence to minimalism principles:

- **Customer focus:** Minimize content customers must wade through to reach real work. Separate conceptual information from procedural tasks.
- **Findability:** Use short paragraphs and sentences, bulleted lists where appropriate.
- **Titles:** Keep between 3 to 11 words. Too short lacks clarity; too long hurts search visibility.
- **Eliminate fluff:** Avoid long introductions and unnecessary context.
- **Error recovery:** Include troubleshooting, verification, and error recovery steps.

### 6. Users (Animate vs. Inanimate)

- Animate users (persons): use "who" — "Users *who* want to install..."
- Inanimate users (system accounts, SELinux users): use "that" — "Specify a user *that* is allowed..."
- Linux `root`, `guest`, and SELinux users like `user_u` are inanimate.

### 7. Titles and Headings

- Use sentence-style capitalization for all titles and headings.
- Do not use headline-style capitalization.

### 8. Non-Breaking Spaces

Use `{nbsp}` between "Red" and "Hat": `Red{nbsp}Hat`.

### 9. Product Names and Versions

- Use AsciiDoc attributes for product names and versions instead of hard-coding them.
- Only hard-code a version if it never changes (for example, "introduced in version X.Y").
- Define attributes for: full product name, abbreviated name, major/minor version.

### 10. Date Formats

Use "day Month year" (for example, 3 October 2019). Use "Month day, year" only when the default format causes clarity issues.

### 11. Single-Step Procedures

When a procedure contains only one step, use an unnumbered bullet (`*`), not a numbered list.

### 12. Admonitions

- Valid types: NOTE, IMPORTANT, WARNING, TIP.
- **Do not use CAUTION** — not fully supported by the Red Hat Customer Portal.
- Keep admonitions short and concise. Do not include procedures in an admonition.
- Only individual admonitions (no plural headings like "NOTES").
- Avoid placing multiple admonitions close together.

### 13. Lead-in Sentences

Do not use a lead-in sentence after Prerequisites or Procedure headings unless necessary to aid navigation or add clarity (for example, grouping a long list of prerequisites into sections).

Use a complete sentence for any lead-in sentence.

### 14. Prerequisites

- Write as checks that are true or completed before the procedure begins.
- Avoid imperative formations.
- Passive voice is acceptable for actions not completed by the current user.
- Use parallel language within the list.
- Examples of correct form: "JDK 11 or later is installed." / "You are logged in to the Administration Portal." / "You have validated Thing 1."

### 15. Short Descriptions (Abstracts)

Every module and assembly must include a short description after the title:

- At least 2-3 sentences.
- Include user intent: *what* users must do and *why*.
- Use active voice and present tense.
- Avoid self-referential language ("This topic covers...", "Use this procedure to...").
- Avoid feature-focused language ("This product allows you to...").
- Use customer-centric language ("You can... by...", "To..., configure...").
- Do not start a module with an admonition — always include the short description first, even before a Technology Preview admonition.

### 16. Commands in Code Blocks

- One command per code block per procedure step.
- Separate commands from example output into individual code blocks.
- Use bold formatting for commands in code blocks (requires `subs="+quotes"`).
- **Do not use callouts** to explain commands or variables. Instead, follow the code block with:
  - A simple sentence for a single element.
  - A definition list introduced with "where:" for multiple parameters (begin each description with "Specifies").
  - A bulleted list for YAML structure explanations.

### 17. Code Block Syntax

- Use `[source,<language>]` with the correct source language for syntax highlighting.
- **Do not use `bash`** for terminal commands — it incorrectly interprets `#` as a comment. Use `terminal` instead.
- Comment out ellipses in YAML code blocks: `# ...` (YAML reserves `...` for document end).
- Use reserved IP addresses for documentation (RFC 5737: 192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24; RFC 3849: 2001:0DB8::/32).
- Use reserved MAC addresses (RFC 7042: unicast 00:00:5E:00:53:00-FF, multicast 01:00:5E:90:10:00-FF).

### 18. User-Replaced Values

Format: `_<value_name>_` (angle brackets, underscores for multi-word, lowercase, italicized).

- In code blocks, use `subs="+quotes"` and `__<value_name>__` for italic.
- For XML code blocks, use `_${value_name}_` instead (curly braces + dollar sign, because XML uses angle brackets).
- Explain user-replaced values with a definition list after the code block, introduced with "where:" and descriptions starting with "Specifies".

### 19. Commands Requiring Root Privileges

- Prefer `sudo` over `su -` for temporary root privileges.
- When using `sudo`, show the `$` prompt, not `#`.
- Do not rely solely on a shell prompt to indicate privilege level — state the requirement in text or prerequisites.
- If multiple commands require root, add introductory text about the requirement.

### 20. Long Code Examples

All code blocks must be necessary, accurate, and helpful. Code blocks must be as copy-and-paste friendly as possible, with the exception of user-replaced values.

### 21. Cross-References

- Include only when necessary.
- If the information is critical, consider including it instead of cross-referencing.

### 22. External Links

- Avoid unnecessary links to sites not owned by Red Hat or IBM (upstream sites like GitHub count as external).
- Link to top-level pages, not deep links, when possible.
- Do not use bare URLs — always include meaningful link text.
- Do not use URL shorteners.
- By default, links are followable. Do not use `nofollow` unless absolutely necessary.

### 23. Links to Red Hat Documentation

Use `latest` in the URL so links resolve to the last published version.

### 24. Links to Knowledgebase Articles

- Use the article title as link text, or use descriptive running text.
- When in Additional resources, put the article title first, followed by `(Red{nbsp}Hat Knowledgebase)`.

### 25. Man Page References

Format: `_<man_page_name>_(_<section_number>_)` man page on your system

Do not link to websites for man page content — man pages vary between systems.

### 26. Accessibility

- Do not use color as the only means of conveying information.
- Avoid directional indicators (left, right, above, below) for navigation instructions.
- All images must have meaningful alt text describing what the image *does*, not what it looks like.
- Avoid images of text.
- All links must have descriptive text — never "click here".
- Tables must have a simple left-to-right, top-to-bottom reading order. Avoid irregular headers, spanned rows/cells, and blank header cells.
- Nest headings correctly (H1 → H2 → H3) without skipping levels.
- Use correct AsciiDoc tags to produce well-formed HTML.

### 27. Graphical Interfaces

- Use bold text for all labeled GUI element names: `*Button Name*`.
- If an element is not labeled in the GUI, use a generic description without bold.
- Use "enter" (not "type" or "input") for text entry, with the value in monospace.
- Avoid screenshots for accessibility and localization reasons. If used, include unique and descriptive alt text.
- For detailed UI writing guidance, see [PatternFly UX writing](https://www.patternfly.org/ux-writing/about).

### 28. Legal

- Avoid all references to costs and charges of Red Hat products.
- Avoid statements that predict future releases or plans. When unavoidable (deprecation notices), use "anticipate", "expect", or "plan" — never promise a specific release number or date.

### 29. Technology Preview

- Capitalize both words: "Technology Preview" (never "Tech Preview" or "Technical Preview").
- Never use "supported as a Technology Preview".
- Use neutral words: available, provide, capability, functionality.
- Include the standard IMPORTANT admonition template verbatim.
- In release notes, end heading with "(Technology Preview)" and mention TP status again in body.

### 30. Developer Preview

- Capitalize both words: "Developer Preview".
- Never use "supported as a Developer Preview".
- Include the standard IMPORTANT admonition template verbatim.
- Check with Content Strategist whether Developer Preview docs are published for your product.

### 31. Release Notes

When the target file is a release note, additionally check:

**General:**
- Write from the perspective of just after the release.
- Use simple present for current state, simple past for previous state.
- Do not use future tenses or "should"/"might" for post-update state.
- Do not use "now" to refer to post-update state.
- Use "before this update" instead of "previously".
- Do not begin a release note with an admonition.

**Headings:**
- Sentence-style capitalization, under 120 characters.
- Do not start with a gerund (gerunds are only for procedural content).
- Be specific — avoid generic headings like "Program no longer crashes".

**Format:**
- Each release note is a description list item (`Heading::`) not a section heading.
- Jira references go on the line directly after the entry, not in parentheses or brackets.

**Bug fixes (CCFR structure):**
- Cause (past tense) → Consequence (past tense) → Fix (present perfect/present) → Result (present tense).
- Template: "Before this update, _<cause>_. As a consequence, _<consequence>_. With this release, _<fix>_. As a result, _<result>_."

**Features and enhancements:**
- Describe customer benefit.
- Link to product documentation.
- For former Technology Previews reaching full support, state that clearly.

**Rebases:**
- Write version in X.Y.Z format only (no build or el suffix).
- Include a parallel list of highlights.
- Avoid blank rebase descriptions.

**Technology Preview notes:**
- End heading with "(Technology Preview)".
- Mention TP status again in body text.
- Never use "supported" in TP descriptions.
- Repeat TP release notes in subsequent releases until fully supported or removed.

**Deprecations:**
- Describe the deprecated feature and provide an alternative.
- Do not use the term "Recommended".
- Do not promise fixes in future releases.

**Known issues:**
- Include workaround in a separate paragraph: "To work around this problem, _<workaround>_."
- If no workaround exists, state: "No known workaround exists."

**Fixed issues:**
- Follow CCFR tense logic.
- Use "before this update" (not "previously").

## Scope

- Check style and formatting only. Do not change technical content unless a style rule requires it.
- Do not rewrite for clarity beyond what the rules require.
- Do not change code inside `[source]` blocks (except for formatting attributes like `subs`).
- Preserve all AsciiDoc structural elements (admonitions, anchors, xrefs, attributes, includes).

## Reference

- Full guide: https://redhat-documentation.github.io/supplementary-style-guide/
- Source: https://github.com/redhat-documentation/supplementary-style-guide
- The Red Hat SSG supplements the IBM Style guide. For topics not listed here, defer to IBM Style.
