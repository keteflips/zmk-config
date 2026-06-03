# AGENTS.md — ZMK user config repo (Sofle keyboard)

## Overview
This is a ZMK firmware **user configuration** repository. Firmware is pulled at build time via `config/west.yml`. There is **no local build toolchain** — all builds happen in GitHub Actions.

## Directory layout
```
config/              # Self module (west.yml `self:` → `config/`)
  west.yml           #   Zephyr manifest; pulls ZMK from zmkfirmware/zmk:main
  sofle.conf         #   Kconfig for Sofle shield
  sofle.keymap       #   Devicetree keymap (not C!)
boards/shields/      # Custom shield definitions (currently only .gitkeep)
zephyr/module.yml    # Sets board_root to repo root
build.yaml           # GitHub Actions build matrix
```

## Key conventions

### Board naming (HWMv2)
Use `nice_nano@2//zmk`, **not** `nice_nano_v2`. This is the Zephyr 4.1 HWMv2 format. The suffix `@2//zmk` means "revision 2 from the zmk vendor namespace".

### Keymap format
Keymaps are **Devicetree source** (bindings as `<&kp A>`), not C. Edit `config/sofle.keymap`.

### ZMK Studio
Only the **left half** (`sofle_left`) gets the `zmk-studio` snippet. Studio locking (`CONFIG_ZMK_STUDIO_LOCKING`) is **disabled** because the keymap uses custom behaviors (home-row mods, tap-dance) that conflict with Studio's layer-locking mechanism. If you add Studio to the right half, the locking flag must match.

### Build matrix (build.yaml)
Each firmware image needs its own entry under `include:`. Split halves get separate entries. The build uses `cmake-args` for Kconfig overrides (e.g., `-DCONFIG_ZMK_STUDIO=y`).

### .zmk/ directory
**Never touch `.zmk/`.** It is git-ignored and is the local `west` workspace. It is only used for optional local `west build`, which is not required for normal editing.

## Adding a new shield/peripheral
1. Add board files under `boards/` if needed
2. Add an entry in `build.yaml` under `include:`
3. Each half of a split keyboard gets its own build entry

## CI
Builds trigger on push, PR, and `workflow_dispatch`. Reusable workflow: `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`. Archive naming: `zmk-build-{ref_name}-run-{run_number}`.
