---
name: agent-browser
description: Use when needing E2E testing, web scraping, browser automation, or solving tasks that require interacting with web pages via CLI
---

# Agent Browser

> CLI version: **0.14.0** | Install: `npm install -g agent-browser && agent-browser install`

Fast browser automation CLI for AI agents. Uses accessibility tree refs (`@e1`, `@e2`) instead of fragile CSS selectors.

**Use for:** E2E testing, web scraping with JS rendering, login flows, form automation, screenshots, debugging.
**Not for:** Simple HTTP requests (use curl), static HTML scraping (use curl+cheerio), complex multi-page test suites (use Playwright/TypeScript).

---

## Core Workflow

```bash
agent-browser open https://example.com   # 1. Navigate
agent-browser snapshot -i                # 2. Get refs (@e1, @e2...)
agent-browser click @e3                  # 3. Interact by ref
agent-browser fill @e5 "user@example.com"
```

Re-run `snapshot` after any page change — refs are invalidated.

---

## Full Command Reference

### Navigation
| Command | Description |
|:---|:---|
| `open <url>` | Navigate to URL |
| `back` / `forward` | Browser history |
| `reload` | Refresh page |
| `close` | Close browser |

### Discovery
| Command | Description |
|:---|:---|
| `snapshot` | Accessibility tree with refs |
| `snapshot -i` | Interactive elements only |
| `snapshot -c` | Compact (less noise) |
| `snapshot -d <n>` | Limit tree depth |
| `snapshot -s <sel>` | Scope to CSS selector |

### Interaction
| Command | Description |
|:---|:---|
| `click @e1` | Click element |
| `dblclick @e1` | Double-click element |
| `fill @e1 "text"` | Clear and fill input |
| `type @e1 "text"` | Type without clearing |
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
| `scrollintoview @e1` | Scroll element into view |

### Mouse (fine control)
| Command | Description |
|:---|:---|
| `mouse move <x> <y>` | Move mouse to coordinates |
| `mouse down [btn]` | Mouse button down |
| `mouse up [btn]` | Mouse button up |
| `mouse wheel <dy> [dx]` | Scroll wheel |

### Get Info
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

### State Check
| Command | Description |
|:---|:---|
| `is visible @e1` | Check if visible |
| `is enabled @e1` | Check if enabled |
| `is checked @e1` | Check if checked |

### Waiting
| Command | Description |
|:---|:---|
| `wait @e1` | Wait for element to appear |
| `wait 1000` | Wait milliseconds |
| `wait --load networkidle` | Wait for network to be idle |

### Capture
| Command | Description |
|:---|:---|
| `screenshot [path]` | Take screenshot |
| `screenshot --full` | Full page screenshot |
| `screenshot --annotate` | Annotated screenshot with numbered labels (great for AI vision!) |
| `pdf <path>` | Save as PDF |
| `record start <path>` | Start video recording (WebM) |
| `record stop` | Stop and save video |

### Diff & Visual Regression
| Command | Description |
|:---|:---|
| `diff snapshot` | Compare current vs last snapshot |
| `diff screenshot --baseline` | Compare screenshot vs baseline image |
| `diff url <u1> <u2>` | Compare two pages side by side |

### Debug
| Command | Description |
|:---|:---|
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

### Network Mocking
| Command | Description |
|:---|:---|
| `network route "**/*.png" --abort` | Block matching requests |
| `network route "**/api/**" --body '{"ok":true}'` | Mock response body |
| `network unroute [url]` | Remove route (all if no url) |

### Storage
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

### Tabs
| Command | Description |
|:---|:---|
| `tab new` | Open new tab |
| `tab list` | List all tabs |
| `tab <n>` | Switch to tab by index |
| `tab close <n>` | Close tab by index |

### Sessions
| Command | Description |
|:---|:---|
| `session` | Show current session name |
| `session list` | List active sessions |

### Setup
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
| `--headed` | Show browser window (debug mode) |
| `--cdp <port>` | Connect to Chrome via CDP |
| `--auto-connect` | Auto-discover running Chrome |
| `--profile <path>` | Persistent browser profile (auth reuse) |
| `--session <name>` | Isolated session |
| `--session-name <name>` | Auto-save/restore cookies & localStorage |
| `--state <path>` | Load storage state from JSON |
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

---

## Common Patterns

### Authenticated session (reuse login)
```bash
# First time: login and save state
agent-browser --profile ~/.myapp open https://app.example.com/login
agent-browser find label "Email" fill "me@example.com"
agent-browser find label "Password" fill "secret"
agent-browser find role button click --name "Sign In"
agent-browser wait "@dashboard"
# State is now saved in profile

# Future runs: already logged in
agent-browser --profile ~/.myapp open https://app.example.com/dashboard
```

### Connect to existing Chrome
```bash
# Launch Chrome with:  --remote-debugging-port=9222
agent-browser --cdp 9222 snapshot
agent-browser --auto-connect snapshot    # auto-discover
```

### AI-friendly screenshot
```bash
agent-browser screenshot --annotate page.png  # numbered labels + legend
```

### Wait for slow pages
```bash
agent-browser open https://example.com
agent-browser wait --load networkidle   # wait for network idle
agent-browser snapshot -i
```

### Visual regression diff
```bash
agent-browser screenshot --full baseline.png   # save baseline
# ... deploy changes ...
agent-browser diff screenshot --baseline baseline.png  # compare
```

### File upload / download
```bash
agent-browser find label "Attachment" upload ./file.pdf
agent-browser find role button click --name "Export" download ./export.csv
```

### Device emulation
```bash
agent-browser set viewport 375 812          # custom size
agent-browser set device "iPhone 15 Pro"   # preset device
agent-browser set geo 37.7749 -122.4194    # geolocation
agent-browser set media dark               # dark mode
agent-browser set media dark reduced-motion
```

### Debug why button is disabled
```bash
agent-browser --headed open https://app.example.com/form
agent-browser is enabled "@submit-button"   # false?
agent-browser eval "document.querySelector('form').checkValidity()"
agent-browser eval "[...document.querySelectorAll('input:invalid')].map(i => ({name: i.name, msg: i.validationMessage}))"
agent-browser console --pattern "error"
```

---

## Common Mistakes

| Mistake | Fix |
|:---|:---|
| Using CSS selectors like `.btn-x9z2` | Use `find role button --name Submit` |
| Not waiting for dynamic content | `wait @element` before interacting |
| Using stale refs after page change | Re-run `snapshot` after navigation |
| `type` vs `fill` confusion | `fill` clears first; `type` appends |
| Hardcoding `wait 5000` | Use `wait @selector` or `wait --load networkidle` |
| Can't see what's happening | Add `--headed` flag |
