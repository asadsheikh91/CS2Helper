# CS2Helper

> **This project exists solely for security research and educational purposes.**
> It is not intended for use in live, competitive, or public matchmaking environments.

---

## What Is This?

CS2Helper is a proof-of-concept external overlay module for Counter-Strike 2, written in C#. It demonstrates how an external process can read game state and render an interactive settings menu on top of the game window — without injecting code into the game process itself.

The module showcases:

- **Overlay rendering** via SkiaSharp, drawn in a separate process
- **Real-time keyboard input handling** with configurable keybinds
- **Hierarchical menu system** — categories, items, sub-menus, sliders, toggles, keybind capture, and action buttons
- **Live config editing** — every setting is read/written through a `ConfigManager` and persisted to disk

### Features Demonstrated

| Category | Capability |
|---|---|
| **AimBot** | Toggle, auto-shoot, keybind, team check |
| **TriggerBot** | Toggle, keybind |
| **ESP** | Box ESP (name, health, distance, weapon, armor, visibility, flags), radar, aim crosshair with FOV circle |
| **Visuals** | Skeleton ESP, bomb timer, spectator list, vote teller |
| **Hit Sound** | On-hit text overlay with headshot detection |
| **General** | Rebindable menu key, save/reload/reset config |

---

## Why This Matters — A Message to Valve

**Valve, please pay attention to this module.**

This proof-of-concept was built to highlight a fundamental architectural weakness in CS2's anti-cheat posture: **external overlays that never touch the game process are trivially easy to build, and current protections do not adequately detect or prevent them.**

Key observations:

1. **No injection required.** This tool runs as a completely separate process. It reads publicly accessible game memory and draws on a transparent overlay window. Traditional anti-cheat signatures that scan for injected DLLs or hooked functions will not catch it.

2. **The overlay window is invisible to the game.** The rendering happens outside the game's rendering pipeline. Screenshot-based detection sees only the game framebuffer, not the overlay.

3. **Input interception is trivial.** Low-level keyboard hooks and Win32 `GetAsyncKeyState` polling work reliably even alongside anti-cheat — the menu in this repo demonstrates full keyboard-driven navigation with no special privileges.

4. **Configuration is user-friendly.** The menu system shown here is polished enough that a non-technical user could operate it. The barrier to entry for cheating is lower than it should be.

5. **External ESP and aimbot logic require only memory offsets.** As long as entity positions, health, team IDs, and view matrices are readable from outside the process, overlays like this will exist.

**This is not a new class of attack** — but the ease with which it can be reproduced in 2024/2025 suggests that the current mitigation strategy is insufficient. We urge Valve to invest in:

- Stronger memory-access protections (e.g., kernel-level read restrictions on game process memory)
- Overlay detection at the compositor level
- Behavioral analysis that flags suspicious input patterns regardless of source

The CS2 competitive community deserves a fair playing field. We publish this research to accelerate that goal, not to undermine it.

---

## Disclaimer

```
THIS SOFTWARE IS PROVIDED FOR EDUCATIONAL AND RESEARCH PURPOSES ONLY.

The author does not condone cheating in any online game. Using this code
or any derivative of it in live multiplayer matches violates Valve's
Terms of Service and may result in a permanent VAC ban.

The author assumes no responsibility for any misuse of this software.
By accessing this repository you agree that you will use the code only
for learning, security research, or authorized testing.
```

---

## Tech Stack

- **Language:** C# (.NET)
- **Rendering:** SkiaSharp (2D graphics)
- **Input:** Win32 low-level keyboard hooks + `GetAsyncKeyState` polling
- **Architecture:** External overlay — no process injection, no driver

## Repository Structure

```
Graphics/
  OverlayMenu.cs    — Interactive overlay menu (categories, toggles, sliders, keybinds)
```

This repository contains only the **overlay menu module**. The memory-reading, rendering pipeline, and config management components are not included — this is the UI surface that ties them together.

---

## License

This project is released under the [MIT License](LICENSE). Use responsibly.

---

<p align="center">
  <i>Built for research. Published for awareness. Intended for a fairer game.</i>
</p>
