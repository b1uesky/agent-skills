---
name: jira-ticket-workflow
description: >-
  Follow a GitLab plus JIRA delivery workflow: branch from the latest main or
  master using the ticket number, implement the fix, verify with lint/build/test,
  commit with a [TICKET] prefixed conventional commit, and open a merge request
  with the team template. Use when the user asks to start, work on, finish, or
  open an MR for a JIRA ticket.
---

# JIRA Ticket Workflow

## Goal

Complete a JIRA ticket end to end in a consistent way:

1. Sync from the default branch.
2. Create a ticket-named branch.
3. Implement and verify the change.
4. Commit with the team's format.
5. Push and open a merge request with the expected template.

## When To Use

Use this skill when the user asks to:

- start working on a JIRA ticket
- pick up a bug or feature ticket
- create a branch from the latest `main` or `master`
- commit work using the team's ticketed commit format
- open a GitLab merge request with a standard description

## Workflow

### 1. Confirm the ticket and repo state

- Identify the ticket key, for example `DUNE-4222`.
- Check whether the repo is clean before making changes.
- If there are unexpected unrelated local changes, stop and ask the user how to proceed.

### 2. Sync from the default branch

- Detect whether the repo uses `main` or `master`.
- Fetch the latest remote state for that branch.
- Create a new branch named exactly as the ticket key unless the user specifies another convention.

Example:

```bash
git fetch origin master
git checkout -b DUNE-4222 origin/master
```

### 3. Implement the requested change

- Make the smallest safe fix that resolves the ticket.
- Prefer fixing shared logic at the integration boundary when it reduces risk and avoids changing many callers.
- Add or update focused tests when they materially reduce regression risk.

### 4. Verify the result

- Run the project's standard verification commands. Prefer:
  - `npm run lint`
  - `npm run build`
  - `npm test`
- If one of these scripts is not a real non-interactive build or cannot complete in the current environment, report that clearly instead of hiding it.

### 5. Review the diff before commit

- Inspect `git status`, `git diff`, and recent commit history.
- Keep the commit scoped to the ticket-related change only.
- Do not include unrelated files.

### 6. Commit using the team format

- Use conventional commit style.
- Prefix the subject with `[TICKET_NUMBER]`.
- Make the branch name and commit prefix match the JIRA ticket.

Format:

```text
[DUNE-4222] fix(stream): normalize PDW object types for data access grants
```

### 7. Push and open a merge request

- Push the branch with upstream tracking.
- Create the GitLab merge request with `glab`.
- Use the commit message as the MR title unless the user requests otherwise.
- Use the team MR template exactly when provided.

MR body template:

```markdown
## Change type
- [ ] 🆕 Feature
- [ ] 🐛 Bugfix
- [ ] 📓 Documentation
- [ ] 🔧 Refactoring

## Changes
[DUNE-XXXX](https://cimpress-support.atlassian.net/browse/DUNE-XXXX)

{- Previously, it works like this -}

{+ Currently, it works like that +}

## Testing
### New unit tests
1. Bla
### New integration tests
1. Bla
### Manual tests
1. Bla
```

### 8. Report back clearly

Always return:

- branch name
- commit hash and commit message
- MR URL
- verification status for lint, build, and test
- any caveats, such as partial verification or existing script limitations

## Guardrails

- Never revert unrelated user changes without explicit approval.
- Never force push unless the user explicitly asks.
- Never amend a commit unless the user explicitly asks.
- If verification fails, explain what failed and whether it is caused by the change or by an existing repository issue.
- If the user asks for a ticket workflow but not a merge request, stop after the requested scope.

## Quick Checklist

- [ ] Ticket key confirmed
- [ ] Default branch updated
- [ ] Ticket branch created
- [ ] Fix implemented
- [ ] Focused tests added if needed
- [ ] Lint, build, and test attempted
- [ ] Commit created with `[TICKET]` prefixed conventional format
- [ ] Branch pushed
- [ ] MR created with required template
- [ ] Final status reported
