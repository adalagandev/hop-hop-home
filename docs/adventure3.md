> **Historical doc.** This describes an early version of [adventure-plan](https://github.com/adalagandev/adventure-plan), the game Hop-Hop-Home was branched from. File names and rules here are outdated. See the [README](../README.md) for the current game.

# Adventure 3 — Themes, Modes & Sound

Kid-facing grid programming game. The player builds a step-plan, presses GO, and the hero executes it.

File: `index.html` (client-side only, no back-end).
Earlier versions: `adventure1.md` (mechanics prototype, `Grid Game.dc.html`), `adventure2-w-propetheme.md` (first themed version).

## Themes

| Theme | Hero | Goal | Obstacle | Tiles |
|---|---|---|---|---|
| Bunny Meadow | Nibbles the bunny | Burrow | Mossy rock | Grass |
| Turtle Cove | Shelly the turtle | Tide pool | Coral | Sand |
| Orca Passage | Kai the orca | Open bay | Iceberg | Deep water |

## Board modes

3×3 (1 obstacle), 4×4 (3), 5×5 (5, **default**), 6×6 (7). Cells resize per mode so the board keeps a consistent footprint; every board is 25% larger than v2.

## Rules

- Clicking Turn Left / Forward / Turn Right queues a step into the **step-plan** (single column, 6 slots max). The hero does not move until GO.
- **GO is locked until at least 3 steps are planned** — the button reads "plan 3+ steps" while locked, "GO!" when ready.
- Goal placement is BFS-validated: always reachable and **at least 3 moves from the start**.
- Hitting a wall or obstacle: the cell blinks red twice, the hero shakes, and it does not move.
- The goal cell pulses light green once per second.
- **Adventuring** = plan playback. Signboard and controls hide, the board scales up, the reset stays active, and each plan slot lights gold as its step runs.
- The plan clears when a run finishes.
- Reaching the goal: goal disappears → frame + hero shadow blink green → full-screen celebration (giant dancing hero, confetti, "YAY!") → auto reset to a fresh level.

## Sound

Synthesized in-browser (no audio files): plan-button blip, undo/clear blip, 4-note GO fanfare, rising step tone, low collision thud, win arpeggio. Toggleable in settings.

## UI layout

- Gear button pinned top-left opens a wooden settings panel: theme tabs, board sizes, sound toggle.
- Signboard (theme title + mission line) centered on top.
- Board with the controls merged into the same scaled panel; reset (⟲, light red) pinned to the board's top-right corner.
- Step-plan panel on the right: upright vertical "Adventure Plan" label, 6 slots, reset (⟲) pinned to its top-right corner.

## Tweakable props

- `theme` — meadow | cove | arctic
- `gridSize` — 3–6 (default 5)
- `facing` — up | right | down | left
- `soundOn` — boolean (default true)
- `showDevLabels` — boolean (default false)

## Element IDs

`game-root`, `sky`, `header`, `signboard`, `settings`, `btn-settings`, `settings-panel`, `btn-theme-meadow`, `btn-theme-cove`, `btn-theme-arctic`, `size-modes`, `btn-size-3`…`btn-size-6`, `btn-sound`, `stage`, `grid-frame`, `grid`, `cell-0`…, `hero-marker`, `btn-reset-board`, `plan-column`, `plan-panel`, `log-grid`, `btn-clear-log`, `controls`, `btn-turn-left`, `btn-forward`, `btn-turn-right`, `btn-start`, `celebration`

## Not yet built

- Soft obstacles (distinct from hard obstacles — behavior TBD).
- Loss condition / move-efficiency scoring.
- Hand-authored levels or progression.
- Persistent state across reloads.
