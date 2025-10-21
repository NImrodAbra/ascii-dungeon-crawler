# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ASCII Dungeon Crawler is a single-file, browser-based retro-style dungeon crawler game built with vanilla HTML, CSS, and JavaScript. The entire game (UI, styles, and logic) is contained in `index.html`.

## Running the Game

Simply open `index.html` in a web browser. No build process, dependencies, or server required.

For development with live reload, you can use:
```bash
python3 -m http.server 8000
# Then open http://localhost:8000
```

## Architecture

### Single-File Structure
All code lives in `index.html`:
- **Lines 7-132**: CSS styles for retro terminal aesthetic (green-on-black theme)
- **Lines 135-176**: HTML structure (stats grid, game board, message area, controls, legend)
- **Lines 178-285**: JavaScript game logic

### Core Game State
The `game` object (line 181) holds all game state:
- `grid`: 2D array (40x15) representing the dungeon layout
- `playerX/playerY`: Player position
- `health`, `score`, `treasureCount`, `level`: Game stats
- `enemies[]`, `treasures[]`: Dynamic entity arrays
- `gameOver`, `won`: Game state flags

### Game Loop Architecture
This is NOT a traditional requestAnimationFrame game loop. Instead:
- Player moves are **event-driven** (keydown listener, lines 277-282)
- Each player move triggers:
  1. Player position update
  2. Collision detection (treasure/exit/wall)
  3. Enemy AI movement (all enemies move toward player)
  4. Enemy collision detection
  5. Stats update
  6. Grid re-render

Enemies only move in response to player movement, making it turn-based rather than real-time.

### Grid System
- Coordinates: `grid[y][x]` (row-major order)
- Cell types: EMPTY `' '`, WALL `'#'`, PLAYER `'@'`, TREASURE `'$'`, ENEMY `'E'`, EXIT `'X'`
- Rendering: The `render()` function (lines 226-234) creates a fresh display grid each frame by layering entities on top of the base grid

### Key Systems

**World Generation** (`initializeGame()`, lines 183-224):
- Randomly places 100 walls (avoiding spawn area and exit area)
- Places 5 treasures in valid empty spaces (not in spawn area)
- Spawns 3 enemies at least 5 units away from player
- Exit always spawns at bottom-right (GRID_WIDTH-2, GRID_HEIGHT-2)

**Enemy AI** (lines 248-254):
- Simple pathfinding: moves one step toward player each turn
- Chooses axis with greatest distance (Manhattan distance)
- Wall collision prevents movement through obstacles
- No A* or advanced pathfinding

**Collision Detection**:
- Wall collision: Blocks player movement (line 240)
- Treasure collision: Removes treasure from array, adds 10 points (lines 243-244)
- Exit collision: Wins game, adds 100 points (line 246)
- Enemy collision: Reduces health, ends game at 0 health (lines 256-257)

## Common Issues

**HTML Structure Bugs**: Lines 143-176 contain duplicate closing `div` tags (e.g., `</div>div>`) and other malformed HTML. This should be fixed.

**No Save System**: Game state resets completely on page reload.

**Fixed Grid Size**: 40x15 is hardcoded, no responsive sizing.

## Modifying Game Behavior

- **Adjust difficulty**: Change number of enemies (line 211) or starting health (line 189)
- **Change grid size**: Modify `GRID_WIDTH`/`GRID_HEIGHT` constants (line 179)
- **Add new entity types**: Add constants, update render logic, and add collision handling in `movePlayer()`
- **Modify enemy AI**: Change movement logic in lines 248-254
