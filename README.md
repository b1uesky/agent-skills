# agent-skills

Portable skills repository for `skills` CLI installation across multiple coding agents.

## Layout

This repo uses the standard `skills/` layout expected by `skills` CLI:

```text
skills/
  jira-ticket-workflow/
    SKILL.md
```

## Install

Install this repo to one or more agents from GitHub:

```bash
npx skills add b1uesky/agent-skills
```

Install only the JIRA workflow skill to selected agents:

```bash
npx skills add b1uesky/agent-skills \
  --skill jira-ticket-workflow \
  --agent codex \
  --agent claude-code \
  --agent cursor
```

List available skills without installing:

```bash
npx skills add b1uesky/agent-skills --list
```

