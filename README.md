<!--
  SEO / discoverability
  Title: Grok Build Blender Plugin | Official Blender Lab MCP for Grok
  Keywords: grok build, grok plugin, blender mcp, blender lab, mcpb, bpy,
            model context protocol, blender 5, 3d ai, uvx blender-mcp,
            grok-build-blender-plugin, xai grok blender
  Topics: blender, mcp, grok, grok-build, bpy, 3d, llm, blender-lab, mcpb
-->

<div align="center">

# Grok Build × Blender MCP Plugin

### Control a **live Blender** session from **[Grok Build](https://grok.x.ai/)** using the official **[Blender Lab MCP](https://www.blender.org/lab/mcp-server/)** server

[![Plugin version](https://img.shields.io/badge/plugin-v1.0.0-111827?style=for-the-badge&labelColor=0a0a0a)](https://github.com/franjorub/grok-build-blender-plugin/releases)
[![MCPB](https://img.shields.io/badge/MCPB-v1.0.0-E87D0D?style=for-the-badge&logo=blender&logoColor=white)](https://projects.blender.org/lab/blender_mcp/releases)
[![Blender](https://img.shields.io/badge/Blender-5.1.2_tested-EA7600?style=for-the-badge&logo=blender&logoColor=white)](https://www.blender.org/)
[![MCP](https://img.shields.io/badge/MCP-stdio-38BDF8?style=for-the-badge)](https://modelcontextprotocol.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-22C55E?style=for-the-badge)](./LICENSE)
[![uv](https://img.shields.io/badge/runtime-uv_·_uvx-DE5FE9?style=for-the-badge)](https://docs.astral.sh/uv/)

<p>
  <a href="https://grok.x.ai/"><img src="assets/grok-logo.svg" alt="Grok" height="40" /></a>
  &nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://www.blender.org/"><img src="assets/blender-logo.svg" alt="Blender" height="52" /></a>
  &nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://modelcontextprotocol.io/"><img src="assets/mcp-mark.svg" alt="Model Context Protocol" height="52" /></a>
</p>

**One install** wires Grok Build to Blender Lab’s `blender-mcp` server + a production skill for safe `bpy` workflows.

[Install](#-quick-install) · [Blender add-on](#-blender-mcp-add-on-required) · [Usage](#-usage) · [Version policy](#-version-policy) · [Troubleshooting](#-troubleshooting)

</div>

---

## Why this plugin?

| | |
|:--|:--|
| **Native Grok plugin** | Install with `grok plugin install` — MCP + skill load together |
| **Official Blender Lab stack** | Uses [lab/blender_mcp](https://projects.blender.org/lab/blender_mcp), not a random PyPI clone |
| **Pinned releases** | Plugin **1.0.0** → MCPB / git tag **v1.0.0** (no silent `main` drift) |
| **Agent-ready skill** | Scene inspect first, high-level tools before free-form code |
| **Zero path hacks** | No machine-local `mcp-bundles` directories in config |

Perfect for: **AI-assisted 3D**, **bpy automation**, **scene QA**, **docs-aware modeling**, and **LLM ↔ Blender** workflows on Grok Build.

---

## Features

- **Scene intelligence** — object hierarchies, datablock summaries, missing files, linked libraries  
- **Live `bpy` execution** — run Python inside the connected Blender instance  
- **Docs in context** — search Blender Python API + user manual from the MCP  
- **Visual feedback** — screenshots, viewport / thumbnail renders  
- **Grok skill** — triggers, safety rules, and connection troubleshooting baked in  

---

## Architecture

```text
  Grok Build  ──stdio MCP──▶  blender-mcp (uvx @ v1.0.0)
                                    │
                               TCP bridge
                                    ▼
                         Blender + Lab MCP add-on
                              (localhost:9876)
```

| Layer | What it is |
|-------|------------|
| **This repo** | Grok plugin wrapper (MIT) — `.mcp.json` + skill + docs |
| **MCP server** | Official `blender-mcp` from Blender Lab (GPL-3.0-or-later, fetched by `uvx`) |
| **Add-on** | Blender Lab extension inside Blender ([lab page](https://www.blender.org/lab/mcp-server/)) |

---

## Prerequisites

| Requirement | Notes |
|-------------|--------|
| [**Grok Build**](https://grok.x.ai/) CLI | Plugin manager: `grok plugin …` |
| [**uv**](https://docs.astral.sh/uv/) | Provides `uvx` on `PATH` |
| **Python ≥ 3.10** | Resolved by `uv` as needed |
| [**Blender**](https://www.blender.org/) | Tested on **5.1.2** |
| **MCP add-on 1.0.0** | From [Blender Lab MCP Server](https://www.blender.org/lab/mcp-server/) |

---

## Quick install

### 1) Grok Build plugin

```bash
grok plugin install franjorub/grok-build-blender-plugin --trust
```

<details>
<summary><strong>Local clone</strong></summary>

```bash
git clone https://github.com/franjorub/grok-build-blender-plugin.git
cd grok-build-blender-plugin
grok plugin install . --trust
```

</details>

### 2) Enable the plugin

In `~/.grok/config.toml`:

```toml
[plugins]
enabled = [
  "blender",
  # …other plugins
]
```

Reload: `/plugins` → `r`, or start a new Grok session.

### 3) Verify

```bash
grok plugin details blender
grok mcp doctor blender
```

In a session, open `/mcps` and confirm **blender** is listed.

---

## Blender MCP add-on (required)

This plugin only starts the **MCP server process**. Blender still needs Lab’s **MCP add-on**.

**Official docs:** [https://www.blender.org/lab/mcp-server/](https://www.blender.org/lab/mcp-server/) → section **Add-on**

### Drag & drop (recommended)

1. Open the Lab page above.  
2. Use **Drag and Drop into Blender** on a running Blender window.  
3. **Do it twice:**
   - **1st drop** → registers the **Blender Lab** extensions repository  
   - **2nd drop** → installs the **MCP add-on**  
4. You get update notifications when Lab ships a new add-on version.

Alternatively: **download** → **Install from Disk** in Blender’s extensions UI.

### Confirm it is running

1. **Edit → Preferences → Add-ons**  
2. Find **MCP** (*MCP server add-on for LLM interaction*)  
3. Enable the checkbox  
4. Defaults:

| Preference | Typical value |
|------------|----------------|
| Host | `localhost` |
| Port | `9876` |
| Maintainer | Blender Lab |
| Version | **1.0.0** (for this plugin release) |

Keep Blender open with the add-on enabled while using Grok.

### Validated combo

| Component | Version |
|-----------|---------|
| Blender | **5.1.2** |
| MCP add-on | **1.0.0** |
| This plugin / MCPB pin | **1.0.0** / git `@v1.0.0` |

---

## Version policy

> **Plugin version = MCPB version.**  
> **`1.0.0` of this plugin installs Lab MCPB / `blender_mcp` release `v1.0.0`.**

- Releases catalog: [projects.blender.org/lab/blender_mcp/releases](https://projects.blender.org/lab/blender_mcp/releases)  
- Launch pin (see `.mcp.json`):

```text
git+https://projects.blender.org/lab/blender_mcp.git@v1.0.0#subdirectory=mcp
```

- When Lab publishes e.g. **1.1.0**, this repo should bump to **1.1.0** and pin `@v1.1.0`.  
- Until then, installs stay on the pinned tag — **no silent tracking of `main`**.  
- Keep the **Blender add-on** on the matching Lab version for client/server compatibility.

---

## Usage

With Blender open and the MCP add-on enabled, ask Grok naturally:

```text
List the objects in the current Blender scene.
```

```text
Which objects use material X?
```

```text
Take a viewport screenshot and describe the layout.
```

```text
Rename datablocks that look mistyped; report what changed.
```

Prefer high-level MCP tools (summaries, docs search, screenshots) before free-form `execute_blender_code`.

---

## Migrating from manual `config.toml`

If you previously had:

```toml
[mcp_servers.blender]
command = "uv"
args = ["run", "--directory", "/path/to/blender_mcp", "blender-mcp"]
enabled = true
```

**Delete that entire block** after installing this plugin. User `config.toml` overrides the plugin MCP if both define `blender`.

Local `mcp-bundles` folders can stay as backups; the plugin does not use them.

---

## First-run notes

- First launch runs:

  ```bash
  uvx --from 'git+https://projects.blender.org/lab/blender_mcp.git@v1.0.0#subdirectory=mcp' blender-mcp
  ```

  Cloning/building can exceed the default MCP startup timeout.

- Raise cold-start allowance if needed:

  ```bash
  export GROK_MCP_STARTUP_TIMEOUT_SECS=60
  # or (ms, Claude-compatible):
  export MCP_TIMEOUT=60000
  ```

- Server stderr: `~/.grok/logs/mcp/blender.stderr.log`

---

## How the MCP is launched

```json
{
  "mcpServers": {
    "blender": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://projects.blender.org/lab/blender_mcp.git@v1.0.0#subdirectory=mcp",
        "blender-mcp"
      ]
    }
  }
}
```

> **Note:** An unrelated project on PyPI is also named `blender-mcp`. This plugin installs **only** from [Blender Lab’s git repository](https://projects.blender.org/lab/blender_mcp).

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Plugin installed, no MCP | `"blender"` in `[plugins].enabled`; reload plugins; remove manual `[mcp_servers.blender]` |
| `uvx` not found | Install [uv](https://docs.astral.sh/uv/) |
| Startup timeout | `GROK_MCP_STARTUP_TIMEOUT_SECS=60`; wait for first download |
| Tools fail while server is up | Blender open? **Preferences → Add-ons → MCP** enabled? `localhost:9876`? Drag-and-drop done **twice**? |
| Logs | `tail -f ~/.grok/logs/mcp/blender.stderr.log` |

```bash
grok mcp doctor blender
```

---

## Project layout

```text
grok-build-blender-plugin/
├── .grok-plugin/plugin.json   # Grok manifest (name, version 1.0.0)
├── .mcp.json                  # stdio MCP → uvx @ v1.0.0
├── skills/blender/SKILL.md    # Agent skill
├── assets/                    # Brand logos
├── LICENSE                    # MIT (wrapper)
└── README.md
```

---

## License & credits

| Piece | License / credit |
|-------|------------------|
| This repository (plugin wrapper, skill, docs, assets) | [MIT](./LICENSE) |
| Upstream MCP server [`lab/blender_mcp`](https://projects.blender.org/lab/blender_mcp) | GPL-3.0-or-later (runtime via `uvx`, not vendored) |
| Blender logo | © Blender Foundation — used to refer to Blender; [blender.org](https://www.blender.org/) |
| Grok / xAI | Product names of xAI — not affiliated; [grok.x.ai](https://grok.x.ai/). Wordmark adapted from [worldvectorlogo / Grok](https://worldvectorlogo.com/logo/grok-3) for identification only. |

**Built for** [Grok Build](https://grok.x.ai/) · **Powered by** [Blender Lab MCP](https://www.blender.org/lab/mcp-server/) · **Protocol** [MCP](https://modelcontextprotocol.io/)

---

<div align="center">

**Star ⭐ this repo** if you build 3D with Grok + Blender.

[Report issues](https://github.com/franjorub/grok-build-blender-plugin/issues) · [Lab releases](https://projects.blender.org/lab/blender_mcp/releases) · [Install](#-quick-install)

<br />

<sub>Keywords: Grok Build plugin · Blender MCP · Blender Lab · MCPB · Model Context Protocol · bpy · Blender 5 · AI 3D · uvx blender-mcp · LLM Blender automation</sub>

</div>
