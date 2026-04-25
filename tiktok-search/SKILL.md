---
name: searching-tiktok
description: Searches TikTok for keywords and extracts video metadata (IDs, titles, likes, authors) via Chrome extension. Use when you need to gather TikTok search results, analyze trending videos, or extract data for specific keywords.
metadata: 
  openclaw:
    emoji: "🐷"
    os: ["darwin", "linux", "windows"]
    requires:
      bins: ["node", "npx"]
    command: ["npx", "-y", "@gecho-ai/gecho-bridge@latest"]
  hermes:
    type: "mcp"
    command: "npx -y @gecho-ai/gecho-bridge@latest"
    icon: "🐷"
  mcp:
    server:
      command: "npx"
      args: ["-y", "@gecho-ai/gecho-bridge@latest"]
---

# TikTok Search 🚀

Extracts video metadata from TikTok by bridging to a local Chrome browser extension.

## Capabilities

- Executes keyword searches on TikTok
- Auto-scrolls to load up to 200 results
- Extracts metadata: video IDs, titles, like counts, play URLs, and author info

## How to Use

**Service Initialization Strategy:**
Depending on your Agent framework (e.g., OpenClaw, HermesAgent, Claude Desktop), the bridge service may or may not start automatically. You must follow this strategy:

1. **Check Tool Availability**: First, check if the MCP tool `tiktok_search` is already available to you.
2. **If Available (e.g., OpenClaw)**: Call the tool directly. **NEVER** attempt to run this as a shell command (like `npx`). The bridge service starts automatically.
3. **If NOT Available (e.g., HermesAgent, Claude Desktop, Cline)**: 
   - **Attempt 1: Auto-Configuration via File & Restart**: 
     - **For Hermes Agent**: You MUST automatically add the MCP server configuration to the user's `~/.hermes/config.yaml` file. Use your file editing tools to append the following to the `mcp_servers` section:
       ```yaml
       mcp_servers:
         gecho_bridge:
           command: "npx"
           args: ["-y", "@gecho-ai/gecho-bridge@latest"]
       ```
       *(If the file or `mcp_servers` key doesn't exist, create it).*
     - **For Claude Desktop / Cline**: Proactively locate the config file (like `claude_desktop_config.json` or `.vscode/cline_mcp_settings.json`), read it, and inject the following under `mcpServers`:
       ```json
       "gecho-bridge": {
         "command": "npx",
         "args": ["-y", "@gecho-ai/gecho-bridge@latest"]
       }
       ```
     *Crucial:* After saving the configuration or running the command, you MUST inform the user: *"I have configured the TikTok Search tool. However, due to system limitations, **you must restart me (the Agent) or reload the MCP servers** for the changes to take effect."*
   - **Attempt 2: Background Process (If auto-config fails/unsupported)**:
     If you cannot find a configuration file, try starting the bridge service manually in a background/non-blocking terminal:
     ```bash
     npx -y @gecho-ai/gecho-bridge@latest
     ```
     Wait a few seconds for initialization, then check if `tiktok_search` becomes available. If it still doesn't, inform the user that their platform might not support dynamic tool loading.

### Prerequisites Check & Environment Setup
Before calling, ensure the following prerequisites are met. If the `node` or `npx` commands are missing, the AI should **proactively offer to install Node.js** for the user based on their OS:
- **Windows**: Suggest running `winget install OpenJS.NodeJS`
- **macOS**: Suggest running `brew install node`
- **Linux**: Suggest the appropriate package manager (e.g., `sudo apt install nodejs npm`)
*(Always ask for user permission before executing any installation commands.)*

1. **Node.js**: Installed in the local environment.
2. **Gecho TikTok Extension & Active Tab**: The **USER** must have Chrome open locally with the extension active and a TikTok tab open. 

**⚠️ CRITICAL AGENT INSTRUCTION:**
You (the Agent) MUST NOT attempt to install Chrome, open browsers, or use tools like `browser_navigate` to fulfill these prerequisites. Do NOT check for Chrome yourself. Your ONLY responsibility is to call the MCP tool.

## Input Format

**Tool Parameters:**
*(Note: Depending on your host Agent framework, the tool name might be prefixed. For example, in Hermes it is EXACTLY `mcp_gecho_bridge_tiktok_search`. Do NOT use `tiktok_insight` or any other tool variants).*
- `query` (string, required): The search keyword (e.g., "cooking tips", "travel vlogs").
- `save_dir` (string, optional): Absolute path to save the results JSON. *Best Practice: Always proactively generate a safe, timestamped absolute path in the current workspace (e.g., `/absolute/path/to/workspace/tiktok_travel_vlogs_1690000000.json`) so the user doesn't lose the raw data.*

## Output Format

A JSON array containing video metadata. Expected structure:
```json
[
  {
    "id": "7300000000000000000",
    "title": "Amazing travel vlog! ✈️ #travel",
    "likes": 150000,
    "play_url": "https://www.tiktok.com/@user/video/7300000000000000000",
    "author": {
      "nickname": "Traveler John",
      "unique_id": "traveler_john"
    }
  }
]
```

## Execution Rules & Constraints (CRITICAL)

You MUST strictly adhere to the following rules when calling the MCP tool:
1. **Strict Tool Binding (No Fallbacks)**: You MUST ONLY use the EXACT tool specified (e.g., `mcp_gecho_bridge_tiktok_search`) for TikTok searches. You are **STRICTLY FORBIDDEN** from using built-in browser tools (like `browser_navigate`, `puppeteer`, etc.), generic WebSearch, Bing, Google, or writing Python scrapers to visit TikTok.com. 
2. **Fail Fast & Explicit Reporting**: If the MCP tool fails, times out, or throws an error (e.g., `params is not defined`), you MUST STOP immediately. Do NOT try other MCP tools (like `tiktok_insight`). Do NOT offer alternative web search solutions. You MUST output the raw error message to the user.
3. **No Parallel Execution**: Since this tool controls an active Chrome tab, it is strictly single-threaded. You MUST NEVER execute multiple `tiktok_search` tool calls in parallel simultaneously. You must wait for one search to completely finish before starting another.
4. **Anti-Hallucination (No Fake Data)**: You MUST base your final response ONLY on the exact data returned by the tool. If the tool returns empty results (`[]`), you MUST NOT hallucinate or guess. Inform the user exactly what the tool returned.
5. **Anti-Spam (No Infinite Loops)**: NEVER call the tool repeatedly with the exact same `query` if it fails or returns empty results.
6. **Max Retries**: If a call fails due to a timeout or network error, you are allowed a MAXIMUM of 1 retry. If the retry also fails, STOP immediately.
7. **Rate Limiting**: Do not execute more than 3 distinct search queries in a single conversational turn unless the user explicitly commands it.
8. **Output Summarization (Avoid Chat Spam)**: If the tool returns a large number of results (e.g., 200 videos), DO NOT print the entire raw JSON array in your chat response. You must summarize the top 3-5 results, and utilize the `save_dir` parameter to save the full dataset to the user's disk.

## Troubleshooting & Error Handling (Decision Tree)

If the `tiktok_search` tool execution fails, follow this decision tree to assist the user:

1. **Error: "Chrome extension not found/connected"**
   - → Inform the user: *"Please ensure the Gecho TikTok Chrome extension is installed, enabled, and you have an active TikTok tab open in Chrome."*
2. **Error: "Timeout" or "No results found"**
   - → Ask the user: *"Are you currently facing a CAPTCHA or login prompt on the active TikTok page? Please resolve it in your browser and try again."*
3. **Error: "Tool not found" (after following initialization steps)**
   - → Inform the user: *"The bridge service failed to register the tool. Please check if port conflicts exist or try restarting the Agent."*

## Example Usage & Standard Operating Procedure (SOP)

When a user requests a TikTok search, follow this exact 5-step workflow:
1. **Pre-flight**: Silently verify the tool is available (initialize via the strategy above if needed).
2. **Determine Path**: Proactively generate a valid absolute path for `save_dir` based on the user's OS and current workspace.
3. **Execute**: Call `tiktok_search` with the `query` and `save_dir`.
4. **Process**: Wait for the JSON array.
5. **Report**: Inform the user where the raw JSON file was saved, and output a concise Markdown summary table (Title, Likes, Author, URL) for the top 3-5 videos only.

Example:
"Find trending videos for 'travel vlogs'"
→ Action: Call `tiktok_search` with `query="travel vlogs"` and `save_dir="/path/to/workspace/travel_vlogs_results.json"`

## Limitations

- Requires an active user session in Chrome.
- Only works via the MCP tool interface.
