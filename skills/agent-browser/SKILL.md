---
name: agent-browser
description: Use when the browser automation task type is unclear or ambiguous — routes to the right specialized skill
---

# Agent Browser

> CLI version: **0.18.0** | Install: `npm install -g agent-browser && agent-browser install`

Fast browser automation CLI. Uses accessibility tree refs (`@e1`, `@e2`) instead of fragile CSS selectors.

**agent-browser covers:** E2E testing, web scraping, form automation, debugging — handled by specialized skills below.
**Not agent-browser:** Simple HTTP requests (use curl), static HTML (use curl+cheerio), large test suites (use Playwright).

---

## Step 1 — Identify Task Type

Load the matching specialized skill BEFORE starting:

| Task | Examples | Load skill |
|:---|:---|:---|
| **Testing / Validation** | "Does this form work?", "Verify redirect" | `agent-browser-e2e` |
| **Debugging** | "Why is this button disabled?", "Find the JS error" | `agent-browser-debug` |
| **Data Extraction** | "Scrape product prices", "Get all links" | `agent-browser-scrape` |
| **Automation** | "Fill and submit form", "Log in and download" | `agent-browser-automate` |

**Overlap rule:** Task feels like two types? Use the *starting point* type.
Example: "Test login, debug if it fails" → load `agent-browser-e2e` (debug steps are embedded inside it).

---

## Step 2 — Check Environment

```bash
printenv | grep AGENT_BROWSER
```

| Env var set | Skip flag |
|:---|:---|
| `AGENT_BROWSER_NATIVE=1` | `--native` |
| `AGENT_BROWSER_HEADED=1` | `--headed` |
| `AGENT_BROWSER_ENGINE=lightpanda` | `--engine lightpanda` |
| `AGENT_BROWSER_DOWNLOAD_PATH=...` | `--download-path` |
| `AGENT_BROWSER_ANNOTATE=1` | `--annotate` on screenshots |

---

## Security (v0.15+)

For production/shared environments:
- `--domain-allowlist example.com` — restrict to approved domains
- Action confirmation is enabled by default in interactive mode
- Output is auto-truncated for large pages

---

**Full command syntax:** use the `agent-browser-commands` skill.
