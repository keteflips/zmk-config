# AGENTS.md — ZMK user config repo (Sofle keyboard)

## Overview
This is a ZMK firmware **user configuration** repository. Firmware is pulled at build time via `config/west.yml`. There is **no local build toolchain** — all builds happen in GitHub Actions.

## Directory layout
```
config/              # Self module (west.yml `self:` → `config/`)
  west.yml           #   Zephyr manifest; pulls ZMK + zmk-nice-oled
  sofle.conf         #   Kconfig for Sofle shield
  sofle.keymap       #   Devicetree keymap (not C!)
boards/shields/      # Custom shield definitions (currently only .gitkeep)
zephyr/module.yml    # Sets board_root to repo root
build.yaml           # GitHub Actions build matrix
```

## Key conventions

### Board naming
Use `nice_nano_v2` (legacy HWMv1 format). Do **not** use `nice_nano@2//zmk` (HWMv2).

### ZMK version
**Pinned to ZMK v0.3.** Both `config/west.yml` (`defaults.revision: v0.3`) and `.github/workflows/build.yml` (workflow ref `@v0.3`) reference this version. Changing one requires changing the other.

### nice_oled shield
The `zmk-nice-oled` external module (from `mctechnology17`) provides the `nice_oled` shield for OLED display support. Added as secondary shield in `build.yaml` (`sofle_left nice_oled`, `sofle_right nice_oled`). The module is declared in `config/west.yml` as a separate project and remote.

### Keymap format
Keymaps are **Devicetree source** (bindings as `<&kp A>`), not C. Edit `config/sofle.keymap`.

### Keymap layer numbering
The keymap has 5 layers (indices 0–4). `#define`s only cover BASE(0) through ADJUST(3); the `gaming` layer is **implicitly index 4**. Adding, removing, or reordering layers breaks `&to`/`&tog`/`&mo` references.

### Custom behaviors
Two custom behaviors defined: `hm` (home-row hold-tap mods) and `td_mt` (tap-dance for LSHIFT/CAPSLOCK/LCTRL). Conditional layers and the adjust activation rely on these. Do not remove them without updating all references.

### ZMK Studio
Only the **left half** gets Studio via `build.yaml` (snippet `studio-rpc-usb-uart`, cmake-args `-DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n`). Studio config is **commented out** in `sofle.conf`. Locking is disabled because custom behaviors conflict with Studio's layer-locking mechanism.

### sofle.conf scope
`config/sofle.conf` is included for **every** Sofle shield build (left, right, `settings_reset`). Per-half Kconfig differences belong in `build.yaml` `cmake-args`.

### Build matrix (build.yaml)
Each firmware image needs its own entry under `include:`. Split halves get separate entries. Secondary shields (e.g., `nice_oled`) are appended to the shield value: `sofle_left nice_oled`.

### .zmk/ directory
**Never touch `.zmk/`.** It is git-ignored and is the local `west` workspace. Only used for optional local `west build`.

## Adding a new shield/peripheral
1. Add board files under `boards/` if needed
2. Add any external module to `config/west.yml` if required
3. Add an entry in `build.yaml` under `include:`
4. Each half of a split keyboard gets its own build entry

## CI
Builds trigger on push, PR, and `workflow_dispatch`. Reusable workflow: `zmkfirmware/zmk/.github/workflows/build-user-config.yml@v0.3`. Archive naming: `zmk-build-{ref_name}-run-{run_number}`.
