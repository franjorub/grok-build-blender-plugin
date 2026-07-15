---
name: blender
description: >
  Control a running Blender instance via the official Blender Lab MCP server.
  Use when the user asks about Blender scenes, bpy, meshes, materials, geometry
  nodes, rendering, viewport inspection, renaming datablocks, or 3D scene work
  that should run inside Blender.
---

# Blender MCP Skill

This plugin wires Grok Build to the [Blender Lab MCP server](https://www.blender.org/lab/mcp-server/). Tools talk to a **live Blender process** that has the MCP add-on enabled and connected.

## Prerequisites (user machine)

1. **[uv](https://docs.astral.sh/uv/)** on `PATH` (the MCP launches with `uvx`).
2. **Python ≥ 3.10** (pulled in by `uvx` as needed).
3. **Blender** running with the Lab **MCP add-on** installed and enabled:
   - Add the Lab extensions repo: `https://lab.blender.org/`
   - Install/enable the MCP add-on, then connect it (see [Blender Lab docs](https://www.blender.org/lab/mcp-server/)).

If Blender is closed or the add-on is disconnected, tools will fail. Do not invent scene state.

## How to work with the tools

1. Prefer **high-level tools** first: object/scene summaries, screenshots, doc search, jump-to-object.
2. Use `execute_blender_code` only when no higher-level tool covers the task.
3. After structural changes, re-inspect (summary or screenshot) before claiming success.

## bpy / Blender API rules

When generating or reviewing Blender Python:

- **Inspect before mutate.** Do not assume object names, selection, or active object.
- **Active vs selected are distinct.** Operators often need both; set them explicitly.
- **Mode matters.** Wrong mode fails or no-ops; switch with care and restore if needed.
- Prefer the **data API** (`bpy.data`, object attributes) for precise control; use `bpy.ops` for standard UI actions (add primitives, modifiers, origins).
- After edit-mode mesh work via **bmesh**, flush changes back to the mesh.
- Update the **dependency graph** before reading computed properties (world matrices, modifier results).
- Do not destructively delete or overwrite user work without confirmation when the request is ambiguous.

## Documentation tools

Use the MCP’s API/manual search tools when unsure about operator signatures, RNA properties, or workflow steps. Prefer those over guessing Blender version-specific behavior.

## Troubleshooting

| Symptom | What to check |
|---------|----------------|
| MCP fails to start | `uv`/`uvx` on PATH; first run downloads the git package (slow) |
| Startup timeout | Raise cold-start allowance: `export GROK_MCP_STARTUP_TIMEOUT_SECS=60` or `MCP_TIMEOUT` (ms) |
| Tools error / connection | Blender open? Add-on enabled and connected? |
| Server stderr | `~/.grok/logs/mcp/blender.stderr.log` |

## Scope

- Interact with the **currently open** Blender file/session.
- Do not claim to install Blender or the add-on yourself unless the user asks for setup guidance; point them to the Lab docs.
