---
name: agent-browser-commands
description: Use when looking up agent-browser command syntax or global options, or when unsure whether agent-browser is the right tool for a browser task
---

# Agent Browser — Command Reference & Overview

**agent-browser covers:** E2E testing, web scraping, form automation, debugging.
**Not agent-browser:** Simple HTTP requests (use curl), static HTML (use curl+cheerio), large test suites (use Playwright).

**Task type → skill to load:**

| Task | Load skill |
|:---|:---|
| Testing / Validation | `agent-browser-e2e` |
| Debugging | `agent-browser-debug` |
| Data Extraction | `agent-browser-scrape` |
| Automation / Forms | `agent-browser-automate` |

**Security (v0.15+):** `--domain-allowlist example.com` restricts to approved domains. Action confirmation enabled by default in interactive mode.

---

> For task-specific workflows, see the specialized skills above.

---

## Navigation

| Command | Description |
|:---|:---|
| `open <url>` | Navigate to URL (supports `chrome://` and `chrome-extension://`) |
| `back` / `forward` | Browser history |
| `reload` | Refresh page |
| `close` | Close browser |

---

## Discovery

| Command | Description |
|:---|:---|
| `snapshot` | Accessibility tree with refs |
| `snapshot -i` | Interactive elements only |
| `snapshot -c` | Compact (less noise) |
| `snapshot -d <n>` | Limit tree depth |
| `snapshot -s <sel>` | Scope to CSS selector |

---

## Interaction

| Command | Description |
|:---|:---|
| `click @e1` | Click element |
| `dblclick @e1` | Double-click element |
| `fill @e1 "text"` | Clear and fill input |
| `type @e1 "text"` | Type without clearing (appends) |
| `keyboard type "text"` | Real keystrokes, no selector needed |
| `keyboard inserttext "text"` | Insert text without key events |
| `press <key>` | Press key: `Enter`, `Tab`, `Control+a` |
| `focus @e1` | Focus element |
| `check @e1` / `uncheck @e1` | Checkbox/radio |
| `select @e1 "option"` | Dropdown selection |
| `hover @e1` | Mouse hover |
| `drag @e1 @e2` | Drag and drop |
| `upload @e1 <file>` | Upload file |
| `download @e1 <path>` | Download file by clicking element |
| `scroll down` | Scroll page (up/down/left/right) |
| `scroll down --selector ".modal"` | Scroll within a specific container |
| `scrollintoview @e1` | Scroll element into view |
| `dialog dismiss` | Dismiss an open alert/confirm/prompt dialog |

---

## Mouse (fine control)

| Command | Description |
|:---|:---|
| `mouse move <x> <y>` | Move mouse to coordinates |
| `mouse down [btn]` | Mouse button down |
| `mouse up [btn]` | Mouse button up |
| `mouse wheel <dy> [dx]` | Scroll wheel |

---

## Get Info

| Command | Description |
|:---|:---|
| `get text @e1` | Get element text |
| `get html @e1` | Get inner HTML |
| `get value @e1` | Get input value |
| `get attr @e1 href` | Get attribute |
| `get title` / `get url` | Page info |
| `get count "selector"` | Count matching elements |
| `get box @e1` | Bounding box (x, y, width, height) |
| `get styles @e1` | Computed styles |
| `get cdp-url` | CDP WebSocket URL for the active page |

---

## State Check

| Command | Description |
|:---|:---|
| `is visible @e1` | Check if visible |
| `is enabled @e1` | Check if enabled |
| `is checked @e1` | Check if checked |

---

## Waiting

| Command | Description |
|:---|:---|
| `wait @e1` | Wait for element to appear |
| `wait 1000` | Wait milliseconds |
| `wait --load networkidle` | Wait for network to be idle |

---

## Capture

| Command | Description |
|:---|:---|
| `screenshot [path]` | Take screenshot |
| `screenshot --full` | Full page screenshot |
| `screenshot --annotate` | Annotated screenshot with numbered labels |
| `pdf <path>` | Save as PDF |
| `record start <path>` | Start video recording (WebM) |
| `record stop` | Stop and save video |

---

## Diff & Visual Regression

| Command | Description |
|:---|:---|
| `diff snapshot` | Compare current vs last snapshot |
| `diff screenshot --baseline` | Compare screenshot vs baseline image |
| `diff url <u1> <u2>` | Compare two pages side by side |

---

## Debug

| Command | Description |
|:---|:---|
| `inspect` | Open Chrome DevTools (commands still work while open) |
| `console` | View console logs |
| `console --pattern "err"` | Filter console logs |
| `console --clear` | Clear console log buffer |
| `errors` | View JavaScript errors |
| `errors --clear` | Clear error buffer |
| `highlight <sel>` | Highlight element visually |
| `network requests` | View all network requests |
| `network requests --filter "api"` | Filter by URL pattern |
| `network requests --clear` | Clear network log |
| `trace start [path]` | Start Playwright trace recording |
| `trace stop` | Stop trace (open with Playwright Trace Viewer) |
| `profiler start [path]` | Start Chrome DevTools profiler |
| `profiler stop` | Stop profiler |
| `eval "JS code"` | Execute JavaScript |

---

## Network Mocking

| Command | Description |
|:---|:---|
| `network route "**/*.png" --abort` | Block matching requests |
| `network route "**/api/**" --body '{"ok":true}'` | Mock response body |
| `network unroute [url]` | Remove route (all if no url) |

---

## Storage

| Command | Description |
|:---|:---|
| `cookies` | View all cookies |
| `cookies set <name> <val> --domain example.com --secure --httpOnly` | Set cookie |
| `cookies clear` | Clear all cookies |
| `storage local` | View localStorage |
| `storage local get <key>` | Get localStorage key |
| `storage local set <key> <val>` | Set localStorage key |
| `storage local clear` | Clear localStorage |
| `storage session get <key>` | SessionStorage (same API) |

---

## Tabs

| Command | Description |
|:---|:---|
| `tab new` | Open new tab |
| `tab list` | List all tabs |
| `tab <n>` | Switch to tab by index |
| `tab close <n>` | Close tab by index |

---

## Sessions

| Command | Description |
|:---|:---|
| `session` | Show current session name |
| `session list` | List active sessions |

---

## Device / Emulation

| Command | Description |
|:---|:---|
| `set viewport <w> <h>` | Set viewport size |
| `set viewport <w> <h> <scale>` | Set viewport with device pixel ratio (e.g. `2` for Retina) |
| `set device "iPhone 15 Pro"` | Preset device emulation |
| `set geo <lat> <lng>` | Set geolocation |
| `set media dark` | Dark mode |
| `set media dark reduced-motion` | Dark + reduced motion |

---

## Setup

| Command | Description |
|:---|:---|
| `install` | Download Chromium (first time setup) |
| `install --with-deps` | Also install system dependencies (Linux) |

---

## Semantic Locators (find)

Preferred over CSS selectors — survives UI refactoring:

```bash
agent-browser find role button click --name "Submit"
agent-browser find role link click --name "Learn More"
agent-browser find text "Sign In" click
agent-browser find label "Email" fill "user@example.com"
agent-browser find placeholder "Search..." type "query"
agent-browser find testid "submit-button" click
agent-browser find alt "Company logo" click
agent-browser find first "button.primary" click
agent-browser find last "a.link" click
agent-browser find nth 2 ".card" hover     # 0-based index
```

---

## Global Options

| Option | Description |
|:---|:---|
| `--headed` | Show browser window; also via `AGENT_BROWSER_HEADED=1` |
| `--native` | Native Rust daemon (no Node.js/Playwright); also via `AGENT_BROWSER_NATIVE=1` |
| `--engine chrome\|lightpanda` | Browser engine; also via `AGENT_BROWSER_ENGINE` |
| `--cdp <port>` | Connect to Chrome via CDP |
| `--auto-connect` | Auto-discover running Chrome |
| `--profile <path>` | Persistent browser profile (auth reuse) |
| `--session <name>` | Isolated session |
| `--session-name <name>` | Auto-save/restore cookies & localStorage |
| `--state <path>` | Load storage state from JSON |
| `--download-path <path>` | Default download directory; also via `AGENT_BROWSER_DOWNLOAD_PATH` |
| `--headers '{"Authorization":"Bearer token"}'` | Auth headers |
| `--proxy "http://127.0.0.1:7890"` | Proxy server |
| `--color-scheme dark\|light` | Color scheme preference |
| `--ignore-https-errors` | Ignore TLS certificate errors |
| `--allow-file-access` | Allow file:// URL access |
| `--user-agent <ua>` | Custom User-Agent |
| `--json` | JSON output |
| `--full` / `-f` | Full page (screenshot) |
| `--annotate` | Annotated output |
| `--debug` | Debug output |
| `--domain-allowlist <domain>` | Restrict to approved domains (security) |
