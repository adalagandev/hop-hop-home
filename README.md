# Hop-Hop-Home — Kids' Programming Puzzle

**Play it:** https://adalagandev.github.io/hop-hop-home/

A browser puzzle game that teaches kids (≈4–8) sequencing. The player queues movement commands into an **Adventure Plan**, presses **GO**, watches an arrow-trail preview of the plan, then watches the animal hero act it out on a grid. Get the hero home — or, in Puzzle Mode, catch the runaway puzzle piece.

Hop-Hop-Home is a major upgrade of [adventure-plan](https://github.com/adalagandev/adventure-plan). New since that game: Jump and Spin commands, a wide 10×5 board, Puzzle Mode with a Koala Album, and a fixed bottom-left start.

No build step, no back-end, no audio files. Art and sound are inline; the only external images are the album pictures in `assets/`.

Player guide for kids and grown-ups: [How to Play.md](How%20to%20Play.md).

---

## 1. How to run

Open `index.html` in any modern browser (Chrome/Edge/Safari/Firefox), or serve the folder with any static server. It needs internet only for the Google Font (Fredoka) and falls back to system-ui offline. The `assets/` folder must sit next to `index.html` for Puzzle Mode pictures.

### File format
`index.html` is a self-contained **bundled page**. Its `<script type="__bundler/template">` holds a "Design Component" (`.dc.html`) as a JSON string, and `<script type="__bundler/manifest">` holds the runtime that renders it.

Inside the template: an `<x-dc>` body with `{{ path }}` holes, plus a `<script type="text/x-dc" data-dc-script>` holding `class Component extends DCLogic { … }`. The runtime is React-like: `state`, `setState`, `componentDidMount`, and `renderVals()` returns every value the template reads. Template control flow: `<sc-for list="{{ x }}" as="item">`, `<sc-if value="{{ flag }}">`.

To edit the game logic, change the template string (or regenerate the bundle from the source `.dc.html`). To port to another stack, treat `renderVals()` as the view-model and the template as JSX.

### Deploying
The site is served by GitHub Pages from the root of the `main` branch. Pushing to `main` redeploys it.

---

## 2. Project files

| File | Purpose |
|---|---|
| `index.html` | The whole game (bundled). |
| `assets/` | Koala album puzzle pictures. |
| `How to Play.md` | Player guide. |
| `docs/adventure1.md`, `docs/adventure2-w-propetheme.md`, `docs/adventure3.md` | Design history from the predecessor game (adventure-plan). Outdated; this README supersedes them. |
| `README.md` | This file. |

---

## 3. Core gameplay loop

1. Board appears with the hero in the **bottom-left corner**, facing up or right at random, plus hard obstacles, soft obstacles and a goal.
2. Player taps **Turn Left / Forward / Turn Right / Jump** (or **Spin** for the turtle). Each tap adds one step to the **Adventure Plan** (8 slots). The hero does **not** move yet.
3. Tapping a filled plan slot removes that step. The plan ⟲ button clears all. Light-green slots show the minimum steps needed before GO.
4. **GO** unlocks when the plan is long enough (see §5).
5. Press GO → **trail preview** plays (arrows appear one per step), then the **hero runs** the plan step by step.
6. Reaching the goal → win sequence → new level with a *different* animal theme, same board size.
7. If the plan ends without reaching the goal, the hero stays where it stopped (position + facing persist); the plan clears and the player plans again from there.

---

## 4. Themes (5)

The game starts in Koala Grove. After each win the theme changes to a random *different* one. Themes can also be picked in Settings.

| Key | Title | Hero | Goal | Obstacle |
|---|---|---|---|---|
| `meadow` | Bunny Meadow | Nibbles (bunny) | Burrow | Mossy rock |
| `cove` | Turtle Cove | Shelly (turtle) | Tide pool | Coral |
| `arctic` | Orca Passage | Kai (orca) | Open bay | Iceberg |
| `grove` | Koala Grove | Bramble (koala) | Gum tree | Tree stump |
| `floe` | Penguin Floes | Pip (penguin) | Big iceberg | Rocky islet |

Each theme has its own sky gradient, checkerboard tile colors and wooden frame colors (see `THEMES` in the component).

### Directional hero views
Every hero has 3 hand-drawn inline-SVG views: facing **down** → front, **up** → back, **right** → side profile, and **left** → the side view mirrored. The hero has a ground shadow, an idle bob, and slides between cells.

---

## 5. Rules

### Board sizes
| Setting | Grid | Cell px | Hard obstacles | Soft obstacles |
|---|---|---|---|---|
| 4 | 4×4 | 83 | 3 | 0 |
| 5 | **10×5** (wide) | 58 | 8 | 3 |
| 6 | 6×6 (**default**) | 55 | 5 | 3 |

On the wide 10×5 board the goal is always in the rightmost 3 columns.

### Level generation (`pickLayout`), up to 400 attempts
- Goal ≠ start; obstacles and soft obstacles are placed randomly on the remaining cells.
- The goal must be reachable **treating soft obstacles as walls**, so you never need to break one to win.
- Shortest path (with all blockers) must be **≥ 3 moves**.
- Goal must be **next to at least one obstacle** (hard or soft).
- Preferred: blockers force a **detour**. If no attempt achieves this, the first layout that satisfies the other rules is used.

### Commands
- **Forward (F)**: move one cell in the facing direction.
- **Turn Left (L) / Turn Right (R)**: rotate 90° in place.
- **Jump (J)**: jump 2 cells forward over anything. The landing cell must be on the board and must not be a hard or soft obstacle; otherwise it's a bump and soft obstacles take no damage. Landing on the goal wins; passing over it doesn't. Before the jump, a pink dashed line shows the landing cell.
- **Spin (S)**, Turtle Cove only, shown in place of Jump: Shelly spins in place and breaks a soft obstacle directly ahead in one go.
- The plan holds max **8** steps. Extra taps buzz.

### GO unlock
- Goal **1–2 moves** away (BFS from the hero's current cell, blockers as walls): GO unlocks at **1 step**.
- Otherwise it needs **3+ steps**.

### Collisions
- **Wall / hard obstacle**: cell blinks red, hero shakes, low thud, no move.
- **Soft obstacle** (dashed gold ring + white pips = hits left): each bump uses one Forward and lightens the tile (deep gold → light gold → cream). The 3rd hit **breaks** it (bubbles + pop) and the cell becomes walkable. The hero does not advance on the breaking bump. Hits persist across plans within the same level.

### Tap to play
Tapping a cell outside a run makes items on it dance; empty cells blink 3 times.

---

## 6. GO sequence

1. **Trail preview** (`planTrail` + `runPlayback`): the whole plan is simulated, then revealed one arrow per plan step (bumps included). Forward = straight arrow, turns = curved arrow, Jump = double chevron, Spin = circular arrow. At most 2 arrows per cell, side by side. Soft obstacles the trail would hit show a blinking ring.
2. **Hero run** (`runHero`): signboard and controls hide, the board scales up, and the active plan slot is enlarged. When the plan ends, plan and trail clear and the hero stays put.
3. **Win** (`triggerWin` + `winBlinks`): the goal disappears, tiles flash a jackpot pattern, the frame blinks green, then a full-screen celebration plays (dancing hero, confetti, "YAY!", "Made it home!"). After that, a new level with a different theme loads.

---

## 7. Puzzle Mode & Koala Album

- Toggle with the **Puzzle Mode / Find Home Mode** button next to the signboard. Puzzle Mode locks the theme to **Koala Grove** and the board to **10×5**.
- Instead of a home cell, a purple **puzzle piece** sits on the board. It moves one cell every 3 hero steps; its dots count down the steps left, and tapping it shows a dashed line to its next cell. Catching it wins.
- Each catch fills 1 of 9 squares of the current koala picture, shown in a reveal modal. The 9th piece completes the picture, which spins, gets saved to the **Koala Album** (5 slots), and the counter resets.
- `koala-DEFAULT_FIRST.jpg` is always the first picture. After that, each new puzzle is a random picture not yet in the album. To add a picture, drop it in `assets/` and add `{ src, name }` to `KOALA_PICS`.
- Progress is saved in `localStorage` (`ag-koala-puzzle`, `ag-koala-album-v2`, `ag-koala-current`).
- **Test shortcut:** press **T** to jump into Puzzle Mode with 8 of 9 pieces collected. There is no visible button for it.

---

## 8. Sound (Web Audio, synthesized)
Plan blip, undo slide, UI tick, GO fanfare (C5-E5-G5-C6), trail tick, step sweep, hard-bump thud, soft-bump thud, boing for jump, pop + chimes for soft break, and a win arpeggio. Toggle in Settings (Sound / Muted).

---

## 9. Layout & UI

- Signboard at the top; it alternates between the theme title and the mission line every 5s, and tapping it toggles it.
- The plan panel is **vertical, to the right of the grid**. It has an "Adventure Plan" label, a ⟲ clear button, and the ⚙ settings gear.
- The controls row sits **below the grid**: Turn Left (blue), Forward (yellow), Turn Right (blue), Jump/Spin (pink), GO, plus the puzzle-progress button in Puzzle Mode.
- Board ⟲ (red) gives a new layout with the same theme and size.
- **Settings** is a centered modal: 5 theme pills, 3 size buttons, a Sound toggle, and Close. Clicking the backdrop also closes it.
- The board scales to fit both viewport width and height (min 0.4×). Font: **Fredoka**. Big touch targets for kids.

---

## 10. Tweakable props
| Prop | Values | Default |
|---|---|---|
| `gridSize` | 4 / 5 / 6 | 6 |
| `goal` | `home` / `piece` | `home` |
| `soundOn` | boolean | true |
| `showDevLabels` | boolean, shows element-ID hint line | false |
| `mode` | `medium` / `hard` (currently forced to `hard` in code) | `hard` |

## 11. Key methods
`pickLayout`, `withPiece`, `pieceStep`, `distance` (BFS), `minPlan`, `planTrail`, `runPlayback`, `runHero`, `blinkCell`, `popSoft`, `triggerWin`, `winBlinks`, `fullReset`, `resetBoard`, `setTheme`, `setSize`, `setGoal`, `addLog`, `removeLog`, `tapCell`, `tone` + `sfx*`.

## 12. Element IDs
`sky`, `celebration`, `game-root`, `header`, `header-row`, `signboard`, `btn-puzzle-mode`, `btn-album`, `stage`, `settings-backdrop`, `settings`, `settings-panel`, `btn-theme-meadow|cove|arctic|grove|floe`, `size-modes`, `btn-size-4|5|6`, `btn-sound`, `btn-close-settings`, `grid-frame`, `board-panel`, `board-left`, `grid`, `hero-marker`, `piece-marker`, `piece-trace`, `jump-trace`, `btn-reset-board`, `controls`, `btn-turn-left`, `btn-forward`, `btn-turn-right`, `btn-jump`, `btn-spin`, `btn-start`, `btn-puzzle`, `plan-column`, `plan-panel`, `log-grid`, `btn-settings`, `btn-clear-log`, `puzzle-backdrop`, `puzzle-modal`, `btn-close-puzzle`, `album-backdrop`, `album-modal`, `btn-close-album`.

## 13. Known gaps
- No loss condition, scoring or level progression. Only Puzzle Mode progress persists across reloads.
- No keyboard controls (except the **T** test shortcut) and no drag-reorder of plan steps.
