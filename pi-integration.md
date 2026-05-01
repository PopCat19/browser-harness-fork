# pi-coding-agent Integration

Purpose: Experimental pi extension for browser-harness — direct browser control via CDP from within pi.

## Architecture

```
pi (TypeScript extension)
  ├── browser_navigate(url)         → new tab + goto URL
  ├── browser_screenshot(path?, f)  → capture viewport PNG
  ├── browser_click(x, y, btn?)     → compositor-level mouse click
  ├── browser_type(text)            → insert text at focus
  ├── browser_press_key(key, mod?)  → dispatch keyboard event
  ├── browser_scroll(x, y, dy?)     → mouse wheel scroll
  ├── browser_js(expression)        → evaluate JS in page
  ├── browser_page_info()           → {url, title, viewport, scroll}
  ├── browser_list_tabs()           → list browser tabs
  ├── browser_switch_tab(targetId)  → switch agent to tab
  ├── browser_wait(seconds)         → sleep N seconds
  ├── browser_wait_for_load(timeout)→ poll readyState
  └── browser_restart_daemon()      → LLM self-heal
         │
         │ Unix socket /tmp/bu-default.sock
         │ JSON-line protocol
         ▼
   browser_harness daemon (Python)
         │
         │ CDP WebSocket
         ▼
   Chrome / Chromium (user's browser)
```

## Install

```bash
# 1. Install Python daemon
nix-shell -p uv --run "cd ~/browser-harness-fork && uv tool install -e ."
export PATH="$HOME/.local/bin:$PATH"

# 2. Extension auto-discovered from ~/.pi/agent/extensions/browser-harness/
# Symlink exists: ~/browser-harness-fork/pi-extension → ~/.pi/agent/extensions/browser-harness

# 3. Skill registered
# ~/.pi/agent/skills/browser-harness/SKILL.md → ~/browser-harness-fork/SKILL.md
```

## Usage After Setup

Pi auto-discovers the extension on startup. The tools appear in the available-tools list. Start Chrome with remote debugging enabled:

```bash
chromium --remote-debugging-port=9222
# or with Google Chrome:
google-chrome-stable --remote-debugging-port=9222
```

Then in pi, the LLM can invoke:
```
browser_navigate("https://github.com")
browser_screenshot()
browser_click(x=480, y=320)
```

## Extension Files

Located at `~/.pi/agent/extensions/browser-harness/`:

- `index.ts` — entry point, registers 13 custom tools
- `daemon.ts` — socket communication + daemon lifecycle

## Design Constraints

- Tools mirror browser-harness SKILL.md conventions (screenshots first, coord clicks, compositor events)
- Extension talks directly to Python daemon socket — no bash CLI required
- Daemon auto-starts on first tool call, LLM can restart via `browser_restart_daemon`
- No manager layer, no framework — thin wrapper around CDP commands

## Upstream Compatibility

Pi extension files live in `~/.pi/agent/extensions/` (pi auto-discovery path), not in the fork's source tree. The fork references the extension via symlink (`pi-extension/`). No merge conflicts with upstream.

## Status

- [x] Fork created at ~/browser-harness-fork
- [x] Dev branch set as default
- [x] Upstream remote configured
- [x] Python daemon installed (uv tool install -e .)
- [x] Skill symlinked to pi
- [x] Extension built (13 tools)
- [ ] Smoke test with running Chrome
