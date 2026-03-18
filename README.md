# agent-browser-skill

> Skill version: **3.0.0** | agent-browser CLI: **0.18.0**

A [Claude Code](https://claude.ai/code) skill that turns Claude into an **autonomous browser automation agent** via the [`agent-browser`](https://www.npmjs.com/package/agent-browser) CLI.

---

## The Problem With Other Browser Skills

Most browser automation skills are documentation mirrors — a reformatted version of `--help`. You load the skill, then still have to figure out which commands to run, in what order, and what to do when something breaks.

**This skill takes a different approach: encode expert judgment, not documentation.**

The difference:

| Documentation skill | This skill |
|:---|:---|
| "Here are the commands" | "Here's the right sequence for your goal" |
| You decide what to run | Claude decides, you confirm the plan |
| You debug failures | Claude auto-recovers technical failures |
| No output structure | Structured report every time |

---

## Design Logic

### Decision 1: Single entry point

**Why not multiple skills (one per task type)?**

Choosing between `agent-browser-e2e`, `agent-browser-scrape`, `agent-browser-debug` requires you to already know what kind of task you have. But real goals don't fit neatly into one category — "verify checkout works" is E2E + auth automation + debugging if something breaks.

A single `agent-browser` skill takes your goal and figures out which workflow(s) apply. You don't choose; Claude routes.

---

### Decision 2: Ask first, then plan, then execute

**Why not just start running commands immediately?**

Executing without context wastes time and can cause harm (submitting forms, deleting data). The INTAKE phase collects just enough to build a correct plan. The PLAN phase makes that plan visible before anything runs — you can catch wrong assumptions before they become wrong actions.

The sequence: **understand → align → execute** — not **execute → fix → repeat**.

---

### Decision 3: Auto-recovery vs. judgment gates

**Why does Claude sometimes recover silently and sometimes stop to ask?**

Two categories of failure require different responses:

**Technical failures** have known solutions that don't require human judgment:
- Stale element ref → re-snapshot and relocate
- Timeout → wait longer and retry
- lightpanda returned empty → switch to native Chrome
- Auth expired → re-authenticate from vault

Claude handles these without interrupting you. You shouldn't need to know that `@e3` became `@e7` after a re-render.

**Judgment calls** have no correct answer without your input:
- Found a JS error — is this a known issue or a real bug?
- Page content doesn't match expected — is that intentional?
- About to submit a form — are these the right values?

These require a human decision. Claude pauses, shows the evidence, asks one question, then continues.

The rule: **if Claude can resolve it without new information from you, it does. If it can't, it asks once.**

---

### Decision 4: Internal templates

**Why do the other skills exist if users don't invoke them?**

Each execution type (E2E verification, debugging triage, data scraping, task automation) has its own ordered protocol, recovery patterns, and judgment gates. Encoding all of this in one file would be unmanageable.

The templates are modular execution protocols — the orchestrator assembles the right ones for your goal and loads them via the `Skill` tool. You get the right protocol without having to know it exists.

---

### Decision 5: Structured report every time

**Why a fixed report format?**

Ad-hoc summaries are hard to scan and easy to skip. A consistent report structure means you always know where to look: steps at the top, issues in the middle, evidence files and next steps at the bottom. It also means Claude can't quietly skip reporting a failure.

---

## How It Works

### 4-Phase Protocol

| Phase | What happens |
|:---|:---|
| **INTAKE** | Claude asks questions one at a time until fully clear |
| **PLAN** | Claude generates a numbered plan, waits for your confirmation |
| **EXECUTE** | Autonomous execution — auto-recovers technical failures, pauses at judgment calls |
| **REPORT** | Structured output: step results, issues found, evidence files, next steps |

### Auto-recovery (never interrupts you)

| Failure | Recovery |
|:---|:---|
| Stale element ref | Re-snapshot, relocate |
| Timeout | Increasing wait: 1s → 3s → 5s |
| lightpanda returns empty | Switch to `--native` Chrome |
| Auth expired | Re-authenticate via vault |
| Selector not found | Try semantic `find role` locator |

### Judgment gates (pauses for you)

Claude only stops to ask when the answer requires your judgment:
- Found a JS error — bug or known issue?
- Expected content ≠ actual — is this correct?
- About to submit / delete / pay — confirm?

---

## Skill Structure

| Skill | Role |
|:---|:---|
| `agent-browser` | **Entry point** — orchestrator, always use this |
| `agent-browser-e2e` | Internal template: E2E verification protocol |
| `agent-browser-debug` | Internal template: debugging triage protocol |
| `agent-browser-scrape` | Internal template: data extraction protocol |
| `agent-browser-automate` | Internal template: form/task automation protocol |
| `agent-browser-ios` | Internal template: iOS Simulator protocol |
| `agent-browser-commands` | Internal reference: full CLI command syntax |

---

## Example

```
You:    Download "The Bitter Lesson" by Rich Sutton as a PDF

Claude: Save to ~/Downloads/bitter-lesson.pdf?

You:    ok

Claude: Goal:     Download The Bitter Lesson as PDF
        Strategy: Navigate to the source page, save via pdf command
        Steps:
          1. Open incompleteideas.net/IncIdeas/BitterLesson.html → expected: page loads
          2. wait --load networkidle → expected: stable
          3. pdf ~/Downloads/bitter-lesson.pdf → expected: file created
        Done when: file exists and size > 10KB
        Confirm?

You:    ok

Claude: [auto-recovered: cleared stale browser process, retried step 1]

        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        Execution Report: Download The Bitter Lesson PDF
        Status: ✅ Done
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        ✅ 1. Open page → success (auto-recovered: cleared stale process)
        ✅ 2. Wait for stable → networkidle reached
        ✅ 3. Save PDF → ~/Downloads/bitter-lesson.pdf (44KB)

        Next steps:
          → open ~/Downloads/bitter-lesson.pdf
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Supported Goals

| Tell Claude | Template used |
|:---|:---|
| "Verify this feature works" | e2e |
| "Why is the login button broken" | debug |
| "Scrape this product listing" | scrape |
| "Automate this weekly download" | automate |
| "Test this on iPhone" | ios |
| Complex (login + verify + screenshot) | automate + e2e combined |

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

## Requirements

- Node.js 18+
- `agent-browser` 0.18.0+ — `npm install -g agent-browser`
- Claude Code with plugin support
