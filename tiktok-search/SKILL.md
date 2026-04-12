---
name: TikTok Search
description: Professional TikTok keyword search and data extraction tool. Automates browsing and scraping via Chrome extension.
metadata: {"openclaw":{"emoji":"🐷","os":["darwin","linux","windows"],"requires":{"bins":["node","npx"]},"command":["npx","-y","@gecho-ai/gecho-bridge@latest"]}}
---

# TikTok Search 🚀

A specialized tool for searching and extracting video metadata from TikTok. It bridges your local Chrome browser via an extension to perform automated searches, scrolling, and data collection.

## Core Capabilities

| Tool | Description |
| ------ | ----------- |
| `tiktok_search_top_200` | Executes a keyword search, auto-scrolls to load at least 200 results, and returns the top 20 videos sorted by like count. |

### Usage
- **query**: The search keyword or phrase (e.g., "cooking tips", "travel vlogs").
- **Output**: Returns a JSON array containing video IDs, titles, like counts, play URLs, and author info.
- **Persistence**: Full search results (200+ entries) are automatically saved to your local `data/` directory for further analysis.

## Runtime Requirements
1.  **Node.js**: Requires a local Node.js environment.
2.  **Gecho TikTok Extension**: Must have the corresponding Chrome extension installed and active.
3.  **Active Tab**: Ensure a browser tab is open and can access TikTok.

## Important Notes
- This is a **read-only** tool for public metadata extraction.
- On the first run, `npx` will automatically download and start the bridge service.