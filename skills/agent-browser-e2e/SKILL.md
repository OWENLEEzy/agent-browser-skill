---
name: agent-browser-e2e
description: "[INTERNAL TEMPLATE] E2E verification execution protocol. Called by agent-browser orchestrator."
---

# E2E Verification — Execution Protocol

> **Internal template.** Entry point: `agent-browser` skill.

---

## Execution Sequence

```bash
# 1. Open and wait for stable state
AGENT_BROWSER_NATIVE=1 agent-browser open <url>
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle

# 2. Check for errors before any interaction
AGENT_BROWSER_NATIVE=1 agent-browser errors          # → judgment gate if critical errors found
AGENT_BROWSER_NATIVE=1 agent-browser snapshot -i

# 3. Execute test steps (prefer semantic locators)
AGENT_BROWSER_NATIVE=1 agent-browser find label "Email" fill "test@example.com"
AGENT_BROWSER_NATIVE=1 agent-browser find role button click --name "Sign In"

# 4. After every navigation: re-snapshot + re-wait
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
AGENT_BROWSER_NATIVE=1 agent-browser snapshot -i

# 5. Assert state
AGENT_BROWSER_NATIVE=1 agent-browser is visible @e3
AGENT_BROWSER_NATIVE=1 agent-browser is checked @e5
AGENT_BROWSER_NATIVE=1 agent-browser get text @e1

# 6. Capture evidence
AGENT_BROWSER_NATIVE=1 agent-browser screenshot --full result.png
```

---

## Recovery Patterns

| Failure | Recovery |
|:---|:---|
| Element ref stale | Re-run `snapshot`, relocate |
| Assertion content mismatch | Screenshot + **judgment gate** |
| Page not loading | `wait --load networkidle` + retry up to 3x |
| Staging cert error | `--ignore-https-errors` (never in production) |
| Slow page | `AGENT_BROWSER_DEFAULT_TIMEOUT=60000` |

---

## Judgment Gates

Pause and ask user when:
- JS errors found before test steps begin
- Expected content ≠ actual (may be a bug)
- Test step fails after all retries exhausted

---

## Device & Environment Variants

```bash
# Mobile viewport
AGENT_BROWSER_NATIVE=1 agent-browser set device "iPhone 15 Pro"

# Offline / PWA
AGENT_BROWSER_NATIVE=1 agent-browser set offline
AGENT_BROWSER_NATIVE=1 agent-browser reload
# ... assert offline state ...
AGENT_BROWSER_NATIVE=1 agent-browser set offline off   # ALWAYS restore

# Geo-aware
AGENT_BROWSER_NATIVE=1 agent-browser set geo 31.2304 121.4737
```

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| Assert before page loads | `wait --load networkidle` first |
| Stale refs after navigation | Re-run `snapshot` after every nav |
| `set offline` left on | Always `set offline off` after offline test |
| Staging cert error | `--ignore-https-errors` — never in production |
