# 🐍 Snake Game — Nokia Classic +

A faithful tribute to the iconic Nokia 3310 Snake, rebuilt as a single self-contained HTML file — now with multiple themes, custom snake shapes per theme, difficulty settings, leaderboard, and Hebrew/RTL support.

> "The game that made waiting for a text message fun." — now with Spider-Man 🕷️

---

## 🎮 Demo

Open `index.html` in any browser — no server, no build step, no dependencies.

---

## 🕹️ How to Play

| Action | Desktop | Mobile |
|--------|---------|--------|
| Move Up | ↑ Arrow or W | Swipe up / ▲ button |
| Move Down | ↓ Arrow or S | Swipe down / ▼ button |
| Move Left | ← Arrow or A | Swipe left / ◀ button |
| Move Right | → Arrow or D | Swipe right / ▶ button |
| Restart | Space | Tap "Play Again" |

- Eat food to grow the snake and earn points (+10 per item).
- Avoid walls and your own tail — collision means game over.
- The snake speeds up as your score increases.

---

## ✨ Features

### 🎨 Themes (v3)
Three themes, each with complete visual control — not just colors, but custom snake shapes, food, and backgrounds:

| Theme | Snake | Food | Background |
|-------|-------|------|------------|
| **Classic** | Green square blocks 🟩 | Dark square | Solid retro LCD green |
| **Kids** 👶 | Rounded green segments + cute eyes on head | 🍎 Apple emoji | Grid dots pattern |
| **Spider-Man** 🕷️ | Red mask head with directional white eyes, alternating red web-patterned + blue body segments | 🕸️ Drawn spider web (spokes + concentric rings) | Dark night with stars |

Adding a new theme takes ~20 lines of drawing code — see [AGENTS.md](./AGENTS.md#theme-system).

### ⚙️ Difficulty
- **Grid Size:** Small (15×15) / Medium (20×20) / Large (25×25)
- **Speed:** Slow (200ms) / Normal (150ms) / Fast (100ms)
- Settings saved to `localStorage`

### 🏆 Leaderboard
- Top 10 scores with name entry
- Tracks: player name, score, theme used, difficulty, date
- Name prompt only appears when score qualifies for top 10
- Clear button with confirmation

### 🌐 Language (v3)
- Full Hebrew/RTL support — toggle between English and Hebrew
- All UI text translated (menus, buttons, leaderboard, game over)
- RTL layout (`dir="rtl"`) with Heebo Hebrew font
- Theme names display in current language

### 📱 Mobile-first
- Touch D-pad, swipe controls, keyboard (arrows + WASD)
- Responsive canvas — scales to screen size
- Auto-pause when tab loses visibility

### 🔇 Zero dependencies
- Everything in one HTML file (1345 lines)
- No frameworks, no build step, no server needed
- Works offline

---

## 🚀 How to Run

```bash
git clone https://github.com/tomguy91/snake-game.git
cd snake-game
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or just drag `index.html` into any browser.

---

## 🛠️ Tech Stack

- **HTML5 Canvas** — game rendering + custom theme drawing
- **Vanilla JavaScript** — game engine, input handling, i18n, theme system
- **CSS3 Custom Properties** — theme colors, fonts, transitions
- **Google Fonts** — Fredoka One (Kids), Heebo (Hebrew)
- **No build tools, no frameworks, no dependencies**

---

## 🏗️ Architecture

```
index.html (single file)
├── <style>          CSS custom properties + theme classes + RTL
├── <div>            Phone bezel, screen, canvas, overlays, D-pad
└── <script>
    ├── THEME_DEFS   Theme objects with drawSegment/drawFood/drawBackground
    ├── STR          i18n strings (en + he)
    ├── state        Game state + localStorage config
    ├── draw()       Theme-aware renderer
    ├── gameLoop()   Classic Snake tick → update → draw
    └── UI handlers  Menus, leaderboard, language toggle
```

For AI agent conventions and how to extend, see [AGENTS.md](./AGENTS.md).

---

## 📄 License

MIT — fork, remix, and share freely.

---

*Made with 💚, 90s nostalgia, and a bit of Spider-Man.*
