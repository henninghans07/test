# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this is

A small collection of **mobile-first browser games** (German UI), each written as a
single self-contained HTML file with inline CSS and JavaScript. No build step, no
dependencies, no framework, no package manager. The games are played directly in the
browser and are designed primarily for iPhone (added to the Home Screen as a PWA-style
full-screen web app).

**Live site:** https://henninghans07.github.io/test/ (GitHub Pages)

## Layout

Every file lives at the repository root.

| File             | Type        | Description                                                        |
| ---------------- | ----------- | ------------------------------------------------------------------ |
| `index.html`     | Menu        | Launcher; lists/links every game via `.card` entries               |
| `skyhopper.html` | Canvas game | 🚀 Flappy-Bird-style; tap to fly through gaps; has a settings menu  |
| `2048.html`      | DOM game    | 🔢 Classic 2048 sliding-tile puzzle                                 |
| `minicraft.html` | Canvas game | ⛏️ 2D sandbox with world gen, mining and building                  |
| `snake.html`     | Canvas game | 🐍 Swipe-controlled snake                                           |
| `memory.html`    | DOM game    | 🧠 Emoji pair-matching memory game                                  |
| `README.md`      | Docs        | Short German overview + "add to Home Screen" instructions          |

There is **no `.github/` workflows directory** — Pages deploys directly from the
branch (a Pages Action workflow existed previously but was intentionally removed; see
git history). Do not re-add a deploy workflow unless explicitly asked.

## How to run / test

There is no test suite and no toolchain. To preview a game, open the HTML file in a
browser, or serve the directory statically, e.g.:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000/
```

Because games target mobile, verify behavior at phone viewport sizes (and ideally with
touch emulation). The canvas games resize to the viewport, so check both portrait
orientation and the safe-area insets.

## Conventions to follow

When adding or editing a game, match the existing patterns:

- **Single file, zero dependencies.** Keep all HTML, `<style>`, and `<script>` inline in
  one `.html` file. Do not introduce external libraries, CDNs, bundlers, or `npm`.
- **German UI.** All player-facing text is in German (`<html lang="de">`). Code
  identifiers and comments are typically English.
- **Mobile/PWA meta tags.** Reuse the standard `<head>` block: `viewport` with
  `viewport-fit=cover` (and `user-scalable=no` for the games), `theme-color` `#1a1a2e`,
  and `apple-mobile-web-app-capable` / `mobile-web-app-capable` set to `yes`.
- **Shared styling idioms:** the system font stack
  (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`), a dark
  `#1a1a2e`/blue-gradient background, gradient-clipped title text, the `.overlay`
  start/game-over screen pattern, `box-sizing: border-box`, and
  `-webkit-tap-highlight-color: transparent`. Use `env(safe-area-inset-*)` for padding.
- **Touch first.** Use `touch-action` / `overscroll-behavior: none` to suppress
  scrolling and zoom; handle pointer/touch events. Canvas games run an animation loop
  via `requestAnimationFrame` and size the canvas to the viewport.
- **High scores / settings in `localStorage`,** with a game-namespaced key. Existing
  keys: `game2048_best`, `skyhopper_highscore`, `skyhopper_settings`, `snake_highscore`,
  plus per-difficulty memory keys. Follow the `<gamename>_<thing>` convention.
- **Back navigation.** Every game links back to the menu with `href="index.html"`.

### Adding a new game

1. Create `<gamename>.html` at the root following the conventions above.
2. Register it in `index.html` by adding a new `<a class="card" href="<gamename>.html">`
   block — include an emoji `.icon` (add a matching `.icon.<abbr>` gradient rule in the
   `<style>`), a `.name`, and a German `.desc`.
3. Update `README.md`'s game list.

## Git workflow

- Active development branch for this work: **`claude/claude-md-docs-sxtb6s`**. Develop,
  commit, and push there; create it locally if missing. Never push to another branch
  without explicit permission.
- Push with `git push -u origin <branch-name>`.
- Commit messages are short, imperative, and describe the change (see history, e.g.
  "Add Snake and Emoji Memory games, link them in the menu").
- Do **not** open a pull request unless explicitly asked.
