# Gecho Skills 🎭

This repository is a collection of skills for **OpenClaw (ClawHub)**. Each skill provides a specialized capability for AI agents by leveraging the **Gecho Bridge** and corresponding browser extensions.

## Skills Marketplace

| Skill | Description | Status |
| :--- | :--- | :--- |
| **[tiktok-search](./tiktok-search)** | Search and extract TikTok video metadata. | ✅ Stable |
| **[tiktok-insight](./tiktok-insight)** | Deep analysis and insights for TikTok data. | 🛠️ Planning |

## How to Install

You can install these skills directly via the **ClawHub** marketplace:

1. Open **OpenClaw**.
2. Go to the **Marketplace**.
3. Search for "Gecho" or the specific skill name.
4. Click **Install**.

## Architecture

These skills are lightweight metadata configurations that call the core service:
- **Service**: [gecho-bridge](https://github.com/gecho-ai/gecho-bridge) (via `npx @gecho-ai/gecho-bridge`)
- **Agent Integration**: Standard MCP (Model Context Protocol).

## License

MIT
