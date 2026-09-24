# Zotero MCP Server for Claude

Connects a local Zotero library to Claude (desktop app / Cowork) through the [zotero-mcp](https://github.com/54yyyu/zotero-mcp) server, so Claude can search, read and edit your library.

Tested on Windows with Miniconda and Zotero 7+.

## 1. Enable Zotero's local API
In Zotero: **Settings → Advanced → General**, check
**"Allow other applications on this computer to communicate with Zotero"**.

Zotero must be running whenever Claude uses the server.

## 2. Install the server
Open an **Anaconda Prompt** (or any terminal where `pip` points to the Python you want to use).
```
pip install zotero-mcp-server
```
Optional: semantic (meaning-based) search over your library
```
pip install "zotero-mcp-server[semantic]"
```

Find where the executable was installed (you need the full path later)
```
where zotero-mcp
```
Example: `C:\Users\<USER>\miniconda3\Scripts\zotero-mcp.exe`

## 3. Configure Claude
Option A: let the tool write the Claude config for you
```
zotero-mcp setup
```

Option B: edit the config by hand. In the Claude desktop app go to **Settings → Developer → Edit Config** and add
```json
{
  "mcpServers": {
    "zotero": {
      "command": "C:\\Users\\<USER>\\miniconda3\\Scripts\\zotero-mcp.exe",
      "env": { "ZOTERO_LOCAL": "true" }
    }
  }
}
```
If the file already has an `"mcpServers"` block, only add the `"zotero"` entry inside it.

## 4. Build the semantic search index (optional)
Only needed if you installed the `[semantic]` extras.
```
zotero-mcp update-db
```
Check the index status
```
zotero-mcp db-status
```
The index is stored at `~/.config/zotero-mcp/chroma_db`.

## 5. Restart Claude
Fully quit Claude (right-click the tray icon → Quit), then reopen it.
**Settings → Developer** should list `zotero` as running. In Cowork, start a new task linked to your computer.

## 6. Allow write access (optional)
To let Claude edit items (fix metadata, add tags, move collections), approve the
**"Allow"/"Always Allow"** prompt that Zotero shows the first time Claude tries to write.
"Always Allow" saves a reusable local API key.

## Test it
Ask Claude:
```
Search my Zotero library for reinforcement learning
List my Zotero group libraries
```

## Troubleshooting
| Problem | Fix |
|---|---|
| Claude doesn't see any Zotero tools | Fully quit and reopen Claude; check **Settings → Developer** for errors |
| "Connection refused" / no results | Make sure Zotero is open and the local API setting is checked |
| `zotero-mcp` not found | Use the full path from `where zotero-mcp` in the config |
| Semantic search returns nothing | Run `zotero-mcp update-db` |
| Writes fail | Approve the write-access prompt in Zotero |

## Useful commands
```
zotero-mcp --help          # list all commands
zotero-mcp setup           # (re)write the Claude config
zotero-mcp update-db       # rebuild the semantic index
pip install -U zotero-mcp-server   # update the server
```
