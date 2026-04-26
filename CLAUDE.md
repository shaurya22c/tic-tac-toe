# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single-file vanilla HTML/CSS/JS tic-tac-toe game. No build step, no dependencies, no package manager. Open `tic-tac-toe.html` directly in a browser to play.

## Running the game

```bash
open tic-tac-toe.html        # macOS
xdg-open tic-tac-toe.html   # Linux
```

For live-reload during development, any static file server works:

```bash
npx serve .
python3 -m http.server 8080
```

## Architecture

Everything lives in `tic-tac-toe.html` in three sections:

**CSS (`<style>`)** — dark-themed UI. Board cells use CSS Grid (3×3, fixed 110px cells). Winner highlight uses the `.win` class with a `pulse` keyframe animation. Mode/score state is reflected through JS class manipulation, not CSS variables.

**HTML (`<body>`)** — static markup. The 9 `.cell` divs carry `data-i` attributes (0–8) as the sole link between DOM and game logic.

**JS (`<script>`)** — all game state is module-level:
- `board` — length-9 array of `null | 'X' | 'O'`
- `current` — whose turn (`'X'` or `'O'`)
- `gameOver` — boolean gate that blocks `play()`
- `vsComputer` — toggles AI mode
- `scores` — `{ X, O, D }` object, reset on mode switch

Key functions:
- `play(i)` — central move handler; writes to `board`, updates DOM, calls `checkWinner`, triggers AI via `setTimeout(bestMove, 300)` so the UI can repaint first
- `checkWinner(b)` — pure function; checks all 8 win lines in `WINS`; returns `{ winner, line }` or `null`
- `minimax(b, isMax)` / `bestMove()` — unoptimized minimax (no alpha-beta); fine for 3×3 but O(9!) worst-case

## Constraints to keep in mind

- No build tooling — stay vanilla; avoid adding `import`/`export` or npm dependencies
- The minimax runs synchronously on the main thread; adding alpha-beta pruning would help if the board size ever grows
- Scores are in-memory only; they reset on page reload
