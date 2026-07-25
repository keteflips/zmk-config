# Copilot instructions for this ZMK repo

This repository contains a ZMK user configuration for a Sofle split keyboard.

## What to keep in mind
- Edit the keymap in config/sofle.keymap, which is Devicetree source, not C.
- Preserve the existing layer indices and custom behavior names.
- Keep the board target as nice_nano_v2.
- Keep ZMK pinned to v0.3 unless there is a strong reason to change it.
- Do not touch the local .zmk/ directory.

## Important conventions
- Layer 0 = BASE, 1 = LOWER, 2 = RAISE, 3 = ADJUST, and layer 4 is the gaming layer.
- Existing custom behaviors include hm, td_capslock, and td_ntilde.
- Studio is enabled only for the left half in build.yaml.

## Preferred change style
- Make small, targeted changes.
- If a layer or behavior is modified, update all references consistently.
- Prefer preserving current layout and behavior unless the user explicitly asks for a change.
