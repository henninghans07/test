# CLAUDE.md

Guidance for AI assistants (and humans) working in this repository.

## Project overview

**Sky Hopper** is a mobile-first, browser-based tap game in the style of Flappy
Bird. A rocket rises on each tap and the player flies it through gaps between
pipes. It is built as a single, self-contained HTML file with no dependencies,
no build step, and no backend. The UI copy is in **German**.

## Repository layout

```
.
├── index.html   # The entire game: HTML, CSS, and JS in one file
├── README.md    # Player-facing instructions (German)
└── CLAUDE.md    # This file
```

That's it — there is no `package.json`, no bundler, no framework, and no test
suite. The whole game lives in `index.html`.

## Running the game

There is nothing to build or install. To play:

- Open `index.html` directly in a browser, **or**
- Serve the folder over HTTP (useful for mobile testing on the same network):
  ```bash
  python3 -m http.server 8000
  # then open http://<host>:8000/index.html
  ```

The game is designed for touch devices but also works with mouse and keyboard
(`Space` / `ArrowUp` to start, flap, and retry).

## Code architecture (`index.html`)

Everything is inside one IIFE (`(() => { ... })()`) in the `<script>` block at
the bottom of the file. Key pieces, in order:

- **Screens / DOM refs** — start overlay (`#start`), game-over overlay
  (`#over`), and their buttons/labels. Overlays are shown/hidden via the
  `.hidden` CSS class.
- **`resize()`** — sizes the `<canvas>` to the viewport, clamping
  `devicePixelRatio` to 2 and applying it via `ctx.setTransform`. All game logic
  works in CSS pixels (`W`, `H`).
- **Highscore** — persisted in `localStorage` under the key
  `skyhopper_highscore`.
- **State machine** — `STATE.MENU / PLAY / OVER`, tracked in `state`.
- **`cfg()`** — returns difficulty constants (gravity, flap impulse, pipe width,
  gap, speed, spacing) **derived from screen dimensions** so difficulty feels
  consistent across phone sizes. The result is cached in `C` and recomputed on
  `reset()`.
- **Entities** — `bird` (the rocket), `pipes[]`, and decorative `stars[]`.
- **Game flow** — `reset()`, `startGame()`, `gameOver()`, `spawnPipe()`,
  `flap()`.
- **`update()`** — physics, pipe spawning/recycling, scoring, and AABB-style
  collision against pipe gaps plus floor/ceiling.
- **Render functions** — `drawBackground()` (gradient + parallax stars),
  `drawPipes()`, `drawBird()` (rocket with animated flame, tilt), `drawScore()`,
  and the `roundRect()` helper.
- **`loop()`** — `requestAnimationFrame` loop; only calls `update()` while in
  `PLAY`, but always renders.

## Conventions

- **Single file, no dependencies.** Keep new game code inside the existing IIFE
  in `index.html`. Do not introduce a build step, framework, or npm packages
  unless explicitly asked.
- **Screen-relative tuning.** Sizes, speeds, and physics are expressed as
  fractions of `W`/`H` (see `cfg()`, `reset()`, `drawScore()`). Preserve this so
  the game scales across devices — avoid hard-coded pixel values for gameplay.
- **Mobile-first.** Preserve the touch handling and meta tags that prevent
  scrolling, zooming, and tap-highlight (`touch-action`, `overscroll-behavior`,
  `user-scalable=no`, `passive: false` on `touchstart`).
- **German UI.** User-facing strings in the HTML and README are German. Match
  the existing language when editing copy.
- **CSS lives in the `<head>`.** Styling uses `clamp()` for fluid, responsive
  typography — keep that approach for new UI.

## Git workflow

- Commit messages are short, imperative, and describe the change (e.g.
  "Make pipe gaps larger for easier gameplay"). Follow that style.
- Do not create pull requests unless explicitly requested.

## Notes for future changes

- There is currently no automated testing or linting. Verify changes by opening
  `index.html` in a browser and playing.
- A GitHub Pages deploy workflow existed previously but was removed
  intentionally (the game is played locally). Don't re-add deployment without
  being asked.
