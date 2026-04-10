---
name: Permission Learner
slug: permission-learner
version: 1.0.0
description: "Learn tool permission preferences from user feedback. Build permission rules over time. Based on Claude Code permission classifier."
---

## When to Use

- User approves or denies a tool execution
- User provides feedback on tool behavior
- Repeated tool permission questions
- New tool type encountered

## Core Concept

Build a permission profile from feedback:

```
Permission Profile:
  file_write: [ask, auto_approve_safe]
  bash: [ask]
  web_fetch: [auto_approve]
  subprocess: [deny]
```

## Feedback Types

| Feedback | Action |
|----------|--------|
| User approves tool | Add to auto-approve list |
| User denies tool | Add to deny list |
| User says "yes, always" | Make permanent rule |
| User says "no, never" | Make permanent block |
| User says "just this once" | One-time approval |

## Learning Rules

### From Explicit Feedback

```
User: "yes do it"
→ Tool: bash → ["mkdir /tmp/test"]
→ Learn: mkdir is auto-approve if benign

User: "no, don't do that"
→ Tool: bash → ["rm -rf /"]
→ Learn: rm -rf requires explicit approval
```

### From Patterns

```
Same tool + same pattern + approved 3x
→ Consider auto-approve

Same tool + destructive pattern + first time
→ Always ask
```

## Permission Levels

| Level | Behavior |
|-------|----------|
| auto_approve | Execute without asking |
| ask | Always prompt |
| deny | Never execute without override |
| learn | Watch and learn, don't act |

## Tool Risk Classification

### High Risk (Always Ask)
- bash with rm, mv, chmod
- file_write to system dirs
- network calls with secrets
- subprocess execution

### Low Risk (Auto-Approve After 3x)
- file_read in workspace
- web_fetch for research
- mkdir in /tmp
- git add/commit (non-destructive)

### Medium Risk (Ask First Time)
- file_write to project
- bash with install commands
- git push
- new tool type

## Usage

### When User Grants Permission

```
User approves: "do it"
→ Record: tool + context + approval
→ If 3+ similar: suggest auto-approve rule
```

### When User Denies Permission

```
User denies: "don't do that"
→ Record: tool + context + denial
→ Future similar prompts: show warning
```

### When Creating Rules

```
Permission Learned:
  Tool: bash
  Pattern: mkdir -p ~/projects/*
  Decision: auto_approve
  Confidence: 3 confirmations
  Validated: 2026-04-10

User can override with: "never auto-approve mkdir"
```

## Profile Storage

Save to: `memory/permission-profile.md`

```
# Permission Profile

## Auto-Approve (Safe)
- mkdir (workspace dirs only)
- git add/commit
- file_read (project files)

## Ask First
- bash (any command)
- file_write (outside workspace)
- web_fetch (with credentials)

## Always Deny
- rm -rf /*
- chmod 777 system
- Keychain access
```

## Anti-Patterns

- Don't auto-approve dangerous commands
- Don't learn from single approval
- Don't make rules without confirmation
- Don't override user deny without explicit override
