---
name: agent-browser-automate
description: Use when automating browser workflows — form filling, file upload/download, login session reuse, multi-step flows
---

# Agent Browser — Automation

**Recommended config:** `--profile <path>` to persist login session across runs

---

## Environment Check

```bash
printenv | grep AGENT_BROWSER   # already set? Omit the corresponding inline flag
```

---

## Workflow

```bash
# First run: log in and save session
agent-browser --profile ~/.myapp open https://app.example.com/login
agent-browser --profile ~/.myapp find label "Email" fill "me@example.com"
agent-browser --profile ~/.myapp find label "Password" fill "secret"
agent-browser --profile ~/.myapp find role button click --name "Sign In"
agent-browser --profile ~/.myapp wait --load networkidle

# Subsequent runs: already authenticated
agent-browser --profile ~/.myapp open https://app.example.com/dashboard
agent-browser --profile ~/.myapp find role button click --name "Export"
agent-browser --profile ~/.myapp find role button click --name "Export CSV" download ~/report.csv
```

---

## Key Patterns

| Pattern | Command |
|:---|:---|
| Persist auth across runs | `--profile ~/.myapp` |
| Set default download dir | `--download-path ~/Downloads` |
| Scroll inside modal/container | `scroll down --selector ".modal-body"` |
| Dismiss unexpected popup | `dialog dismiss` |
| Upload file | `find label "Resume" upload ./resume.pdf` |
| Append text (don't clear) | `type @e1 "text"` — vs `fill` which clears first |

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Profile session expired (401) | Delete profile dir, re-auth |
| `fill` overwrites existing content | Use `type` to append to existing text |
| File upload fails | Use `find label "..." upload <path>`, not `click` then `fill` |
| Can't scroll inside modal | `scroll down --selector ".modal-body"` |

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
