# agent-browser-skill

> Skill version: **2.0.0** | agent-browser CLI: **0.18.0**

A [Claude Code](https://claude.ai/code) skill that teaches Claude how to drive browsers via the [`agent-browser`](https://www.npmjs.com/package/agent-browser) CLI.

## Skill Structure

This plugin ships **6 focused skills** instead of one large skill:

| Skill | When Claude loads it |
|:---|:---|
| `agent-browser` | When the task type is unclear or ambiguous |
| `agent-browser-e2e` | E2E testing, login flows, form submission, UI validation |
| `agent-browser-debug` | Investigating JS errors, broken buttons, failed API calls |
| `agent-browser-scrape` | Extracting data from JS-rendered pages, pagination, infinite scroll |
| `agent-browser-automate` | Form filling, file upload/download, session reuse, multi-step flows |
| `agent-browser-commands` | Looking up command syntax, global options, semantic locators |

Full command reference: use the `agent-browser-commands` skill.

---

## Installation

### Step 1 — Install the CLI

```bash
npm install -g agent-browser
agent-browser install        # download Chromium (first time only)
```

### Step 2 — Add marketplace & install skill

```bash
/plugin marketplace add OWENLEEzy/agent-browser-skill
/plugin install agent-browser-skill@agent-browser-skill
```

---

## Use cases

### E2E Testing
Automate full user journeys — login, form submission, cart checkout, redirect verification — with screenshot and video capture on failure.

```bash
agent-browser open https://app.example.com/signup
agent-browser snapshot -i
agent-browser find label "Email" fill "test@example.com"
agent-browser find role button click --name "Sign Up"
agent-browser wait "@welcome-banner"
agent-browser screenshot --full result.png
```

### Web Scraping
Extract data from JavaScript-rendered pages, handle pagination and infinite scroll, bypass cookie banners.

```bash
agent-browser open https://example.com/products
agent-browser wait --load networkidle
agent-browser eval "[...document.querySelectorAll('.product-card')].map(c => ({
  name: c.querySelector('h2')?.textContent,
  price: c.querySelector('.price')?.textContent
}))"
```

### Form Automation
Fill and submit complex forms — multi-step wizards, file uploads, dropdowns, date pickers.

```bash
agent-browser find label "Name" fill "John Doe"
agent-browser find label "Resume" upload ./resume.pdf
agent-browser find label "Country" select "Taiwan"
agent-browser find role button click --name "Submit"
```

### Visual Regression Testing
Catch unintended UI changes between deploys.

```bash
agent-browser screenshot --full baseline.png        # save baseline
# ... deploy changes ...
agent-browser diff screenshot --baseline baseline.png  # compare
```

### Debugging Web Apps
Inspect console errors, network requests, JavaScript state — without opening DevTools manually.

```bash
agent-browser --headed open https://app.example.com
agent-browser errors                                # JS errors
agent-browser console --pattern "error|warn"       # console logs
agent-browser network requests --filter "api"      # API calls
agent-browser eval "window.__APP_STATE__"          # read app state
```

### Auth-Protected Pages
Reuse login sessions across runs without re-authenticating every time.

```bash
# First time: login and save
agent-browser --profile ~/.myapp open https://app.example.com/login
agent-browser find label "Email" fill "me@example.com"
agent-browser find role button click --name "Sign In"
agent-browser wait "@dashboard"

# Future runs: already logged in
agent-browser --profile ~/.myapp open https://app.example.com/dashboard
```

### API Mocking / Edge Case Testing
Simulate network errors, empty responses, and slow APIs without touching the backend.

```bash
agent-browser network route "**/api/users**" --abort      # simulate outage
agent-browser network route "**/api/data**" --body '[]'   # empty response
agent-browser network unroute                              # restore
```

---

## Core workflow

```bash
agent-browser open https://example.com
agent-browser snapshot -i                    # accessibility tree → @e1, @e2...
agent-browser click @e3
agent-browser fill @e5 "user@example.com"
agent-browser screenshot --annotate          # AI-friendly labeled screenshot
```

> Re-run `snapshot` after any navigation — refs are invalidated.

---

## Requirements

- Node.js 18+
- `agent-browser` 0.18.0+ — `npm install -g agent-browser`
- Claude Code with plugin support
