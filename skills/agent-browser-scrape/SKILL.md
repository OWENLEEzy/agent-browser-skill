---
name: agent-browser-scrape
description: "[INTERNAL TEMPLATE] Data extraction execution protocol. Called by agent-browser orchestrator."
---

# Data Extraction — Execution Protocol

> **Internal template.** Entry point: `agent-browser` skill.

---

## Execution Sequence

```bash
# 1. Try lightpanda first (fastest, lightest)
AGENT_BROWSER_ENGINE=lightpanda agent-browser open <url>
AGENT_BROWSER_ENGINE=lightpanda agent-browser wait --load networkidle

# 2. Understand structure
AGENT_BROWSER_ENGINE=lightpanda agent-browser snapshot -c

# 3. Extract
AGENT_BROWSER_ENGINE=lightpanda agent-browser eval "[...document.querySelectorAll('.item')].map(el => ({
  name: el.querySelector('h2')?.textContent?.trim(),
  price: el.querySelector('.price')?.textContent?.trim()
}))"

# 4. If empty/wrong → fallback to native
AGENT_BROWSER_NATIVE=1 agent-browser open <url>
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
AGENT_BROWSER_NATIVE=1 agent-browser eval "<same query>"
```

---

## Pagination Handling

```bash
# Scroll-based infinite scroll
AGENT_BROWSER_NATIVE=1 agent-browser scroll down
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
AGENT_BROWSER_NATIVE=1 agent-browser eval "<extract again>"
# Repeat until no new items

# Click-based pagination
AGENT_BROWSER_NATIVE=1 agent-browser find role button click --name "Next"
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
```

---

## Anti-Bot Bypass

```bash
# Level 1: custom user-agent
agent-browser --user-agent "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" open <url>

# Level 2: disable automation detection (combine with level 1 for strongest)
agent-browser \
  --user-agent "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" \
  --args "--disable-blink-features=AutomationControlled" \
  open <url>
```

---

## Recovery Patterns

| Failure | Recovery |
|:---|:---|
| lightpanda returns empty | Switch to `--native` |
| Detected as bot | Add `--user-agent`, then combine with `--args` |
| `eval` before content loads | `wait --load networkidle` before every `eval` |
| Infinite scroll not handled | `scroll down` + re-`eval` loop |

---

## Judgment Gates

Pause when:
- Anti-bot measures block all approaches (CAPTCHA requires human)
- Data structure unclear after `snapshot -c` — show to user, ask how to identify items

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| lightpanda returns empty | Switch to `--native` |
| `eval` before load | `wait --load networkidle` first |
| Bot detected despite `--user-agent` | Add `--args "--disable-blink-features=AutomationControlled"` |
