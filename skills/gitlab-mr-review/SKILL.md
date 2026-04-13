---
name: gitlab-mr-review
description: >-
  Review a GitLab merge request from all critical software development
  perspectives: correctness, test coverage, DRY, security, CI/CD, and style.
  Uses glab CLI for MR metadata and git for diffs, reads relevant source files
  for context, then delivers structured feedback. Use when the user asks to
  review an MR, review a merge request, check MR changes, or provide MR
  feedback.
---

# GitLab MR Review

## Goal

Provide a thorough, constructive code review of a GitLab merge request by:

1. Fetching MR metadata with `glab`.
2. Retrieving the full diff with `glab` or `git`.
3. Reading surrounding source and test files for context.
4. Delivering structured feedback across all critical dimensions.

## When To Use

Use this skill when the user asks to:

- review a GitLab merge request
- check or audit MR changes
- provide feedback on an MR before merge
- look at a specific MR number (e.g., "review MR 600")

## Workflow

### 1. Gather MR metadata and diff

Run these two commands in parallel:

```bash
glab mr view <MR_NUMBER>
glab mr diff <MR_NUMBER>
```

From the output, extract:

- title, author, labels, state
- MR description (summary, test plan, change type)
- full file-level diff

### 2. Read surrounding source for context

For each file changed in the diff:

- Read the **current version** of the file (not just the diff hunk) to understand the broader context — class structure, neighboring methods, existing patterns.
- Read the **corresponding test file** if one exists, to assess test coverage.
- If the MR introduces a new dependency or library call, check how that library is used elsewhere in the codebase.

Do not skip this step. Reviewing a diff without its surrounding context misses architectural issues, pattern violations, and regression risks.

### 3. Analyse changes across all review dimensions

Evaluate each of the following areas. Only include a dimension in the final output when there is something meaningful to say.

| Dimension | What to look for |
|---|---|
| **Correctness** | Logic bugs, off-by-one errors, null/undefined risks, race conditions, behavior changes that may not be intended |
| **Error handling** | Missing catch blocks, swallowed errors, incorrect error shapes, unhelpful error messages |
| **Security** | Credential leaks, injection risks, missing auth checks, overly broad permissions |
| **Test coverage** | Untested new code paths, test names that no longer match behavior, missing edge-case tests |
| **DRY / maintainability** | Duplicated logic that should be extracted, magic numbers, overly complex functions |
| **API & data contract** | Breaking changes to public interfaces, missing validation, backwards-compatibility risks |
| **Performance** | Unbounded queries, missing pagination, N+1 patterns, unnecessary allocations in hot paths |
| **CI/CD & config** | Pipeline correctness, safe defaults, missing environment guards |
| **Style & conventions** | Import ordering, naming conventions, project-specific patterns violated |
| **Documentation** | Missing or outdated README sections, misleading comments |

### 4. Format the review

Structure the review as follows:

```markdown
## MR <NUMBER> Review: <TITLE>

### Overview
Brief summary of what the MR does and how many logical changes it contains.

### 1. <Dimension Name>
Findings grouped by dimension. For each finding:
- State what the issue is.
- Reference the specific file and code.
- Explain *why* it matters.
- Suggest a concrete fix when possible.

### ...

### Summary
| Area | Verdict |
|---|---|
| <Area> | One-line assessment |

Closing paragraph with overall assessment and prioritised action items.
```

Guidelines for tone:

- Be constructive, not prescriptive — use "consider" and "suggest" over "you must".
- Distinguish between blocking issues and nice-to-haves.
- Acknowledge what the MR does well.
- When suggesting a refactor, explain the benefit concretely (e.g., "single place to change retry config" rather than "better separation of concerns").

### 5. Report back

Always include:

- MR number, title, and author
- Link to the MR
- Structured review with numbered dimensions
- Summary table with per-area verdicts
- Clear list of action items ranked by priority

## Guardrails

- Never approve or merge the MR — only provide review feedback.
- Never modify local files or branches as part of the review.
- If the diff is very large (>1000 lines), tell the user and offer to focus on specific files or areas.
- If `glab` is not installed or authenticated, fall back to `git` commands or ask the user for access.
- Do not fabricate issues. If a dimension has no findings, omit it or state it looks good.

## Quick Checklist

- [ ] MR metadata fetched via `glab mr view`
- [ ] Full diff fetched via `glab mr diff`
- [ ] Surrounding source files read for context
- [ ] Corresponding test files checked
- [ ] All relevant review dimensions evaluated
- [ ] Review formatted with structured headings and summary table
- [ ] Constructive tone with concrete suggestions
- [ ] Action items prioritised
