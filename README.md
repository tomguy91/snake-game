# 🐍 Snake Game — Nokia Classic

A faithful tribute to the iconic Nokia 3310 Snake, rebuilt as a single self-contained HTML file. Play it on desktop or mobile — no installs, no build step, no dependencies. Just open and play.

> “The game that made waiting for a text message fun.”

---

## 🎮 Demo

*(Screenshot / GIF placeholder — coming soon)*

Open `index.html` in any browser to see it in action.

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

## 🚀 How to Run

No build tools. No server required. No dependencies.

```bash
# Clone or download the repo, then simply open the file
git clone https://github.com/tomguy91/snake-game.git
cd snake-game
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or drag `index.html` into any web browser. It works offline too.

---

## ✨ Features

- 🎯 **Classic Nokia gameplay** — grid-based movement, retro scoring, pure nostalgia
- 📱 **Mobile-first touch controls** — swipe on the canvas or use the on-screen D-pad
- ⌨️ **Keyboard support** — Arrow keys + WASD on desktop
- ⚡ **Speed progression** — game gets faster as your score climbs
- 🏆 **High score persistence** — saved to `localStorage`
- 🎨 **Nokia 3310 aesthetic** — green LCD palette, pixel styling, phone bezel frame
- 📐 **Responsive canvas** — scales to fit mobile screens up to ~400 px for an authentic feel
- 🔇 **Zero dependencies** — everything in one HTML file

---

## 🛠️ Tech Stack

- **HTML5 Canvas** — game rendering
- **Vanilla JavaScript** — game engine, input handling, game loop
- **CSS3** — Nokia LCD styling, responsive layout, touch-friendly UI
- **No build tools, no frameworks, no dependencies**

---

## 📄 License

MIT License — feel free to fork, remix, and share.

---

*Made with 💚 and a lot of 90s nostalgia.*
