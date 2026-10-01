> **Historical doc.** This describes an early version of [adventure-plan](https://github.com/adalagandev/adventure-plan), the game Hop-Hop-Home was branched from. File names and rules here are outdated. See the [README](../README.md) for the current game.

# Adventure 1

A simple browser game prototype: a 3x3 grid board where a "hero" follows a step-plan the player builds by clicking arrows, then executes on "Start."

Single file: `Grid Game.dc.html` (no back-end, runs entirely client-side).

## Concepts

- **Hero**: a triangle marker (red tip = facing direction) that moves through the 3x3 grid.
- **Step-plan**: a 10x2 (20-slot) history strip. Clicking Forward / Turn Left / Turn Right queues that move into the step-plan instead of moving the hero immediately.
- **Adventuring**: the state while the queued step-plan is being executed (after pressing Start). During Adventuring:
  - The controls (turn/forward/start buttons) hide.
  - The game board and step-plan scale up 30%.
  - The reset button stays active.
- **Obstacles**: blocking cells the hero can't move through.
  - Hard obstacle (octagon, light red) — currently implemented, blocks movement.
  - Soft obstacle — planned, not yet implemented (noted for future work).
- **Flag (goal)**: a green flag placed randomly on the board (never on the hero's start cell or the obstacle cell). Reaching it during Adventuring is the win condition.

## Built features

- 3x3 checkered game board (light gray/white).
- Hero marker: triangle with red direction tip, drop shadow, smooth sliding movement between cells (no teleporting), rotates smoothly on turns.
- Controls: Turn Left, Forward, Turn Right (curved/straight arrow icons), plus a green Start button.
- Step-plan (click-history) grid: shows the queued moves as arrow icons; turns light red when full (20/20); clicking a queued move removes it and shifts the rest; a clear (X) button resets it.
- Reset-board button (⟲): returns hero to its default start cell/facing.
- Playback ("Adventuring"): pressing Start executes the step-plan in order at a moderate pace, moving/rotating the hero.
- Collision handling: hitting a wall or the obstacle blinks that cell red twice (~1s) and shakes the hero, without moving it.
- Randomized layout: obstacle and flag positions are randomized on load, guaranteed not to overlap the start cell or each other.
- Win condition: when the hero reaches the flag, the flag disappears, the grid's outer frame blinks green twice, and the hero's shadow blinks green in sync — signaling endgame.

## Not yet built

- Soft obstacles (distinct from hard obstacles — behavior TBD).
- Loss condition / retry flow.
- Multiple levels or level progression.
- Sound/audio feedback.
- Persistent state (e.g. localStorage) across reloads.
