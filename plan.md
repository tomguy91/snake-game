# Snake Game — Nokia Classic

> **For Hermes:** Single HTML file implementation, no build tools, no framework.

**Goal:** Build a classic Snake game (Nokia 3310 style) as a single self-contained HTML file that works great on both desktop and mobile.

**Architecture:** Pure HTML5 Canvas game engine. Single file — HTML structure + CSS styling + vanilla JS game logic. No dependencies, no build step. Just open in browser.

**Tech Stack:** HTML5 Canvas, vanilla JavaScript, CSS (Nokia 3310 aesthetic)

---

## Game Specification

### Look & Feel
- Dark green background (#9bbc0f-ish Nokia LCD green, or classic #a8cc0c)
- Dark pixel-style snake and food on lighter green "screen"
- Rounded faux phone bezel around the game area (Nokia tribute)
- Pixel-style monospace font for score
- Subtle screen glare/gradient for LCD effect

### Gameplay
- Grid-based movement (e.g., 20×20 grid)
- Arrow keys on desktop, swipe + on-screen d-pad on mobile
- Snake grows by 1 segment per food eaten
- Score increments by 10 per food
- Game over when snake hits wall or itself
- Press Space / tap to restart after game over
- Speed increases slightly as score grows (optional, for replayability)

### Mobile Support
- Viewport meta tag for proper scaling
- Touch swipe detection (up/down/left/right)
- On-screen directional buttons below the game area (large, tappable)
- Canvas scales to fit screen width (max ~400px for authentic feel)
- Prevent page scroll during gameplay

### Controls
| Input | Desktop | Mobile |
|-------|---------|--------|
| Up | ↑ or W | Swipe up / ▲ button |
| Down | ↓ or S | Swipe down / ▼ button |
| Left | ← or A | Swipe left / ◀ button |
| Right | → or D | Swipe right / ▶ button |
| Restart | Space | Tap "Play Again" |

---

## Implementation Plan

### Task 1: Create project structure and HTML shell

**Objective:** Set up the single-file HTML document with basic structure, CSS, and canvas element.

**Files:**
- Create: `/opt/data/snake/index.html`

**Steps:**

1. Create `index.html` with HTML5 boilerplate
2. Add viewport meta tag for mobile
3. Add `<canvas id="game">` with fallback text
4. Style: Nokia-inspired dark theme, phone bezel, LCD screen effect
5. Add score display `<div id="score">`
6. Add mobile control buttons (d-pad layout)
7. Add game-over overlay

**Verification:** Open in browser → see styled phone bezel with empty green screen, score "0", and d-pad buttons.

---

### Task 2: Implement game state and core loop

**Objective:** Game engine — grid, snake data structure, game loop with requestAnimationFrame.

**Files:**
- Modify: `/opt/data/snake/index.html` (add `<script>` section)

**Steps:**

1. Define constants: `GRID_SIZE = 20`, `CELL_SIZE`, `GAME_SPEED = 150` (ms per tick)
2. Game state object:
   ```js
   const state = {
     snake: [{x: 10, y: 10}, {x: 9, y: 10}, {x: 8, y: 10}], // head first
     direction: {x: 1, y: 0},  // moving right
     nextDirection: {x: 1, y: 0},
     food: {x: 15, y: 10},
     score: 0,
     gameOver: false,
     running: false
   };
   ```
3. `spawnFood()` — random empty cell
4. `gameLoop()` — move snake, check collisions, check food, redraw
5. `setInterval` with `GAME_SPEED` for tick-based movement
6. `draw()` — clear canvas, draw grid lines (subtle), draw snake segments, draw food
7. Start/stop functions: `startGame()`, `endGame()`

**Verification:** Snake appears and moves right automatically. Food visible at random position.

---

### Task 3: Add keyboard controls and direction locking

**Objective:** Arrow keys and WASD move the snake. Prevent 180° reversal (can't go from right to left instantly).

**Files:**
- Modify: `/opt/data/snake/index.html` (update script)

**Steps:**

1. `handleKeydown(e)` listener
2. Map: ArrowUp/W → {x:0, y:-1}, ArrowDown/S → {x:0, y:1}, ArrowLeft/A → {x:-1, y:0}, ArrowRight/D → {x:1, y:0}
3. Direction validation: new direction must not be opposite of current (e.g., if moving right, ignore left key)
4. Store in `state.nextDirection` (applied on next tick, prevents double-move within one tick)
5. Prevent default on arrow keys (stop page scroll)

**Verification:** Snake responds to arrow keys. Cannot reverse into itself. Cannot scroll page with arrows.

---

### Task 4: Implement collision detection and game over

**Objective:** Snake dies on wall hit or self-collision. Show game over overlay with score. Allow restart.

**Files:**
- Modify: `/opt/data/snake/index.html` (update script + CSS)

**Steps:**

1. `checkCollision(head)` — returns true if head out of bounds or head position overlaps any snake segment
2. In game loop: if collision → `endGame()`
3. `endGame()` — set `state.gameOver = true`, show overlay with "Game Over" + final score + "Play Again" button
4. `restartGame()` — reset state to initial values, hide overlay, start loop
5. Space bar triggers restart when game is over
6. Style the game-over overlay (semi-transparent dark, centered text)

**Verification:** Snake dies hitting wall. Snake dies hitting itself. Overlay appears. Space/button restarts game.

---

### Task 5: Implement food eating and score

**Objective:** When snake head reaches food, grow snake, increment score, spawn new food.

**Files:**
- Modify: `/opt/data/snake/index.html` (update game loop)

**Steps:**

1. In game loop, after moving head: check if `head.x === food.x && head.y === food.y`
2. If eating: don't remove tail (snake grows), increment score by 10, call `spawnFood()`, update score display
3. If not eating: remove tail segment (pop from array)
4. `spawnFood()` must avoid all current snake positions (loop until valid cell)
5. Edge case: if snake fills entire grid (win condition) → show "You Win!" — or just handle gracefully (all cells occupied)

**Verification:** Eating food grows snake by 1, score increments, new food appears in empty cell, score display updates.

---

### Task 6: Add touch controls (swipe + d-pad buttons)

**Objective:** Mobile-friendly input — swipe detection on canvas + tappable directional buttons.

**Files:**
- Modify: `/opt/data/snake/index.html` (update script + CSS + HTML)

**Steps:**

1. Touch swipe detection on canvas:
   - `touchstart` → record `startX, startY`
   - `touchend` → calculate `dx, dy`, determine primary direction
   - Threshold: minimum 30px swipe distance
   - Map to direction same as keyboard
2. On-screen d-pad buttons:
   - Four buttons in cross layout: ▲ ▼ ◀ ▶
   - `touchstart` on each → set direction (preventDefault to avoid double-fire)
   - Visual feedback on press (scale/opacity change)
3. Prevent default touch behavior on game area (no zoom, no scroll)
4. CSS: large touch targets (minimum 44×44px), good spacing

**Verification:** Swipe on canvas changes direction. D-pad buttons respond to taps. No page zoom/scroll during gameplay on mobile. Both input methods respect direction locking.

---

### Task 7: Responsive layout and mobile polish

**Objective:** Game looks great on all screen sizes. Canvas scales properly. D-pad positioned correctly.

**Files:**
- Modify: `/opt/data/snake/index.html` (CSS + canvas sizing)

**Steps:**

1. Canvas sizing: calculate cell size dynamically based on viewport
   - `const maxWidth = Math.min(window.innerWidth * 0.9, 400)`
   - `CELL_SIZE = Math.floor(maxWidth / GRID_SIZE)`
   - `canvas.width = canvas.height = CELL_SIZE * GRID_SIZE`
2. Handle window resize → recalculate and redraw
3. Center everything vertically and horizontally with flexbox
4. D-pad: below canvas, centered, with clear spacing
5. Score: above canvas, large readable font
6. Prevent body scroll/overscroll on mobile (`overscroll-behavior: none`, `touch-action: manipulation`)
7. Add a title "SNAKE" in Nokia-style pixel font above the game
8. Test at common breakpoints: 375px (iPhone SE), 414px (iPhone 11), 360px (Android)

**Verification:** Open on phone → game fills screen nicely, d-pad below game area, no scrolling, everything centered. Resize desktop browser → canvas rescales smoothly.

---

### Task 8: Speed progression and final polish

**Objective:** Add speed increase as score grows. Final visual polish and edge case handling.

**Files:**
- Modify: `/opt/data/snake/index.html` (update game loop + CSS)

**Steps:**

1. Dynamic speed: `GAME_SPEED = Math.max(70, 150 - Math.floor(score / 50) * 5)` → starts at 150ms, gets faster every 50 points, caps at 70ms
2. When speed changes, clear and reset interval with new speed
3. Add subtle screen flicker/scanline effect (CSS pseudo-element overlay) for CRT/Nokia LCD vibe
4. Add high score tracking via localStorage
5. Handle edge case: pause game when tab loses focus (visibilitychange)
6. Add subtle sound effects? (optional — can skip for simplicity)
7. Final QA pass on mobile and desktop

**Verification:** Snake speeds up as score increases. High score persists across page reloads. Game pauses when switching tabs. Visual effects are subtle and non-distracting.

---

## Files Summary

| File | Action | Description |
|------|--------|-------------|
| `/opt/data/snake/index.html` | Create | Complete game — HTML + CSS + JS in one file |

Just **one file**. Open in any browser, works offline.

## Verification Checklist

- [ ] Opens in Chrome, Firefox, Safari (desktop)
- [ ] Opens in Chrome, Safari (mobile iOS/Android)
- [ ] Arrow keys + WASD work on desktop
- [ ] Swipe + d-pad work on mobile
- [ ] Snake can't reverse into itself
- [ ] Game over on wall collision
- [ ] Game over on self collision
- [ ] Eating food grows snake + increments score
- [ ] Restart works (Space key + button)
- [ ] Speed increases as score grows
- [ ] High score saved across sessions
- [ ] No page scroll during gameplay on mobile
- [ ] Canvas scales correctly on resize
- [ ] Looks like a Nokia tribute 🟢📱
