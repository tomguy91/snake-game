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

---

## v3 — Extensible Theme System, Spider-Man v1, Hebrew/RTL

**Status:** ✅ Complete

### Features Added
1. **THEME_DEFS registry** — drawSegment(), drawFood(), drawBackground() hooks
2. **Spider-Man v1** — red mask head (ellipse eyes + thin webs), red/blue body with X web pattern, spider-web food drawing, dark starfield
3. **Hebrew/RTL i18n** — EN/עב toggle, Heebo font, dir=rtl
4. **Kids theme upgrade** — cute eyes on snake head
5. **AGENTS.md** — agent conventions + theme system docs

---

## v3.5 — Spider-Man Visual Upgrade (planned)

**Goal:** Make the Spider-Man theme actually look like Spider-Man, not just red/blue colors.

### Tasks

#### Task A: Spider-Man Head Sprite (base64 PNG)
- Create a 20×20 or 24×24 pixel-art Spider-Man mask as a data URI (base64 PNG)
- Draw it using an offscreen canvas: red mask, white angular eyes (McFarlane style), black outlines, web lines, tiny spider emblem
- Embed the data URI directly in the HTML (no external files)
- Use `ctx.drawImage()` in `drawSegment()` for the head instead of canvas primitives
- Eyes should face the direction of movement (rotate/flip the sprite based on direction)

#### Task B: Enhanced Body Costume
- Improve the web pattern on red body segments: proper radial web (not just X), thicker lines (1px), more visible
- Add a tiny spider emblem on segments 1-3 (the "chest" area, just behind the head)
- Blue segments: add subtle web pattern too (the blue parts of Spider-Man's costume also have webs in most versions)
- Consider adding a "web trail" effect behind the snake tail (fading dots/lines)

#### Task C: Environmental Theming
- **NYC skyline silhouette** — draw simple building outlines at the bottom 15% of the game canvas in dark gray
- **Corner webs** — small spider web decorations in 2-4 corners of the game area
- **Moon** — a subtle white/yellow circle in one corner of the sky
- **Food change** — use 🕷️ spider emoji instead of the drawn web (simpler, more recognizable)
- Add subtle "web strands" that connect between corners occasionally

### Implementation Notes
- All changes in `index.html` only (single-file constraint)
- Use `THEME_DEFS.spiderman.drawBackground()` for skyline + corner webs + moon
- Use `THEME_DEFS.spiderman.drawSegment()` for sprite head + enhanced body
- Use `THEME_DEFS.spiderman.drawFood()` for spider emoji food
- The head sprite should be generated programmatically (offscreen canvas → toDataURL → base64 string)
- Test at all three grid sizes (15/20/25) to ensure head sprite scales well
