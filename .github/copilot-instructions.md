# Copilot instructions for this ZMK repo

This repository contains a ZMK user configuration for a Sofle split keyboard.

## What to keep in mind
- Edit the keymap in config/sofle.keymap, which is Devicetree source, not C.
- Preserve the existing layer indices and custom behavior names.
- Keep the board target as nice_nano_v2.
- Keep ZMK pinned to v0.3 unless there is a strong reason to change it.
- Do not touch the local .zmk/ directory.
- keymap.yaml and images/keymap/*.svg are generated from the keymap with keymap-drawer; if you change custom behaviors, update raw_binding_map in keymap_drawer.config.yaml and regenerate them.

## Important conventions
- Layers: BASE 0, LOWER 1, RAISE 2, NUMPAD 3, ADJUST 4, GAMING 5 (reference layers by name, never by number).
- Existing custom behaviors include hml, hmr, ntilde_ht, and tp_gravehome.
- Studio is enabled only for the left half in build.yaml.

## Preferred change style
- Make small, targeted changes.
- If a layer or behavior is modified, update all references consistently.
- Prefer preserving current layout and behavior unless the user explicitly asks for a change.
