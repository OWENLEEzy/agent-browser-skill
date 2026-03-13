---
name: agent-browser-debug
description: Use when investigating why a web page isn't working — JS errors, broken buttons, failed API calls, unexpected UI state
---

# Agent Browser — Debugging

**Recommended config:** `AGENT_BROWSER_HEADED=1` + `inspect` for DevTools access

---

## Environment Check

```bash
printenv | grep AGENT_BROWSER   # already set? Omit the corresponding inline flag
```

---

## Workflow

```bash
AGENT_BROWSER_HEADED=1 agent-browser open https://app.example.com/problem-page
AGENT_BROWSER_HEADED=1 agent-browser errors                       # 1. JS errors first
AGENT_BROWSER_HEADED=1 agent-browser console --pattern "error|warn"
AGENT_BROWSER_HEADED=1 agent-browser network requests --filter "api"
AGENT_BROWSER_HEADED=1 agent-browser snapshot -i                  # DOM state
AGENT_BROWSER_HEADED=1 agent-browser is enabled @e5               # check element state
AGENT_BROWSER_HEADED=1 agent-browser eval "document.querySelector('form').checkValidity()"
AGENT_BROWSER_HEADED=1 agent-browser inspect                      # open DevTools (CLI stays live)
```

---

## Key Patterns

- **Always run `errors` first** — JS errors explain most broken UIs
- `inspect` opens DevTools while CLI commands keep working simultaneously
- `get cdp-url` → hand off CDP WebSocket to an external tool
- `highlight <selector>` → visually confirm which element you're targeting

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Skip `errors`, jump to DOM | Errors explain 80% of issues — always check first |
| `is enabled` returns false, give up | Use `eval` to check form validation state |
| Think `inspect` kills CLI | It doesn't — both work simultaneously |
| Button looks enabled but won't click | Check `is enabled`, then `eval` form validity |

---

## After Task Completion

**MANDATORY. Do not skip. There are no exceptions.**

### Step 1 — List pitfalls (always, even if none)

Format each finding:

🕳️ **Pitfall:** [what happened]
   **Workaround:** [what fixed it]
   **In skill?** ✅ Yes — [section] / ❌ Missing

If zero pitfalls: write "✅ No new pitfalls encountered."

### Step 2 — Ask the user

> 「任务完成。遇到 N 个坑，其中 X 个还没写进 skill。要更新吗？」

If yes → update Pitfalls + bump version. Then scan all other `agent-browser-*` skills for the same issue.

### Anti-rationalization

| Excuse | Reality |
|:---|:---|
| "No pitfalls encountered" | Write "✅ No new pitfalls" — still required |
| "I mentioned issues inline" | Inline ≠ structured retrospective |
| "Task was simple" | Short list is fine. No list is not. |
| "User can see what happened" | User needs a structured summary to decide on iteration |
| "Not sure if it's a skill issue" | If you worked around something, it's a pitfall |
