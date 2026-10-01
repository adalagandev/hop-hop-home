> **Historical doc.** This describes an early version of [adventure-plan](https://github.com/adalagandev/adventure-plan), the game Hop-Hop-Home was branched from. File names and rules here are outdated. See the [README](../README.md) for the current game.

# Adventure 2 — Themed & Kid-Friendly

Cute, kid-facing version of the grid adventure game. Two swappable animal themes, original chunky-outlined 2D art (soft sky, drifting clouds, wooden signboards, 3D press buttons).

File: `index.html` (no back-end, runs entirely client-side).
Previous mechanics-only prototype: `Grid Game.dc.html` (see `adventure1.md`).

## Themes

| Theme | Hero | Goal | Obstacle | Tiles |
|---|---|---|---|---|
| Bunny Meadow | Nibbles the bunny | Burrow hole | Mossy rock | Grass green |
| Turtle Cove | Shelly the turtle | Tide pool | Coral | Sand |

Switch themes with the two tabs under the signboard. Switching starts a fresh level (clears plan, resets hero, reshuffles goal + obstacle).

## Concepts

- **Hero**: a top-down cute animal that faces the direction it will move; idle bob animation, soft drop shadow.
- **Step-plan**: a single row of 10 slots. Clicking Turn Left / Forward / Turn Right queues that move — the hero does not move until GO is pressed.
- **Adventuring**: playback of the queued plan. During Adventuring the controls hide, the board and step-plan scale up 30%, and the reset button stays active.
- **Obstacles**: blocking cells.
  - Hard obstacle — implemented (rock / coral), blocks movement.
  - Soft obstacle — planned, not yet implemented.
- **Goal**: burrow or tide pool, placed randomly (never on the hero's start cell or the obstacle cell).

## Built features

- 3x3 checkered board with themed tiles, rounded cells, wooden frame with drop shadow.
- Hero moves smoothly cell-to-cell (springy easing), rotates on turns, bobs while idle.
- Controls: Turn Left, Forward, Turn Right (chunky 3D buttons) plus a big green GO! button.
- Step-plan: 10 slots, arrow chips; clicking a queued move removes it and shifts the rest; X button clears the whole plan; strip tints red when full.
- Reset (⟲): resets the hero and reshuffles goal + obstacle positions.
- Collision: hitting a wall or obstacle blinks that cell red twice (~1s) and shakes the hero — no movement.
- Step-plan auto-clears when adventuring finishes.
- Win sequence: goal disappears → grid frame + hero shadow blink green → full-screen celebration (everything else hidden, giant dancing hero, confetti, bursting rings, "YAY!") → auto full reset to a fresh level (~3s total).

## Tweakable props

- `theme` — `meadow` | `cove` (starting theme)
- `startCell` — 0–8, hero's start cell (default 6, bottom-left)
- `facing` — `up` | `right` | `down` | `left` (default up)
- `showDevLabels` — boolean, shows element-name captions for design reference (default off)

## Element IDs (for design reference)

`game-root`, `sky`, `header`, `signboard`, `theme-switch`, `btn-theme-meadow`, `btn-theme-cove`, `grid-frame`, `grid`, `cell-0`…`cell-8`, `hero-marker`, `btn-reset-board`, `log-grid`, `btn-clear-log`, `controls`, `btn-turn-left`, `btn-forward`, `btn-turn-right`, `btn-start`, `celebration`

## Not yet built

- Soft obstacles (behavior TBD).
- Loss condition / limited-moves scoring.
- Multiple hand-authored levels or progression.
- Sound effects and music.
- Persistent state across reloads.
