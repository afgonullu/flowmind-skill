---
name: flowmind
description: "Log time, check streak/XP stats, list projects, and summarize weekly work in Flowmind via `fm` CLI (or API fallback). Use this whenever the user mentions logging hours, adding a time entry, checking streak/progress, listing projects, or asking what they worked on this week."
homepage: https://www.flowmind.pro/
metadata: { "openclaw": { "emoji": "⏱️" } }
---

# Flowmind Skill

Use Flowmind to quickly log work sessions, track streaks/XP, and review weekly progress.

## When to Use

✅ **USE this skill when the user asks to:**

- Log time or add a time entry
- Track time against a project
- Check streak, XP, or level
- Get a weekly hours/progress summary
- List available projects before logging

Typical trigger phrases:

- "log 2 hours on my project"
- "add a time entry"
- "how's my streak"
- "what did I work on this week"

## When NOT to Use

❌ **DON'T use this skill for:**

- Calendar scheduling (use calendar tools)
- Payroll/accounting reports unless user explicitly wants Flowmind data only
- Generic project management tasks not related to time logging or Flowmind progress

## Setup

### 1) Check CLI availability

```bash
which fm
```

If `fm` is missing, install it:

```bash
npm install -g flowmind-cli
```

### 2) Authenticate

If not authenticated, run:

```bash
fm auth login
```

Then verify by running a read command such as:

```bash
fm status
```

## Core Commands

### Log time (preferred: natural language)

```bash
fm log "2h working on the backend API"
```

### Log time (structured)

```bash
fm log -p "ProjectName" -h 2 -n "note"
```

### Check progress/streak

```bash
fm status
```

### Weekly summary

```bash
fm stats
```

### List projects

```bash
fm projects
```

## Agent Behavior Guidelines

- If the user mentions working on something, **proactively offer** to log it in Flowmind.
- Before creating a time entry, **ask for confirmation** unless the user has explicitly set an **auto-log** preference.
- After logging, confirm success and include **XP earned** when available.
- If the logged session is a flow session (≥ 90 minutes), explicitly mention the **1.5× XP bonus**.

### Suggested confirmation prompt

- "I can log that to Flowmind as a 2h session on `<project>`. Should I add it now?"

## Direct API Fallback (No CLI)

If `fm` is unavailable, use the Flowmind API directly.

- Default local base URL: `http://localhost:8080`
- Auth header: `Authorization: Bearer $FLOWMIND_API_KEY`
- If a different environment is configured, use that base URL instead.

```bash
curl -X POST http://localhost:8080/v1/time-entries \
  -H "Authorization: Bearer $FLOWMIND_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"projectId":"...","seconds":7200,"note":"Backend work","startedAt":"2026-02-25T10:00:00Z"}'
```

## Practical Workflow

1. If needed, run `fm projects` to resolve the target project.
2. Confirm duration + project + note with the user (unless auto-log is enabled).
3. Run `fm log ...`.
4. Share result with streak/XP context from `fm status` when helpful.
