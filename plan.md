# Snake Game v2 — Menu, Themes, Difficulty, Leaderboard

> **For Hermes:** Jules implements all features. Single file: `index.html`

**Goal:** Add main menu with theme selection (classic + kids), difficulty settings (grid size + speed), and top-10 leaderboard with name entry.

**Architecture:** Extend existing `index.html` — add menu screen, CSS theme system via custom properties, localStorage leaderboard. Keep single-file, zero dependencies.

---

## Feature Spec

### 1. Main Menu Screen
- Full-screen menu overlay shown before game starts
- Title: "SNAKE" (big, styled per theme)
- Three buttons/sections:
  - **🎮 Play** → starts game with selected theme + difficulty
  - **🎨 Theme** → toggle between Classic / Kids
  - **⚙️ Difficulty** → grid size + speed selectors
  - **🏆 Leaderboard** → top 10 scores
- Menu hidden during gameplay
- After game over, show score + "Save Score" + "Menu" buttons

### 2. Theme System
CSS custom properties on `:root` / `.theme-classic` / `.theme-kids`. Two themes:

| Property | Classic | Kids |
|----------|---------|------|
| `--bg` | #9bbc0f | #e8f5e9 |
| `--snake` | #0f380f | #4caf50 |
| `--food` | #0f380f | 🍎 (emoji or #ff5722) |
| `--grid-line` | #8bac0f | #c8e6c9 |
| `--text` | #0f380f | #2e7d32 |
| `--bezel` | #2b3a42 | #ff9800 (orange) |
| `--overlay-bg` | rgba(155,188,15,0.9) | rgba(255,152,0,0.85) |
| `--font` | Courier New | 'Fredoka One', cursive (or Comic Sans fallback) |
| `--snake-radius` | 0 | 4px (rounded segments) |
| `--food-shape` | square | circle (border-radius: 50%) |

Theme saved in localStorage (`snakeTheme`).

### 3. Difficulty Settings
Stored in state, selectable in menu:

| Setting | Options |
|---------|---------|
| Grid Size | Small (15×15) / Medium (20×20) / Large (25×25) |
| Speed | Slow (200ms) / Normal (150ms) / Fast (100ms) |

Defaults: Medium + Normal. Saved in localStorage.

### 4. Leaderboard
- localStorage key: `snakeLeaderboard`
- Array of `{name, score, theme, difficulty, date}`
- Top 10 by score (descending)
- After game over:
  - If score > 0 and would place in top 10 (or leaderboard has <10 entries):
    - Show name input field + "Save" button
  - Otherwise: just show "Score not in top 10" or skip
- Leaderboard screen in menu: scrollable list with rank, name, score, date
- Max name length: 12 characters
- Clear leaderboard button (with confirmation)

---

## Implementation Tasks

### Task 1: CSS Theme System + Kids Theme

**Objective:** Refactor CSS to use custom properties. Define classic + kids themes. Add theme switching.

**Files:** Modify `index.html` — `<style>` section

**Details:**
1. Extract all colors/fonts/sizes into CSS custom properties on `:root`
2. Create `.theme-classic` and `.theme-kids` class sets
3. Kids theme: bright green+orange palette, rounded snake segments, circle food, playful font
4. Add `setTheme(name)` JS function — applies class to `<body>`, saves to localStorage
5. Default to classic if no saved preference
6. Food in kids theme: draw fruit emoji (🍎) or colorful circle instead of square

**Verification:** Switch between themes via JS console → game visuals change. Reload → theme persists.

---

### Task 2: Main Menu HTML + CSS

**Objective:** Add menu screen overlay with sections for Play, Theme, Difficulty, Leaderboard.

**Files:** Modify `index.html` — HTML structure + CSS styles

**Details:**
1. Add `<div id="menu">` overlay with:
   - Big "SNAKE" title
   - Theme selector (Classic / Kids toggle buttons)
   - Difficulty section (grid size: S/M/L buttons, speed: Slow/Normal/Fast buttons)
   - "PLAY" button (prominent)
   - "LEADERBOARD" button
2. Menu CSS: centered card, themed colors, button styling consistent with d-pad
3. Menu shown on load, hidden during gameplay
4. `showMenu()` / `hideMenu()` functions
5. Difficulty values stored in `state.gridSize` and `state.baseSpeed`

**Verification:** Page loads → menu visible. Click Play → menu hides, game starts. After game over → can return to menu.

---

### Task 3: Difficulty Integration

**Objective:** Wire grid size and speed settings into game logic.

**Files:** Modify `index.html` — game constants + init

**Details:**
1. Replace `const GRID_SIZE = 20` with `state.gridSize` (default 20)
2. Grid size options: 15 (Small), 20 (Medium), 25 (Large)
3. Replace `const baseSpeed = 150` with `state.baseSpeed` from difficulty
4. Speed options: 200 (Slow), 150 (Normal), 100 (Fast)
5. When grid size changes → reinitialize snake position to center, respawn food
6. Resize canvas based on new `CELL_SIZE = floor(maxWidth / state.gridSize)`
7. Save difficulty preferences to localStorage

**Verification:** Select Small grid → smaller play area, snake in center. Select Fast → snake moves noticeably faster. Settings persist on reload.

---

### Task 4: Leaderboard System

**Objective:** Full leaderboard — save, display, name entry flow.

**Files:** Modify `index.html` — JS + HTML

**Details:**
1. `loadLeaderboard()` → reads `snakeLeaderboard` from localStorage, returns array
2. `saveToLeaderboard(name, score, theme, difficulty)` → adds entry, sorts by score desc, keeps top 10, saves
3. After game over (in `endGame()`):
   - Check if score qualifies for top 10: `isTop10(score)` 
   - If yes: show name input with "SAVE" button in overlay
   - If no: show "Great game! Not in top 10 though" + menu button
4. Name input: `<input maxlength="12" placeholder="Your name">`
5. Leaderboard screen in menu:
   - `<div id="leaderboardScreen">` with ranked list
   - Each entry: `#1  Tom  420pts  Classic  May 23`
   - Empty state: "No scores yet! Play a game."
   - "CLEAR" button with confirm dialog
   - "BACK" button to return to main menu
6. Leaderboard entries include difficulty label and theme used

**Verification:** Play game → score 30 → game over → if top 10, enter name → appears in leaderboard. Leaderboard persists on reload. Clear works.

---

### Task 5: Kids Theme Polish

**Objective:** Make the kids theme delightful for a 5-year-old.

**Files:** Modify `index.html` — CSS + draw logic

**Details:**
1. Kids theme colors: bright green snake on light background, orange bezel
2. Snake segments: rounded corners (`border-radius: 4px`) with subtle gap
3. Food: draw as emoji (🍎) or colorful circle with slight pulse animation
4. Score display: bigger, colorful, maybe with a star emoji ⭐
5. Game over overlay: friendly "Oh no! Try again?" text
6. Font: try to load Fredoka One from Google Fonts (with fallback to Comic Sans)
7. Canvas background: subtle grid dots pattern instead of solid
8. Bezel: orange rounded frame, looks like a toy/game device

**Verification:** Switch to Kids theme → bright colors, rounded snake, emoji food, playful feel.

---

### Task 6: Integration & Edge Cases

**Objective:** Wire everything together. Handle all states and transitions.

**Files:** Modify `index.html`

**Details:**
1. Flow: Page load → Menu → (select theme/difficulty) → Play → Game Over → (save score if top 10) → Menu
2. Leaderboard accessible from menu at any time
3. Theme switching during gameplay? Keep it simple: only from menu (before/after game)
4. Difficulty changes only apply on next game start
5. Edge cases:
   - Empty leaderboard → don't show name prompt (any score qualifies)
   - Leaderboard full (10 entries) → only prompt if score beats #10
   - Name input empty → don't save, show validation
   - Name too long → trim to 12 chars
   - localStorage full → graceful fallback
6. Mobile: menu buttons large enough, leaderboard scrollable

**Verification:** Complete flow works end-to-end. All edge cases handled.

---

## Files Summary

| File | Action |
|------|--------|
| `/opt/data/snake-game/index.html` | **Modify** — add all features |

Still one file. Still zero dependencies.

## Work Plan

1. Update `plan.md` in repo (this document)
2. Jules: implement all tasks in one session (reads existing code + this plan)
3. Review PR, merge
4. Deploy to GitHub Pages (auto-updates)
