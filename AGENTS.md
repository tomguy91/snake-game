# AGENTS.md — Snake Game

> Guidelines for AI agents (Claude, Codex, Jules, Hermes) working on this codebase.

---

## ⚠️ Workflow Rule (CRITICAL)

**ALWAYS use Jules for implementation.** The orchestrator (Hermes) plans and decomposes work into small parallel subtasks; Jules opens PRs for each. Never do implementation work inline in the main agent loop. If Jules is not suitable for a specific task, use `delegate_task` to spawn subagents.

- Jules limits: 100 tasks/day, max 15 parallel, 10 connected repos
- API: `sourceContext{source:"sources/github/o/r",githubRepoContext:{startingBranch}}`

---

## Project Overview

Single-file HTML5 Canvas Snake game (`index.html`) with CSS theme system, localStorage persistence, Hebrew/RTL i18n, and extensible per-theme drawing logic. Zero dependencies.

**Repo:** `tomguy91/snake-game`  
**Deploy:** GitHub Pages from `master` branch  
**Language:** English UI code, Hebrew user-facing strings, RTL layout support

---

## Architecture

```
index.html (~1345 lines)
│
├─ <style>                      CSS custom properties on :root
│   ├── .theme-classic          Green LCD retro palette
│   ├── .theme-kids             Bright green+orange, rounded segments
│   ├── .theme-spiderman        Dark red/blue, white text
│   └── html[dir="rtl"]         Direction overrides
│
├─ <div class="phone-bezel">    Visual phone frame
│   ├── .brand                  Title (dynamic via JS)
│   ├── .screen-container       LCD screen area
│   │   ├── .header             Score + High Score
│   │   ├── <canvas>            Game rendering
│   │   ├── .overlay            Game-over screen
│   │   ├── #mainMenu           Menu overlay (active on load)
│   │   └── #leaderboardScreen  Leaderboard overlay
│   └── .controls               D-pad buttons
│
└─ <script>                     All game logic (~900 lines)
    ├── THEME_DEFS              Theme registry (see below)
    ├── STR                     i18n strings (en + he)
    ├── state                   Central game state
    ├── Theme functions         applyTheme(), themeName()
    ├── i18n functions          t(), setLanguage(), updateAllUI()
    ├── Game engine             initGame(), gameLoop(), update(), draw()
    ├── Leaderboard             loadLeaderboard(), saveToLeaderboard(), isTop10()
    ├── Input handling          Keyboard, D-pad, swipe, visibility
    └── Init                    On-load setup
```

---

## Theme System

Each theme is an entry in `THEME_DEFS` with optional custom drawing functions. If a theme **does not** provide a function, the default CSS-variable-based rendering is used.

### Theme Object Shape

```javascript
THEME_DEFS = {
  themeKey: {
    name: { en: 'English Name', he: 'שם בעברית' },
    emoji: '🎨',                    // shown before name in menu button; '' for none
    cssClass: 'theme-themekey',     // CSS class applied to <body>; '' for :root default
    headShape: 'descriptor',        // metadata only, for future use (not yet used by engine)

    // Optional drawing overrides:
    drawSegment(ctx, segment, index, snake, cellSize) { ... },
    drawFood(ctx, food, cellSize) { ... },
    drawBackground(ctx, gridSize, cellSize) { ... },
  }
}
```

### How `draw()` Dispatches

```javascript
function draw() {
    const def = THEME_DEFS[state.theme];

    // Step 1: Clear canvas with theme background color
    ctx.fillStyle = getCSS('--bg');
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Step 2: Theme background (grid dots, stars, etc.)
    if (def && def.drawBackground) {
        def.drawBackground(ctx, state.gridSize, CELL_SIZE);
    }

    // Step 3: Each snake segment
    state.snake.forEach((segment, idx) => {
        if (def && def.drawSegment) {
            def.drawSegment(ctx, segment, idx, state.snake, CELL_SIZE);
        } else {
            // Default: solid colored rectangle
            ctx.fillStyle = getCSS('--snake');
            ctx.fillRect(segment.x * CELL_SIZE, segment.y * CELL_SIZE, CELL_SIZE - 1, CELL_SIZE - 1);
        }
    });

    // Step 4: Food (only if valid position)
    if (state.food.x >= 0 && state.food.y >= 0) {
        if (def && def.drawFood) {
            def.drawFood(ctx, state.food, CELL_SIZE);
        } else {
            ctx.fillStyle = getCSS('--food');
            ctx.fillRect(state.food.x * CELL_SIZE + 2, state.food.y * CELL_SIZE + 2, CELL_SIZE - 5, CELL_SIZE - 5);
        }
    }
}
```

### `drawSegment` Parameters

| Param | Type | Description |
|-------|------|-------------|
| `ctx` | CanvasRenderingContext2D | The 2D canvas context |
| `segment` | `{x, y}` | Grid coordinates of this segment |
| `index` | `number` | **0 = head**, 1+ = body |
| `snake` | `Array<{x,y}>` | Full snake array (use `snake[0]` and `snake[1]` to determine direction) |
| `cellSize` | `number` | Current CELL_SIZE in pixels |

### Determining Snake Direction in `drawSegment`

```javascript
// Get direction from first two segments
const dir = snake.length >= 2
    ? { x: snake[0].x - snake[1].x, y: snake[0].y - snake[1].y }
    : { x: 1, y: 0 };

const faceRight = dir.x > 0 || (dir.x === 0 && dir.y !== 0);
// Use faceRight to orient head features (eyes, etc.)
```

---

## Adding a New Theme

### Step 1: CSS class
```css
.theme-newtheme {
    --bg: #...;
    --snake: #...;
    --food: #...;
    --grid-line: #...;
    --screen-border: #...;
    --container-bg: #...;
    --text: #...;
    --bezel: #...;
    --brand-text: #...;
    --overlay-bg: rgba(...);
    --font: 'Font Name', fallback;
    --snake-radius: Npx;
}
```

### Step 2: THEME_DEFS entry
Add to the `THEME_DEFS` object in the `<script>`:
```javascript
newtheme: {
    name: { en: 'New Theme', he: 'שם חדש' },
    emoji: '🆕',
    cssClass: 'theme-newtheme',
    drawSegment(ctx, seg, idx, snake, CS) { /* custom drawing */ },
    drawFood(ctx, food, CS) { /* custom food */ },
    drawBackground(ctx, gridSize, CS) { /* custom background */ },
}
```

### Step 3: Menu button
In `#themeButtons` div, add:
```html
<button class="menu-btn" data-theme="newtheme">🆕 New Theme</button>
```
The button text is auto-updated by `updateAllUI()` on language change — include the emoji and English name as the default.

### Step 4: Test
- Open `index.html` in browser
- Select theme from menu → verify rendering
- Switch language → verify theme name translates
- Play a game → verify no rendering glitches
- Check all grid sizes (S/M/L) work with the theme

---

## i18n / Language System

### Adding new strings
Add key to both `STR.en` and `STR.he`:
```javascript
const STR = {
    en: {
        myNewKey: 'Hello World',
    },
    he: {
        myNewKey: 'שלום עולם',
    }
};
```

### Using strings in code
```javascript
// In JS:
element.textContent = t('myNewKey');

// In HTML (static, updated by updateAllUI):
<button id="myBtn">DEFAULT TEXT</button>
// Then in updateAllUI():
document.getElementById('myBtn').textContent = t('myNewKey');
```

### Language state
- Stored: `state.language` + `localStorage.snakeLanguage`
- Toggle: `setLanguage('en')` / `setLanguage('he')`
- RTL: `document.documentElement.setAttribute('dir', 'rtl'/'ltr')`
- Font: `--font-rtl` CSS variable set to `'Heebo'` for Hebrew

### Theme name translation
`themeName(themeKey)` → returns the translated name from `THEME_DEFS[themeKey].name[lang]`.

---

## localStorage Keys

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `snakeTheme` | string | `'classic'` | Active theme key |
| `snakeGridSize` | number | `20` | Grid dimension (15/20/25) |
| `snakeSpeed` | number | `150` | Base tick ms (200/150/100) |
| `snakeHighScore` | number | `0` | All-time high score |
| `snakeLanguage` | string | `'en'` | UI language (`'en'`/`'he'`) |
| `snakeLeaderboard` | JSON array | `[]` | Top 10 entries |

---

## Conventions

1. **Single file only** — `index.html` contains everything. No separate CSS/JS files, no CDN deps except Google Fonts.
2. **No build step** — open in browser, it just works. No npm, no bundler, no TypeScript.
3. **localStorage for persistence** — keys above. Always use try/catch for storage calls.
4. **CSS custom properties** — color/font values go in `:root` or `.theme-*` classes. Never hardcode colors in JS unless it's theme-specific canvas drawing.
5. **Canvas rendering** — `draw()` is the single render function. Theme overrides go in `THEME_DEFS`, not in `draw()` itself.
6. **Game state** — centralized in `state` object. No global variables for mutable game data.
7. **`CELL_SIZE`** — global variable recalculated on `resize()`. Depends on `state.gridSize` and window dimensions.
8. **Food hidden state** — when `state.food.x === -1 && state.food.y === -1`, food is not drawn. Used on initial menu screen.
9. **Speed formula** — `state.currentSpeed = Math.max(70, state.baseSpeed - Math.floor(state.score / 50) * 5)`. Minimum tick is 70ms.

---

## Testing

No test framework. Manual testing checklist:

- [ ] All 3 themes render correctly (menu → select → verify canvas)
- [ ] Language toggle works (EN ↔ עב, all UI text updates)
- [ ] RTL layout correct in Hebrew (text alignment, menu direction)
- [ ] Play a game in each theme (Classic, Kids, Spider-Man)
- [ ] Game over → score save flow (top 10 qualification)
- [ ] Leaderboard: view, save, clear
- [ ] Grid sizes: S (15), M (20), L (25) all render correctly
- [ ] Speeds: Slow, Normal, Fast all work
- [ ] D-pad buttons work
- [ ] Keyboard (arrows + WASD) works
- [ ] Swipe on canvas works on mobile
- [ ] Settings persist across page reload
- [ ] Visibility change pauses/resumes game
- [ ] Responsive: resize browser, verify canvas scales
