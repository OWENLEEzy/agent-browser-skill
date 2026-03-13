---
name: agent-browser-e2e
description: Use when verifying that a web page works correctly — login flows, form submission, redirect testing, UI validation
---

# Agent Browser — E2E / Testing

**Recommended config:** `AGENT_BROWSER_NATIVE=1` (faster, no Node.js overhead)

---

## Environment Check

```bash
printenv | grep AGENT_BROWSER   # already set? Omit the corresponding inline flag
```

---

## Workflow

```bash
AGENT_BROWSER_NATIVE=1 agent-browser open https://app.example.com
AGENT_BROWSER_NATIVE=1 agent-browser snapshot -i
AGENT_BROWSER_NATIVE=1 agent-browser find label "Email" fill "test@example.com"
AGENT_BROWSER_NATIVE=1 agent-browser find role button click --name "Sign Up"
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
AGENT_BROWSER_NATIVE=1 agent-browser screenshot --full result.png
```

---

## Key Patterns

- Always `wait --load networkidle` after navigation before asserting
- Use `screenshot --full failure.png` on failure as evidence
- Use `screenshot --annotate` for AI-readable labeled screenshots (or set `AGENT_BROWSER_ANNOTATE=1`)
- Use `diff screenshot --baseline` for visual regression

---

## When Tests Fail — Inline Debug Steps

**Don't switch to `agent-browser-debug` yet.** Run these first:

```bash
agent-browser errors                          # Step 1: JS errors?
agent-browser console --pattern "error|warn"  # Step 2: console logs?
agent-browser snapshot -i                     # Step 3: DOM state?
agent-browser screenshot --full failure.png   # Step 4: capture evidence
```

If still unresolved, *then* load `agent-browser-debug`.

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Assert before page loads | `wait --load networkidle` first |
| Stale refs after navigation | Re-run `snapshot` after every nav |
| No evidence on failure | Always capture `screenshot --full failure.png` |
| Visual regressions undetected | Save baseline with `screenshot --full baseline.png` first |

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

If user says yes → update the Pitfalls section + bump version:
- New pitfall → patch x.x.**N**
- New command → add to `agent-browser-commands` skill + minor x.**N**.0
- After updating → scan all other `agent-browser-*` skills for the same issue (dead links, stale references, similar pitfalls)

### Anti-rationalization

| Excuse | Reality |
|:---|:---|
| "No pitfalls encountered" | Write "✅ No new pitfalls" — still required |
| "I mentioned issues inline" | Inline ≠ structured retrospective |
| "Task was simple" | Short list is fine. No list is not. |
| "User can see what happened" | User needs a structured summary to decide on iteration |
| "Not sure if it's a skill issue" | If you worked around something, it's a pitfall |
