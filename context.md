# browser-harness-fork

Purpose: Experimental fork of browser-use/browser-harness with pi-coding-agent integration.

## Structure

- `src/browser_harness/` — protected core package (daemon, helpers, admin, ipc)
- `agent-workspace/` — agent-editable helpers and domain skills
- `interaction-skills/` — reusable UI mechanics (dialogs, tabs, dropdowns, etc.)
- `docs/` — setup images and documentation

## Files

- `SKILL.md` — day-to-day browser skill instructions for agents
- `install.md` — first-time install and browser bootstrap
- `README.md` — project overview and quick start
- `pyproject.toml` — Python package config, dependencies, entry points

## Upstream Sync

- `upstream` remote → browser-use/browser-harness
- `main` branch → tracks upstream/main (readonly, for clean merges)
- `dev` branch → experimental work, default branch

## Vocabulary

- **Daemon** — CDP websocket holder + IPC relay (one per BU_NAME)
- **CDP** — Chrome DevTools Protocol
- **Domain Skill** — site-specific workflow knowledge (agent-generated)
- **Interaction Skill** — reusable UI mechanic (tabs, dialogs, uploads)
- **Helper** — Python function exposed to agent code execution