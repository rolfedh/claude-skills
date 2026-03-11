---
name: quarkus-pr
description: Create a pull request for the Quarkus framework repository (quarkusio/quarkus). Use when the user wants to submit a PR to the Quarkus project, ensuring compliance with contributing guidelines, code formatting, commit standards, and the PR template.
argument-hint: [issue-number]
allowed-tools: Read, Grep, Glob, Bash, Edit
---

Create a pull request for the Quarkus framework repository following the project's CONTRIBUTING.md guidelines.

Target: `$ARGUMENTS`

If an issue number is given, link it in the PR body. If neither is provided, use the current branch and ask the user which issue (if any) to link.

## Detect the Repo Root

Do NOT hardcode a path. Use the current working directory. Verify it is a Quarkus checkout by checking for `independent-projects/` or the `io.quarkus` group ID in the root `pom.xml`. If not, warn the user and stop.

## Pre-flight Checks

Before creating the PR, verify each of these. If any check fails, fix it or warn the user. Present a summary of all check results before proceeding.

1. **Current branch is not `main`.**
   If on `main`, stop and ask the user to create a feature branch first.

2. **Code formatting.**
   Run `./mvnw process-sources` from the repo root. If any files change, show the user which files were modified and **ask for confirmation** before staging and committing them. CI rejects PRs where formatting produces diffs.

3. **No `@author` tags.**
   Search changed Java files (`git diff main..HEAD --name-only -- '*.java'`) for `@author` in Javadoc. If found, remove them — the project disallows `@author` tags.

4. **Atomic, semantic commits.**
   Run `git log main..HEAD --oneline` and review. If there are fixup/WIP commits, advise the user to squash before merging (fixups are acceptable during review but must be cleaned up).

5. **Lambdas and streams in runtime code.**
   If the changes touch runtime modules (paths containing `/runtime/`), check for heavy use of lambdas and streams. Flag any excessive use — the project minimizes these in runtime code for footprint optimization.

6. **Tests included.**
   Check that the diff includes test changes. If no tests are added or modified, warn the user: "Don't forget to include tests in your pull requests."

7. **Native test configuration.**
   If new integration test modules were added, verify they are listed in `.github/native-tests.json`. Each new integration test module must be explicitly configured there.

8. **Documentation.**
   If the change adds or modifies user-facing behavior, check whether docs were updated (files under `docs/`). Remind the user if not: "Also don't forget the documentation (reference documentation, javadoc...)."

## Create the PR

1. **Determine the upstream repo.** Check `git remote -v` for an `upstream` remote pointing to `quarkusio/quarkus`. If found, use `--repo quarkusio/quarkus` with `gh pr create`. If the `origin` remote points directly to `quarkusio/quarkus`, omit `--repo`. If neither, warn the user to configure their remotes.

2. **Push the branch** (if not already pushed):
   ```
   git push -u origin HEAD
   ```

3. **Build the PR body** using the official Quarkus PR template from CLAUDE.md. The body must match this structure exactly — do not add extra sections (no checklist, no emoji):

   ```
   <!--
   If this is your first time contributing to the project,
   please consider reviewing https://github.com/quarkusio/quarkus/blob/main/CONTRIBUTING.md
   -->

   <Description of what the change does and why>

   <!--
   Please include in the description above the list of GitHub issues this Pull Request addresses in the following format:

   * Fixes #xxxxx
   * Fixes #yyyyy
   * Fixes #zzzzz
   * ....

   See https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue
   for more information about linking issues to the Pull Request.
   -->
   ```

4. **Write the description:**
   - Summarize what the change does and why, based on the diff and commit messages.
   - Follow the writing style guide at `~/.claude/style-guide.md` for voice, word choices, and punctuation.
   - Place `* Fixes #<number>` lines inside the description text (above the closing HTML comment), not hidden inside the comment block.

5. **Create the PR** with `gh pr create`. Use a HEREDOC for the body to preserve formatting. Keep the title under 70 characters.

6. **Return the PR URL** to the user.

## Important Rules

- All contributions fall under the Apache License, version 2.0 (ASL 2.0).
- All contributions require compliance with the Developer Certificate of Origin (DCO).
- Never sign commits as Claude.
- Never use `--no-verify` or skip pre-commit hooks.
- Never force-push without explicit user approval.
- The upstream remote is `https://github.com/quarkusio/quarkus` — PRs go from the user's fork to upstream `main`.
