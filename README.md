# agent-browser-skill

> Skill version: **3.0.0** | agent-browser CLI: **0.18.0**

A [Claude Code](https://claude.ai/code) skill that turns Claude into an **autonomous browser automation agent** via the [`agent-browser`](https://www.npmjs.com/package/agent-browser) CLI.

---

## Design Philosophy

Most browser automation skills are documentation mirrors — you still have to know what commands to run. This skill is different.

**You state a goal. Claude plans, executes, recovers, and reports.**

```
You: "验证登录功能是否正常，URL 是 https://app.example.com"
  ↓
Claude asks clarifying questions (one at a time)
  ↓
Claude presents an execution plan → you confirm
  ↓
Claude executes autonomously:
  - technical failures → auto-recover (retry, fallback engine, re-auth)
  - judgment calls    → pause and ask you
  ↓
Structured report: steps ✅❌⏭️, issues 🐛, screenshots 📸, next steps →
```

---

## How It Works

### Single entry point

Always invoke `agent-browser`. You never need to choose a sub-skill.

### 4-Phase Protocol

| Phase | What happens |
|:---|:---|
| **INTAKE** | Claude asks questions one at a time until fully clear |
| **PLAN** | Claude generates a numbered plan, waits for your confirmation |
| **EXECUTE** | Autonomous execution — auto-recovers technical failures |
| **REPORT** | Structured output: step results, issues found, evidence files |

### Auto-recovery (never interrupts you)

| Failure | Recovery |
|:---|:---|
| Stale element ref | Re-snapshot, relocate |
| Timeout | Increasing wait: 1s → 3s → 5s |
| lightpanda returns empty | Switch to `--native` Chrome |
| Auth expired | Re-authenticate via vault |
| Selector not found | Try semantic `find role` locator |

### Judgment gates (pauses for you)

Claude only stops to ask when **you** need to decide:
- Found a JS error — bug or known issue?
- Expected content ≠ actual — is this correct?
- About to submit / delete / pay — confirm?

---

## Skill Structure

One user-facing skill + internal execution templates:

| Skill | Role |
|:---|:---|
| `agent-browser` | **Entry point** — orchestrator, always use this |
| `agent-browser-e2e` | Internal template: E2E verification protocol |
| `agent-browser-debug` | Internal template: debugging triage protocol |
| `agent-browser-scrape` | Internal template: data extraction protocol |
| `agent-browser-automate` | Internal template: form/task automation protocol |
| `agent-browser-ios` | Internal template: iOS Simulator protocol |
| `agent-browser-commands` | Internal reference: full CLI command syntax |

The internal templates are loaded automatically by the orchestrator based on your goal. You don't invoke them directly.

---

## Example

```
You:    下载 Rich Sutton「The Bitter Lesson」为 PDF，存到 ~/Downloads/

Claude: 存到 ~/Downloads/bitter-lesson.pdf 可以吗？

You:    ok

Claude: 目标：下载 The Bitter Lesson PDF
        策略：导航原文页面，用 pdf 命令保存
        步骤：
          1. 打开 incompleteideas.net/IncIdeas/BitterLesson.html
          2. wait --load networkidle
          3. pdf ~/Downloads/bitter-lesson.pdf
        确认？

You:    ok

Claude: [执行中，自动恢复残留进程冲突]

        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        执行报告：下载 The Bitter Lesson PDF
        状态：✅ 完成
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        ✅ 1. 打开页面 → 成功（自动恢复：清除残留进程）
        ✅ 2. 等待稳定 → networkidle 完成
        ✅ 3. 保存 PDF → ~/Downloads/bitter-lesson.pdf（44KB）
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Supported Goals

| Tell Claude | Template used |
|:---|:---|
| 「验证这个功能是否正常」 | e2e |
| 「为什么登录按钮点不了」 | debug |
| 「帮我抓这个页面的数据」 | scrape |
| 「每天自动下载这份报告」 | automate |
| 「在 iPhone 上测试这个页面」 | ios |
| 复杂目标（如：登录 + 验证 + 截图） | automate + e2e 组合 |

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
