---
name: agent-browser-scrape
description: Use when extracting data from JavaScript-rendered pages, handling pagination, or scraping dynamic content
---

# Agent Browser — Data Extraction / Scraping

**Recommended config:** Try `--engine lightpanda` first (lightest, fastest headless). Fall back to `--native` if page needs full Chrome.

---

## Environment Check

```bash
printenv | grep AGENT_BROWSER   # already set? Omit the corresponding inline flag
```

---

## Workflow

```bash
# 1. Try lightpanda first
AGENT_BROWSER_ENGINE=lightpanda agent-browser open https://example.com/products
AGENT_BROWSER_ENGINE=lightpanda agent-browser wait --load networkidle
AGENT_BROWSER_ENGINE=lightpanda agent-browser eval "[...document.querySelectorAll('.product-card')].map(c => ({
  name: c.querySelector('h2')?.textContent?.trim(),
  price: c.querySelector('.price')?.textContent?.trim()
}))"

# 2. If lightpanda returns empty/wrong data, switch to native:
AGENT_BROWSER_NATIVE=1 agent-browser open https://example.com/products
AGENT_BROWSER_NATIVE=1 agent-browser wait --load networkidle
AGENT_BROWSER_NATIVE=1 agent-browser eval "[...document.querySelectorAll('.product-card')].map(c => ({
  name: c.querySelector('h2')?.textContent?.trim(),
  price: c.querySelector('.price')?.textContent?.trim()
}))"
```

---

## Key Patterns

- `snapshot -c` (compact) to quickly find extractable DOM structure
- `scroll down` + `eval` loop for infinite scroll pages
- Always `wait --load networkidle` before `eval` on dynamic pages

---

## Pitfalls

| Mistake | Fix |
|:---|:---|
| lightpanda returns empty/wrong data | Switch to `--native` — page needs full Chrome JS engine |
| `eval` runs before content loads | `wait --load networkidle` before every `eval` |
| Infinite scroll not handled | `scroll down`, then re-`eval` in a loop |

---

## After Task Completion

**MANDATORY. Do not skip. There are no exceptions.**

### Step 1 — List pitfalls + lightpanda compatibility

Format each finding:

🕳️ **Pitfall:** [what happened]
   **Workaround:** [what fixed it]
   **In skill?** ✅ Yes — [section] / ❌ Missing

**Also report lightpanda status (always):**
- ✅ lightpanda succeeded — site: [domain]
- ❌ lightpanda failed → fell back to native — site: [domain], reason: [what broke]

This tracks compatibility over time so future runs skip lightpanda on known-incompatible sites.

If zero pitfalls: write "✅ No new pitfalls encountered."

### Step 2 — Ask the user

> 「任务完成。遇到 N 个坑，其中 X 个还没写进 skill。要更新吗？」

If yes → update Pitfalls + bump version. Then scan all other `agent-browser-*` skills for the same issue.

### Anti-rationalization

| Excuse | Reality |
|:---|:---|
| "No pitfalls encountered" | Write "✅ No new pitfalls" — still required |
| "I mentioned issues inline" | Inline ≠ structured retrospective |
| "lightpanda failure isn't a skill issue" | It's a compatibility data point — record it |
| "User can see what happened" | User needs a structured summary to decide on iteration |
| "Not sure if it's a skill issue" | If you worked around something, it's a pitfall |
