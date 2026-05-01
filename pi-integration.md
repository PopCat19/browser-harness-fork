# pi-coding-agent Integration Plan

Purpose: Document experimental integration approach for pi-coding-agent while maintaining upstream compatibility.

## Integration Strategy

### 1. Skill Registration

Browser-harness uses `SKILL.md` as its agent skill file. For pi:

```bash
# Option A: Symlink (preferred for development)
mkdir -p ~/.pi/agent/skills/browser-harness
ln -sf ~/browser-harness-fork/SKILL.md ~/.pi/agent/skills/browser-harness/SKILL.md

# Option B: Copy (for stable releases)
cp ~/browser-harness-fork/SKILL.md ~/.pi/agent/skills/browser-harness/SKILL.md
```

### 2. Installation Variants

**Editable install (recommended for experimental fork):**
```bash
cd ~/browser-harness-fork
uv tool install -e .
```

**NixOS integration via flake:**
Add to `~/popcat19-nixos-hm` flake inputs or as a package overlay.

### 3. Upstream Compatibility Strategy

**Branch model:**
- `main` — tracks upstream/main (fast-forward only, no local commits)
- `dev` — experimental work (default branch)

**Sync workflow:**
```bash
# Fetch upstream changes
git fetch upstream

# Update main (clean tracking branch)
git checkout main
git merge --ff-only upstream/main

# Merge into dev
git checkout dev
git merge main
```

**Keep pi-specific changes isolated:**
- New files (no upstream conflicts)
- `pi-*` prefixed files (documentation, configs)
- `agent-workspace/` modifications (already fork-customizable)

### 4. Proposed pi-Specific Additions

**New files (no merge conflicts):**
- `pi-integration.md` — this plan document
- `pi-installer.nix` — optional NixOS module
- `.pi/agents/` — project-local pi agent configs (if needed)

**Modified files (track carefully):**
- `agent-workspace/agent_helpers.py` — pi-specific helper additions
- `install.md` — append pi installation section

### 5. Integration Points

**CDP via pi web_search/fetch_content:**
- pi already has web access tools via browser automation
- browser-harness provides lower-level CDP control
- Potential: pi could use browser-harness for complex DOM interactions

**Custom tools integration:**
- browser-harness could expose `browser-harness` CLI as a pi custom tool
- pi skills could invoke CDP commands via daemon socket

**Agent workspace sharing:**
- `BH_AGENT_WORKSPACE` can point to any directory
- pi could have its own `agent-workspace/` for domain skills

### 6. NixOS Service Module (Future)

**Concept for `pi-installer.nix`:**
```nix
# Potential home-manager module
{ config, lib, pkgs, ... }:
{
  options.services.browser-harness = {
    enable = lib.mkEnableOption "browser-harness daemon";
    package = lib.mkOption { type = lib.types.package; };
  };
  config = lib.mkIf config.services.browser-harness.enable {
    # daemon service definition
  };
}
```

### 7. Testing Strategy

1. Verify basic CDP connection to Chrome
2. Test skill registration with pi
3. Validate domain skill workflow
4. Confirm upstream merge works cleanly

## Status

- [x] Fork created at ~/browser-harness-fork
- [x] Dev branch set as default
- [x] Upstream remote configured
- [ ] Skill symlinked to pi
- [ ] Installation tested
- [ ] Upstream merge tested
- [ ] NixOS module (optional future)