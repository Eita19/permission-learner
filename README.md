# OpenClaw Permission Learner

[![Stars](https://img.shields.io/github/stars/Eita19/openclaw-skills?style=flat)](https://github.com/Eita19/openclaw-skills)
[![Based on](https://img.shields.io/badge/based-Claude%20Code-7B68EE?style=flat)](https://github.com/anthropics/skills)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> 🤖 **Learn tool permissions from user feedback** — based on Claude Code's permission system

**Build permission rules over time. Less asking, more doing.**

---

## What is Permission Learner?

Every time you ask "Can I run this?", you teach your agent something.

Permission Learner builds a permission profile from user feedback:

- **Approve → Learn** — User says yes, remember the pattern
- **Deny → Remember** — User says no, avoid next time
- **Rule → Auto-approve** — 3 confirmations = auto-approve next time

---

## When to Use

- User approves a tool execution
- User provides feedback on tool behavior
- Same tool asks repeatedly
- New tool type encountered

---

## Permission Levels

| Level | Behavior |
|-------|----------|
| auto_approve | Execute without asking |
| ask | Always prompt |
| deny | Never without override |
| learn | Watch and learn |

---

## Risk Classification

### High Risk (Always Ask)
- rm -rf, chmod 777
- System directory writes
- Network calls with secrets
- subprocess execution

### Low Risk (Auto-After 3x)
- file_read in workspace
- mkdir in /tmp
- git add/commit

### Medium Risk (Ask First Time)
- file_write to project
- bash install commands
- git push

---

## Learning Flow

```
User: "yes do it"
→ Tool: bash → ["mkdir /tmp/test"]
→ Learn: mkdir is auto-approve if benign

User: "no, don't do that"
→ Tool: bash → ["rm -rf /"]
→ Learn: rm -rf requires explicit approval
```

---

## Profile Storage

Save to: `memory/permission-profile.md`

```markdown
## Auto-Approve (Safe)
- mkdir (workspace only)
- git add/commit
- file_read (project files)

## Ask First
- bash (any command)
- file_write (outside workspace)
- web_fetch (with credentials)

## Always Deny
- rm -rf /*
- chmod 777 system
```

---

## Quick Start

```bash
clawhub install permission-learner
```

---

*Based on [Claude Code permission system](https://github.com/anthropics/skills) — [Eita19](https://github.com/Eita19)*
