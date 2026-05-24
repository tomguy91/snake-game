# Snake Game — Plan

> Implementation plan and feature history.  
> **For AI agents:** read this + [AGENTS.md](./AGENTS.md) before coding.

---

## v1 — Nokia Classic (baseline)

- HTML5 Canvas snake game
- Green LCD retro aesthetic, phone bezel frame
- Keyboard (arrows + WASD), swipe, D-pad controls
- Speed progression, high score in localStorage

---

## v2 — Menu, Themes (Classic + Kids), Difficulty, Leaderboard

**Status:** ✅ Complete

### Features Added
1. **Main Menu** — overlay with Play, Theme, Difficulty, Leaderboard
2. **Theme System v1** — CSS custom properties on `.theme-classic` / `.theme-kids`
3. **Difficulty** — Grid size (S/M/L) + Speed (Slow/Normal/Fast), persisted to localStorage
4. **Leaderboard** — Top 10 with name entry, clear button
5. **Kids Theme Polish** — rounded segments, emoji food, Fredoka One font, grid dots

### Architecture (v2)
- CSS-only theming via custom properties
- Theme switching: `document.body.classList.add/remove`
- Drawing: `if (state.theme === 'kids')` branching in `draw()`

---

## v3 — Extensible Theme System, Spider-Man, Hebrew/RTL

**Status:** ✅ Complete

### Features Added
1. **THEME_DEFS registry** — each theme is an object with optional `drawSegment()`, `drawFood()`, `drawBackground()` overrides
2. **Spider-Man Theme** — custom canvas drawing:
   - Head: red mask with directional white almond eyes + web lines
   - Body: alternating red (web-patterned) + blue (every 4th) segments
   - Food: drawn spider web (8 spokes + concentric rings)
   - Background: dark night with subtle stars
3. **Hebrew/RTL i18n** — language toggle (EN/עב), all UI text translated, `dir="rtl"`, Heebo font
4. **Kids theme upgrade** — snake head now has cute eyes
5. **Theme buttons** — labels update dynamically on language change

### Architecture Change (v2 → v3)
```
Before: CSS variables only, draw() with if/else per theme
After:  THEME_DEFS objects with draw hooks, draw() dispatches to theme functions
```

### Files Changed
| File | Action |
|------|--------|
| `index.html` | Major refactor (902 → 1345 lines): theme system, Spider-Man drawing, i18n |
| `README.md` | Updated with v2+v3 features, architecture section |
| `AGENTS.md` | **New** — agent conventions, theme system docs, testing checklist |

---

## Future Ideas

- More themes: realistic snake, ninja, robot, Minecraft, Pokémon
- Sound effects (Web Audio API)
- Power-ups (speed boost, invincibility, shrink)
- Obstacles / maze mode
- Two-player mode
- Online leaderboard (Firebase/supabase)
