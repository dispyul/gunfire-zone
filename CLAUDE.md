# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A collection of retro-style browser games, each built as a **single self-contained HTML file** with no build step, no dependencies, and no server required. Open any file directly in a browser.

## Running the games

```bash
start tictactoe.html   # Windows — opens in default browser
start shooter.html
```

There is no build, no install, no test suite. The entire game — HTML, CSS, and JS — lives in one file per game.

## Architecture

Every game follows the same pattern inside one `<script>` block:

- **Constants & color palette** — all magic numbers and hex colors defined at the top as `const`
- **Input system** — keyboard (`keys` object via `keydown`/`keyup`) + mouse (`mouse` object via `mousemove`/`mousedown`/`mouseup`)
- **State machine** — named states (`MENU`, `PLAYING`, `LEVEL_COMPLETE`, `GAME_OVER`) with a dispatch table mapping each state to `{ update, render }` functions
- **Entity classes** — `Player`, `Enemy`, `Bullet`, `Particle` with their own `update(dt)` and `draw()` methods
- **Game loop** — `requestAnimationFrame` with delta time capped at 50 ms to prevent tunneling on tab resume

### shooter.html specifics

- All sprites are drawn with **Canvas 2D primitives only** (no images or spritesheets) — `ctx.save()/restore()` wraps every sprite draw
- Delta-time is passed to every `update(dt)` call; friction is frame-rate-independent: `vel *= Math.pow(factor, dt * 60)`
- Audio is synthesized via **Web Audio API** oscillators — `AudioContext` is created on the first user click (browser autoplay policy)
- Mouse coordinates are scaled to canvas logical size: `(e.clientX - rect.left) * (canvas.width / rect.width)`
- High score persists via `localStorage` key `gz_hs`
- Level config lives in the `LEVELS` array; add a new object there to add a level

## Git workflow

**Commit and push after every meaningful unit of work** — do not batch up multiple features into one commit. The goal is that GitHub always reflects the current working state so nothing is ever lost.

Rules to follow on every task:
- Stage and commit as soon as a logical piece of work is complete (a new feature, a bug fix, a new game file, a config change)
- Push to `origin/master` immediately after each commit — never leave commits sitting only locally
- Commit messages are imperative and describe *what changed and why* (e.g. `Add enemy speed scaling per level`, not `changes`)
- Each game file is committed separately when possible; avoid combining unrelated changes in one commit

Remote: `https://github.com/dispyul/gunfire-zone` (`origin/master`)
