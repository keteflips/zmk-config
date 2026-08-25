# AGENTS.md — ZMK user config repo (Sofle keyboard)

## Overview
This repository contains a ZMK user configuration for a Sofle split keyboard. The firmware is not built locally here; CI builds the images from GitHub Actions using the manifest in [config/west.yml](config/west.yml) and the matrix in [build.yaml](build.yaml).

## Important files
- [config/sofle.keymap](config/sofle.keymap): main keymap in Devicetree source format. This is not C code.
- [config/sofle.conf](config/sofle.conf): shared Kconfig options for all Sofle builds.
- [config/west.yml](config/west.yml): west manifest, including the ZMK pin and the external module for the OLED shield.
- [build.yaml](build.yaml): GitHub Actions build matrix.
- [zephyr/module.yml](zephyr/module.yml): Zephyr module configuration.

## Conventions to preserve
- Keep the board target as `nice_nano_v2`.
- Keep ZMK pinned to `v0.3` in [config/west.yml](config/west.yml) and the workflow reference used by CI.
- Treat the keymap as Devicetree source. Keep bindings in the `<&kp ...>` style.
- Preserve the existing layer structure. Layers are named via `#define` in [config/sofle.keymap](config/sofle.keymap): `BASE` 0, `LOWER` 1, `RAISE` 2, `NUMPAD` 3, `ADJUST` 4, `GAMING` 5. Always reference layers by these names (in `&to`, `&mo`, `&tog`, `&lt`, and `conditional_layers`), never by raw numbers. Adding or reordering layers requires updating the defines and every reference, plus the conditional layer target.
- Preserve the custom behaviors defined in [config/sofle.keymap](config/sofle.keymap): `hml`/`hmr` for home-row mods, the `ntilde_ht` hold-tap, and the tap-dance behaviors `td_shiftend` and `tp_gravehome`.
- Do not modify the local `.zmk/` workspace directory.

## Build and CI notes
- The left half uses Studio in [build.yaml](build.yaml); the related Kconfig options are commented out in [config/sofle.conf](config/sofle.conf).
- If adding a new shield or peripheral, update both [config/west.yml](config/west.yml) and [build.yaml](build.yaml).
- Split keyboards need one build entry per half.

## Agent guidance
When editing this repository, prefer minimal, targeted changes. If changing keybindings, keep behavior names and layer references consistent. If introducing new layers or new behaviors, update all relevant references in the keymap.
