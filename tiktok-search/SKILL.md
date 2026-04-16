---
name: TikTok Search
description: Professional TikTok keyword search and data extraction tool. Automates browsing and scraping via Chrome extension.
metadata: 
  openclaw:
    emoji: "🐷"
    os: ["darwin", "linux", "windows"]
    requires:
      bins: ["node", "npx"]
    command: ["npx", "-y", "@gecho-ai/gecho-bridge@latest"]
---

# TikTok Search 🚀

A specialized tool for searching and extracting video metadata from TikTok. It bridges your local Chrome browser via an extension to perform automated searches, scrolling, and data collection.

## Tools

### `tiktok_search_top_200`

Executes a keyword search, auto-scrolls to load results, and returns metadata.

**Parameters:**

- `query` (string, required): The search keyword or phrase (e.g., "cooking tips", "travel vlogs").
- `save_dir` (string, optional): Absolute path to save the results JSON.

**Returns:**

A JSON array containing video IDs, titles, like counts, play URLs, and author info.

## Runtime Requirements

1.  **Node.js**: Requires a local Node.js environment.
2.  **Gecho TikTok Extension**: Must have the corresponding Chrome extension installed and active.
3.  **Active Tab**: Ensure a browser tab is open and can access TikTok.

## Important Notes

- This is an **MCP tool**. AI should call the `tiktok_search_top_200` function.
- **NEVER** attempt to run this as a shell command like `gecho-bridge` or `npx ...`. 
- The bridge service starts automatically on the first call.
