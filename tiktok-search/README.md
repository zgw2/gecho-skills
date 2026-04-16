# TikTok Search 🐷

An OpenClaw skill that allows AI agents to search TikTok videos and retrieve detailed metadata (ID, Title, Like Count, etc.) via a Chrome extension.

## How it works

1.  **Browser Bridge**: This skill uses `@gecho-ai/gecho-bridge` to communicate with your local Chrome browser.
2.  **Automated Scraping**: It triggers searches and auto-scrolls in the active TikTok tab to collect results.

## Prerequisites

- **Chrome Extension**: You must have the Gecho TikTok Extension installed and active.
- **Active Tab**: TikTok must be open in a Chrome tab for the tool to work.

## Installation via OpenClaw

```bash
openclaw skill install tiktok-search
```

## Core Features

- **Deep Search**: Fetches up to 200 results per query.
- **Zero Configuration**: Powered by `npx`, no manual bridge setup required.

## License

MIT
