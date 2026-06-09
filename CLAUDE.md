# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file, dependency-free HTML application for organizing football (soccer) tournaments for FC Rebstein. Everything lives in `index.html` (formerly `FC-Rebstein-Turnier.html`) — HTML, CSS, and JavaScript are all inlined, along with a bundled copy of the SheetJS (xlsx.js) library for Excel import/export.

## Architecture

The app is structured as a single `state` object with five keys:
- `state.meta` — tournament settings (name, date, field count, rounds, time config)
- `state.players` — `{id, name, cat}` array
- `state.teams` — `{id, name, captainId, playerIds[]}` array
- `state.schedule` — `{slot, field, round, homeId, awayId, hg, ag, played}` array
- `state.twShots` — `{playerId: [field1, field2, field3]}` map (Torwand/wall-shot mini-game)

The JavaScript is organized into sections separated by `/* ===== SECTION ===== */` comments (line ~441 onward). Key sections:
- **DATEN** (~441): `state` definition, category list, `CATEGORIES` constant
- **KATEGORIE-NORMALISIERUNG** (~485): `normCat()` fuzzy-maps raw import strings to canonical categories
- **SPIELER** (~514): manual player entry and rendering
- **IMPORT** (~557): drag-and-drop Excel/CSV import via SheetJS; shows column-mapping dialog before applying
- **TEAMS BILDEN** (~692): snake-draft algorithm groups players by category priority, first T players become captains
- **SPIELPLAN** (~800): round-robin schedule generator with multi-field support and optional time slots
- **TABELLE** (~887): standings with 3-pts win, direct-comparison tiebreaker
- **EINSTELLUNGEN** (~931): modal for meta settings
- **SPEICHERN / LADEN** (~956): JSON export/import and Excel export
- **DRUCKEN** (~982): opens a print window for schedule or standings
- **TORWAND** (~1039): penalty-shootout mini-game (6 scoring zones, 3 shots per player)
- **INIT** (~1135): `renderAll()` entry point

## Running the App

No build step. Open `FC-Rebstein-Turnier.html` directly in a browser. All functionality works offline.

```bash
open index.html
```

## Key Conventions

- `nid()` generates unique IDs: `'x' + uid++ + Date.now().toString(36).slice(-3)`
- `$()` is aliased to `document.querySelector()`
- HTML is built by string concatenation; use `esc()` for all user-supplied values to prevent XSS
- SheetJS occupies roughly the first 440 lines and should not be edited
- All state mutations go through the module-level `let state` — there is no framework reactivity; call the appropriate `render*()` function after mutations
- `renderAll()` re-renders every panel; prefer targeted `renderPlayers()` / `renderTeams()` / `renderSchedule()` etc. when only one section changed
