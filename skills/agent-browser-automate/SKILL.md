---
name: agent-browser-automate
description: "[INTERNAL TEMPLATE] Task automation execution protocol. Called by agent-browser orchestrator."
---

# Task Automation — Execution Protocol

> **Internal template.** Entry point: `agent-browser` skill.

---

## Auth Setup (choose one, in priority order)

```bash
# Option 1 (recommended): Auth Vault — encrypted, team-shareable
agent-browser auth login <vault-name>
agent-browser open <url>

# Option 2: Persistent profile (local only)
agent-browser --profile ~/.myapp open <url>

# Option 3: Token injection
agent-browser open <url>
agent-browser storage local set authToken "<token>"
agent-browser reload

# Option 4: HTTP Basic Auth
agent-browser open <url>
agent-browser set credentials <user> <pass>

# Option 5: Bearer header
agent-browser --headers '{"Authorization":"Bearer <token>"}' open <url>
```

---

## Form Automation Sequence

```bash
agent-browser snapshot -i                                    # get refs
agent-browser find label "Email" fill "user@example.com"
agent-browser find label "Password" fill "<password>"
agent-browser find role button click --name "Submit"
agent-browser wait --load networkidle
agent-browser screenshot --full step-complete.png           # evidence
```

---

## File Operations

```bash
# Upload
agent-browser find label "Attachment" upload ./file.pdf

# Download (triggers on button click)
agent-browser find role button click --name "Export CSV" download ~/report.csv

# Generate PDF
agent-browser pdf ./report.pdf
```

---

## Multi-Tab Flows

```bash
agent-browser tab new
agent-browser open <second-url>
agent-browser tab 0       # switch back to first tab
agent-browser tab list    # see all tabs
```

---

## Clipboard

```bash
# Read clipboard
agent-browser eval "navigator.clipboard.readText()"

# Write clipboard
agent-browser eval "navigator.clipboard.writeText('text')"

# Paste (macOS)
agent-browser keyboard inserttext "$(pbpaste)"
```

---

## Fine Mouse Control (canvas / Kanban / drawing apps)

```bash
agent-browser mouse move <x> <y>
agent-browser mouse down
agent-browser mouse move <x2> <y2>   # drag
agent-browser mouse up
```

---

## Recovery Patterns

| Failure | Recovery |
|:---|:---|
| Auth expired | `auth login <name>` re-authenticate |
| Auth Vault password changed | `auth delete <name>` then `auth save <name>` |
| Unexpected popup/dialog | `dialog dismiss` |
| `fill` overwrites existing content | Use `type` to append |
| Modal won't scroll | `scroll down --selector ".modal-body"` |

---

## Judgment Gates

Pause when:
- About to submit a form (show field values → ask confirm)
- About to trigger download / delete / payment
- Auth fails after retry (need fresh credentials)

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Auth Vault password changed | `auth delete` + `auth save` again — no in-place update |
| `fill` clears content unintentionally | Use `type` to append |
| File upload fails | Use `find label "..." upload <path>`, not `click` then `fill` |
| Can't scroll inside modal | `scroll down --selector ".modal-body"` |
