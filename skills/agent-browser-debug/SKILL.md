---
name: agent-browser-debug
description: "[INTERNAL TEMPLATE] Debugging execution protocol. Called by agent-browser orchestrator."
---

# Debug Diagnosis — Execution Protocol

> **Internal template.** Entry point: `agent-browser` skill.

---

## Triage Sequence (run in order, stop at first finding)

```bash
AGENT_BROWSER_HEADED=1 agent-browser open <url>

# Step 1: JS errors (explains 80% of broken UIs)
AGENT_BROWSER_HEADED=1 agent-browser errors

# Step 2: Console warnings
AGENT_BROWSER_HEADED=1 agent-browser console --pattern "error|warn"

# Step 3: Failed network requests
AGENT_BROWSER_HEADED=1 agent-browser network requests --filter "api"

# Step 4: Auth state (check first if login/session suspected)
AGENT_BROWSER_HEADED=1 agent-browser cookies         # token present?
AGENT_BROWSER_HEADED=1 agent-browser storage local   # JWT present?

# Step 5: DOM state
AGENT_BROWSER_HEADED=1 agent-browser snapshot -i
AGENT_BROWSER_HEADED=1 agent-browser is enabled @e5
AGENT_BROWSER_HEADED=1 agent-browser is visible @e3

# Step 6: Form validity (if button appears enabled but won't click)
AGENT_BROWSER_HEADED=1 agent-browser eval "document.querySelector('form').checkValidity()"

# Step 7: Deep investigation
AGENT_BROWSER_HEADED=1 agent-browser inspect         # DevTools (CLI stays live)
```

---

## Recovery Patterns

| Symptom | Next action |
|:---|:---|
| JS errors found | Report errors → **judgment gate**: continue or stop? |
| Auth token missing | Re-run auth flow, recheck |
| Network 4xx/5xx | Report endpoint + status → judgment gate |
| `is enabled` false | Check form validity via `eval` |

---

## Judgment Gates

Pause when:
- JS errors found (show to user, ask: bug or known?)
- Auth token missing (hard to auto-recover without credentials)
- API returning unexpected errors (user needs to decide next step)

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Skip `errors`, jump to DOM | Errors explain 80% — always check first |
| Auth bug → jump to JS debugging | Check `cookies` + `storage local` first |
| Think `inspect` kills CLI | It doesn't — both work simultaneously |
