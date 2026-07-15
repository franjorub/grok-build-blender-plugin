# Blender plugin for Grok Build

Connect [Grok Build](https://grok.x.ai/) to a running [Blender](https://www.blender.org/) instance through the official **Blender Lab MCP server**.

Installing this plugin registers the MCP automatically and ships a skill with bpy/scene best practices so the agent uses the tools safely.

## What you get

| Component | Purpose |
|-----------|---------|
| **MCP server `blender`** | stdio server from [lab/blender_mcp](https://projects.blender.org/lab/blender_mcp) (inspect scenes, run `bpy`, search API/manual, screenshots, renders) |
| **Skill `blender`** | When/how to use the tools, connection prerequisites, bpy safety rules |

---

## 1. Install the Grok Build plugin

### From GitHub (recommended)

```bash
grok plugin install franjorub/grok-build-blender-plugin --trust
```

### From a local clone

```bash
git clone https://github.com/franjorub/grok-build-blender-plugin.git
grok plugin install ./grok-build-blender-plugin --trust
```

### Enable the plugin

List the plugin under `[plugins].enabled` in `~/.grok/config.toml`:

```toml
[plugins]
enabled = [
  "blender",
  # ...your other plugins
]
```

Reload plugins in the TUI (`/plugins` → `r`) or start a new Grok session.

### Verify the plugin

```bash
grok plugin details blender
grok mcp doctor blender
```

In a session, open `/mcps` and confirm **blender** is listed and trusted.

You also need **[uv](https://docs.astral.sh/uv/)** on your `PATH` (the plugin launches the server with `uvx`) and **Python ≥ 3.10** (satisfied by `uv` as needed).

---

## 2. Install the official Blender MCP add-on

This Grok plugin only starts the **MCP server process**. To talk to a live Blender session you must install Blender Lab’s **MCP add-on** inside Blender.

Official page (Add-on section): **[https://www.blender.org/lab/mcp-server/](https://www.blender.org/lab/mcp-server/)**

### Drag and drop (recommended)

1. Open [blender.org/lab/mcp-server](https://www.blender.org/lab/mcp-server/) and find the **Add-on** section.
2. Use **Drag and Drop into Blender** and drop it onto a running Blender window.
3. **You must do the drag-and-drop twice:**
   - **First drop** — registers the **Blender Lab** extensions repository (source).
   - **Second drop** — installs the **MCP add-on** itself.
4. Doing it this way also lets Blender notify you when a new version of the add-on is available.

Alternatively, use **download** and then **Install from Disk** in Blender’s extensions UI (same page links both).

### Enable and confirm it is running

1. In Blender: **Edit → Preferences → Add-ons**.
2. Search for **MCP** (or open the add-on list and expand **MCP**).
3. Ensure the checkbox is **enabled**.
4. You should see something like:
   - Description: *MCP server add-on for LLM interaction.*
   - Maintainer: **Blender Lab**
   - Version: **1.0.0** (or newer)
   - **Preferences → Host:** `localhost`
   - **Preferences → Port:** `9876` (default)

Leave Blender open with the add-on enabled while you use Grok. The MCP process talks to Blender over this bridge; if Blender is closed or the add-on is disabled, tools will fail.

### Tested with

| Component | Version |
|-----------|---------|
| Blender | **5.1.2** |
| MCP add-on (Blender Lab) | **1.0.0** |
| Grok plugin MCP | `uvx` from [lab/blender_mcp](https://projects.blender.org/lab/blender_mcp) (`mcp/` package) |

---

## Migrating from a manual `config.toml` entry

If you previously added something like:

```toml
[mcp_servers.blender]
command = "uv"
args = ["run", "--directory", "/path/to/blender_mcp", "blender-mcp"]
enabled = true
```

**remove that entire block** after installing this plugin. User `config.toml` takes precedence and will hide the plugin’s MCP if both define `blender`.

You can keep any local `mcp-bundles` checkout as a backup; the plugin does not use it.

---

## First-run notes

- The first launch runs  
  `uvx --from 'git+https://projects.blender.org/lab/blender_mcp.git#subdirectory=mcp' blender-mcp`  
  which clones the monorepo, builds the `mcp/` package, and installs dependencies. That can exceed the default MCP startup timeout.
- If the server fails to start on first use, raise the timeout, for example:

  ```bash
  export GROK_MCP_STARTUP_TIMEOUT_SECS=60
  # or (milliseconds, Claude-compatible):
  export MCP_TIMEOUT=60000
  ```

- Stderr from the process is written to `~/.grok/logs/mcp/blender.stderr.log`.

---

## Usage

With Blender open and the MCP add-on enabled, ask Grok naturally, for example:

- “List the objects in the current Blender scene.”
- “Rename datablocks that look mistyped.”
- “Which objects use material X?”
- “Take a viewport screenshot and describe the layout.”

Prefer high-level MCP tools (summaries, docs search, screenshots) before free-form `execute_blender_code`.

---

## Troubleshooting

| Problem | What to try |
|---------|-------------|
| Plugin installed but no MCP | Ensure `"blender"` is in `[plugins].enabled`; reload plugins; remove a manual `[mcp_servers.blender]` if present |
| `uvx` / command not found | Install [uv](https://docs.astral.sh/uv/) |
| Startup timeout | `GROK_MCP_STARTUP_TIMEOUT_SECS=60` (see above); wait for first download to finish |
| Tools fail while server runs | Blender open? MCP add-on checked in **Edit → Preferences → Add-ons**? Host/port `localhost` / `9876`? Did you drag-and-drop **twice** on the Lab page? |
| Logs | `tail -f ~/.grok/logs/mcp/blender.stderr.log` |

---

## How the MCP is launched

```json
{
  "mcpServers": {
    "blender": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://projects.blender.org/lab/blender_mcp.git#subdirectory=mcp",
        "blender-mcp"
      ]
    }
  }
}
```

This matches the official package entrypoint (`blender-mcp`) without hard-coding machine-local paths.

> **Note:** There is an unrelated PyPI project also named `blender-mcp`. This plugin intentionally installs **only** from Blender Lab’s git repository.

---

## License

- **This repository** (Grok plugin wrapper, skill, docs): [MIT](./LICENSE).
- **Upstream MCP server** ([lab/blender_mcp](https://projects.blender.org/lab/blender_mcp)): GPL-3.0-or-later (fetched at runtime by `uvx`, not vendored here).

## Credits

- [Blender Lab MCP server](https://www.blender.org/lab/mcp-server/)
- [Grok Build plugins](https://github.com/xai-org) — install via `grok plugin install`
