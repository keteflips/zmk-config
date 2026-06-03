# AGENTS.md — ZMK user config repo (Sofle keyboard)

## Overview
This is a ZMK firmware **user configuration** repository. Firmware itself is pulled at build time via `config/west.yml`. There is **no local build toolchain** — all firmware builds happen in GitHub Actions.

## Directory layout
```
config/              # Self module (west.yml `self:` → `config/`)
  west.yml           #   Zephyr manifest; pulls ZMK from zmkfirmware/zmk:main
  sofle.conf         #   Kconfig for Sofle shield
  sofle.keymap       #   Devicetree keymap (not C!)
boards/              # Custom board/shield definitions (currently empty)
zephyr/module.yml    # Sets board_root to repo root
build.yaml           # GitHub Actions build matrix
```

## Key conventions

### Board naming (HWMv2)
Use `nice_nano@2//zmk`, **not** `nice_nano_v2`. This is the Zephyr 4.1 HWMv2 format. The suffix `@2//zmk` means "revision 2 from the zmk vendor namespace".

### Keymap format
Keymaps are **Devicetree source** (`.dtsi`-included, bindings as `<&kp A>`), not C. Edit `config/sofle.keymap`.

### build.yaml
Defines the GitHub Actions build matrix. Each firmware image (left, right, settings_reset) needs its own entry. The `sofle_left` entry includes the `zmk-studio` snippet and Studio Kconfig flags.

### .zmk/ directory
**Never touch `.zmk/`.** It's git-ignored and is the local `west` workspace (cloned ZMK source + modules). Used only if you need to do local `west build`, which is not required for normal editing.

## Adding a new shield/peripheral
1. Add board files under `boards/` if needed
2. Add an entry in `build.yaml` under `include:`
3. Each half of a split keyboard gets its own build entry

## CI
Builds trigger on push, PR, and manual `workflow_dispatch`. The reusable workflow lives at `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`. The archive naming convention is `zmk-build-{ref_name}-run-{run_number}`.
