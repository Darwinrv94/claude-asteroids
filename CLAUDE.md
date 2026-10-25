# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone in plain HTML5 Canvas + vanilla JS. No dependencies, no bundler, no build step, no tests, no linter. README and in-code comments are in Spanish; keep that convention.

## Run

Open `index.html` in a browser, or serve locally: `npx serve .` (http://localhost:3000). Reload to see changes.

## Architecture

All game logic lives in `game.js` (single script, loaded by `index.html`, `'use strict'`). Canvas is fixed 800x600 (`W`, `H` constants must match the `<canvas>` attributes in `index.html`).

- **Loop**: `loop(ts)` → `update(dt)` → `draw()`. `dt` is seconds, clamped to 0.05. All motion/timers are dt-based, except ship `DRAG` (per-frame multiplier, frame-rate dependent).
- **State machine**: global `state` is `'playing' | 'dead' | 'gameover'`. `update()` branches on it; `dead` runs a 2s `deadTimer` then `ship.reset()`; `gameover` waits for Space to call `initGame()`.
- **Input**: `keys` (held) and `justPressed` (edge-triggered, consumed by `pressed(code)`). Use `pressed()` for one-shot actions, `keys[]` for held controls.
- **Entities**: classes `Ship`, `Bullet`, `Asteroid`, `Particle`, each with `update(dt)`/`draw()` and a `dead` flag. Dead entities are removed by `filter` in `update()`; they are not mutated in place mid-iteration.
- **Asteroid sizes**: index 1–3 into `RADII`/`SPEEDS`/`POINTS` arrays (index 0 unused). `split()` returns two children of `size - 1`. Note `POINTS` is small=100, large=20.
- **World is toroidal**: positions use `wrap(v, max)`. Collisions use plain `dist()` on circle radii (no wrap-aware distance); ship hit radius uses `a.radius * 0.82`.
- **Levels**: clearing all asteroids calls `nextLevel()` (resets ship, spawns `3 + level` large asteroids away from center via `SAFE_DIST`).

Rendering is vector-style (white strokes on black); no sprites or assets besides `favicon.svg`.
