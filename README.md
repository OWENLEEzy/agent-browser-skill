# agent-browser skill

> CLI version: **0.14.0**

A Claude Code skill for browser automation via [`agent-browser`](https://www.npmjs.com/package/agent-browser) CLI.

**Use for:** E2E testing, web scraping with JS rendering, login flows, form automation, screenshots, debugging.

## Install skill

```bash
/skill install OWENLEEzy/agent-browser
```

## Requires

```bash
npm install -g agent-browser
agent-browser install   # download Chromium
```

## What it covers

- Full command reference (navigation, interaction, capture, debug, network mocking, storage, tabs)
- Semantic locators (`find role`, `find label`, `find text`...)
- Global options (`--headed`, `--cdp`, `--annotate`, `--profile`...)
- Common patterns: auth sessions, visual regression, file upload/download, device emulation
