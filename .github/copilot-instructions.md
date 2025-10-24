# Copilot instructions — zmk-config

Purpose: Give a concise, actionable guide so an AI coding agent can be immediately productive in this ZMK configuration repository.

High level architecture (what to read first)
- This repo is a ZMK keyboard configuration overlay, not the full ZMK firmware. The repo bootstraps ZMK via a West manifest at `config/west.yml` which imports the upstream `zmk` project.
- Key areas:
  - `config/` — the active configuration shared by CI and local builds (board/shield defaults, `sofle.conf`, `sofle.keymap`, `west.yml`).
  - `boards/shields/<shield>/` — shield-specific artifacts: `.zmk.yml` metadata, `.conf` and `.overlay` files, and `*.keymap` (device-tree style keymap). Example: `boards/shields/sofle/sofle.zmk.yml` describes the Sofle shield features and siblings.
  - `zephyr/module.yml` — minimal Zephyr module metadata used by the build system.
  - `build.yaml` — GH Actions / CI matrix generator for building board+shield combinations. CI reads this file to create build matrix entries (see top comments inside `build.yaml`).

Important patterns and conventions (project-specific)
- Device-tree-style keymaps: keymaps are stored as `.keymap` and DTSI-like fragments (see `config/sofle.keymap`). They use ZMK DT bindings (e.g. `#include <dt-bindings/zmk/keys.h>`) and define layers inside a `keymap` node. Expect bindings like `&kp`, `&mo`, `&bt`, `&rgb_ug` and `sensor-bindings` in these files.
- Conditional-layer idiom: this repo uses the `conditional_layers` node to create an ADJUST layer when LOWER+RAISE are active (see `config/sofle.keymap` around `conditional_layers`).
- Shield metadata: each shield has a `.zmk.yml` describing `requires`, `exposes` and `features`. Use these files to understand hardware capabilities (I2C OLED, encoders, underglow, etc.).
- Config fragments: `*.conf` contain Kconfig-style `CONFIG_` entries to enable features (BLE, USB, RGB, STUDIO). Example: `config/sofle.conf` toggles `CONFIG_ZMK_STUDIO`, `CONFIG_ZMK_BLE`, RGB and sleep options.
- Naming: shields are organized under `boards/shields/<name>/` and are referenced in CI via `shield: <name>` and in build overlays via `snippet` / `cmake-args` in `build.yaml`.

Developer workflows (what CI and a developer expect)
- CI: `build.yaml` defines the board/shield matrix. To add a new build variant, edit `build.yaml` and add an `include:` entry with optional `cmake-args`, `snippet`, and `artifact-name` fields — CI will pick these up.
- Local dev (how to reproduce CI builds): this repo supplies a West manifest at `config/west.yml` that imports upstream ZMK. Reproduce CI builds by using the standard ZMK/Zephyr workflow: initialize West with the manifest, update projects, and build the app for the board target. (Confirm Zephyr SDK and west are installed in the environment before building.)
  - Files to start with: `config/west.yml` (manifest), `build.yaml` (matrix), `boards/shields/<shield>/*` (hardware overlays), and `config/*.conf` (Kconfig toggles).

Integration points and external dependencies
- Upstream ZMK is pulled via the West manifest in `config/west.yml` (project `zmk` from `zmkfirmware`). Changes to the ZMK module may affect local builds.
- CI snippets: `build.yaml` can reference Zephyr snippets such as `studio-rpc-usb-uart`. Those snippets are applied during CI builds — look at `build.yaml` comments and includes for examples.

What an AI agent should do first (reading order)
1. Read `config/west.yml` (understand what upstream is imported).
2. Open `build.yaml` to see which board+shield combinations CI is expecting.
3. Inspect `boards/shields/<shield>/*.zmk.yml`, `*.conf`, and `*.keymap` for the shield you will modify (e.g. `boards/shields/sofle/*`).
4. Read `config/sofle.conf` and `config/sofle.keymap` to learn repository-wide defaults and keymap patterns.

Concrete examples to reference while coding
- To add a new feature flag for the Sofle shield: edit `config/sofle.conf` (Kconfig-style `CONFIG_*` entries) and, if needed, add device-tree overlays under `boards/shields/sofle/`.
- To add a CI variant with ZMK Studio enabled: add an entry to `build.yaml` with `snippet: studio-rpc-usb-uart` (same pattern as the existing `nice_nano_v2 + sofle_left` entry).

Edge cases and gotchas (observed in repo)
- This repo provides configuration fragments but relies on the upstream ZMK project for core code. Changes to ZMK API or DT bindings upstream can break builds here.
- Keymap files are DT fragments — do not refactor them as plain C/TS/Python. Keep the ZMK DT syntax and `#include` style.

When you're editing: be conservative and reference the corresponding shield `.zmk.yml` and `config/*.conf` before changing behavior flags.

If anything above is unclear or you'd like me to add short example build commands (to reproduce CI locally), tell me which OS/Zephyr setup you have and I will add verified commands.
