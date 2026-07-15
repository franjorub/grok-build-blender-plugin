# Blender plugin for Grok Build

Connect [Grok Build](https://grok.x.ai/) to a running [Blender](https://www.blender.org/) instance through the official **Blender Lab MCP server**.

Installing this plugin registers the MCP automatically and ships a skill with bpy/scene best practices so the agent uses the tools safely.

## What you get

| Component | Purpose |
|-----------|---------|
| **MCP server `blender`** | stdio server from [lab/blender_mcp](https://projects.blender.org/lab/blender_mcp) (inspect scenes, run `bpy`, search API/manual, screenshots, renders) |
| **Skill `blender`** | When/how to use the tools, connection prerequisites, bpy safety rules |

## Prerequisites

1. **[uv](https://docs.astral.sh/uv/)** installed and on your `PATH` (the plugin launches the server with `uvx`).
2. **Python ≥ 3.10** (satisfied by `uv` as needed).
3. **Blender** with the Lab **MCP add-on** installed, enabled, and connected:
   - Add the Lab extensions repository: `https://lab.blender.org/`
   - Install and enable the MCP add-on  
   - Details: [Blender Lab — MCP Server](https://www.blender.org/lab/mcp-server/)

The MCP process talks to Blender over the add-on bridge. **Blender must be running** for tools to work.

## Install

### From GitHub

```bash
grok plugin install franjorub/grok-build-blender-plugin --trust
```

### From a local clone

```bash
git clone https://github.com/franjorub/grok-build-blender-plugin.git
grok plugin install ./grok-build-blender-plugin --trust
```

### Enable the plugin

Plugins are not always active until listed under `[plugins].enabled` in `~/.grok/config.toml`:

```toml
[plugins]
enabled = [
  "blender",
  # ...your other plugins
]
```

Reload plugins in the TUI (`/plugins` → `r`) or start a new Grok session.

### Verify

```bash
grok plugin details blender
grok mcp list
# optional connectivity check (Blender + add-on should be up):
grok mcp doctor blender
```

In a session, open `/mcps` and confirm **blender** is listed and trusted.

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

## First-run notes

- The first launch runs  
  `uvx --from git+https://projects.blender.org/lab/blender_mcp.git blender-mcp`  
  which clones the package and installs dependencies. That can exceed the default MCP startup timeout.
- If the server fails to start on first use, raise the timeout, for example:

  ```bash
  export GROK_MCP_STARTUP_TIMEOUT_SECS=60
  # or (milliseconds, Claude-compatible):
  export MCP_TIMEOUT=60000
  ```

- Stderr from the process is written to `~/.grok/logs/mcp/blender.stderr.log`.

## Usage

With Blender open and the MCP add-on connected, ask Grok naturally, for example:

- “List the objects in the current Blender scene.”
- “Rename datablocks that look mistyped.”
- “Which objects use material X?”
- “Take a viewport screenshot and describe the layout.”

Prefer high-level MCP tools (summaries, docs search, screenshots) before free-form `execute_blender_code`.

## Troubleshooting

| Problem | What to try |
|---------|-------------|
| Plugin installed but no MCP | Ensure `"blender"` is in `[plugins].enabled`; reload plugins; remove a manual `[mcp_servers.blender]` if present |
| `uvx` / command not found | Install [uv](https://docs.astral.sh/uv/) |
| Startup timeout | `GROK_MCP_STARTUP_TIMEOUT_SECS=60` (see above); wait for first download to finish |
| Tools fail while server runs | Open Blender; enable/connect the Lab MCP add-on |
| Logs | `tail -f ~/.grok/logs/mcp/blender.stderr.log` |

## How the MCP is launched

```json
{
  "mcpServers": {
    "blender": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://projects.blender.org/lab/blender_mcp.git",
        "blender-mcp"
      ]
    }
  }
}
```

This matches the official package entrypoint (`blender-mcp`) without hard-coding machine-local paths.

> **Note:** There is an unrelated PyPI project also named `blender-mcp`. This plugin intentionally installs **only** from Blender Lab’s git repository.

## License

- **This repository** (Grok plugin wrapper, skill, docs): [MIT](./LICENSE).
- **Upstream MCP server** ([lab/blender_mcp](https://projects.blender.org/lab/blender_mcp)): GPL-3.0-or-later (fetched at runtime by `uvx`, not vendored here).

## Credits

- [Blender Lab MCP server](https://www.blender.org/lab/mcp-server/)
- [Grok Build plugins](https://github.com/xai-org) — install via `grok plugin install`
