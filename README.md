# nvim-flake

A reproducible Neovim configuration built as a Nix flake.

This repository provides a Nix flake that packages a Neovim configuration, development shells, and (optionally) Home Manager/NixOS integration so you can reproduce your Neovim environment across machines.

Highlights
- Reproducible Neovim config via Nix flakes
- Dev shells for plugin development or binaries used by LSP/formatters
- Home Manager / NixOS integration for system-level activation
- Easy overrides to customize plugins, lua modules, and tools

Prerequisites
- Nix with flakes support (Nix 2.4+ recommended; recent Nix channels or Nix 2.9+)
- A working knowledge of Nix flakes is helpful but not required
- Optional: NixOS or Home Manager for system-level activation

Quick overview
The flake exposes typical outputs:
- `devShell` — a development shell with tools used by the Neovim config (linters, formatters, language servers, etc.)
- `neovim` / `packages.neovim` — a packaged Neovim binary with runtime/plugin paths set
- `homeConfigurations.<user>.activationPackage` — (when configured) Home Manager activation package to apply the config on non-NixOS systems
- `nixosConfigurations.<host>` — NixOS system modules/activation packages (if configured)

Install / Run locally (without modifying system)
- Enter a development shell (contains runtimes, linters, etc.):
  nix develop .#devShell

- Run the packaged Neovim:
  nix run .#neovim

- Build the config artifacts:
  nix build

Home Manager (non-NixOS)
- Build and activate the Home Manager activation package:
  nix build .#homeConfigurations.<your-username>.activationPackage
  ./result/activate

NixOS
- Rebuild and switch using the flake:
  sudo nixos-rebuild switch --flake .#<your-hostname>

Customization
- Main entry points for customization:
  - `flake.nix` — top-level flake inputs and outputs
  - `modules/` or `nix/` (if present) — modular Nix modules for different parts of the config
  - `lua/` — Lua configuration files (Neovim runtime config)
  - `home.nix` or `home-manager/*` — Home Manager options if used
- Common customization approaches:
  - Override plugin lists or plugin manager options in the appropriate Lua module
  - Add or remove tools from `devShell` to control which binaries are available to LSPs and formatters
  - Use flake inputs to pin plugin manager versions or tools

Recommended workflow for edits
1. Fork and clone this repo.
2. Create a feature branch.
3. Edit the configuration files (Lua, flake inputs, Nix modules).
4. Test locally via `nix develop` and `nix run .#neovim`.
5. If using Home Manager or NixOS, build the activation package or run a dry-run before activating.

Tips & troubleshooting
- If flakes are disabled, enable them in `/etc/nix/nix.conf` or use a Nix release that has `experimental-features = nix-command flakes`.
- Use `nix flake lock --update-input <input>` to update specific pinned inputs.
- If a language server or tool is missing, add it to `devShell` or the relevant `packages` list and rebuild.
