# 🎨 Foreast — Design Document

## Vibe

Adventurous but calm. Like a quiet walk through a forest where you need to stay focused.
Think nature journal meets treasure map — earthy, a little tense, and oddly cosy.

---

## Colour Scheme

| Role | Name | Hex |
|---|---|---|
| Page background | Light green | `#e8f5e0` |
| Grass & foliage accents | Mid green | `#8fc98f` |
| Corridor outline / headings | Dark green | `#2e5f3e` |
| Deepest shadows / text | Deep green | `#1f4630` |
| Player heart | Heart green | `#3fae5c` |
| Thorn bushes (obstacle) | Deep magenta | `#b3123f` |
| Thorn bush stroke | Magenta dark | `#7e0c2c` |
| Canvas base | Cream | `#fbfaf3` |
| Win goal glow | Gold | `#d4af37` |

The palette is intentionally cool and earthy — nothing harsh or neon.
The only warm accent is the magenta, which acts as a natural danger signal (thorns!).

---

## Typography

- **Font stack:** `'Trebuchet MS', 'Segoe UI', sans-serif`
- **Heading (h1):** `2rem`, letter-spacing `1px`, dark green with a 1px white text-shadow for a subtle lift
- **Description (p):** italic, muted forest green `#3f6b4a`
- **HUD / status:** bold, inline with the lives display

No external fonts are loaded — the stack is system-safe and renders well on all devices.

---

## Visual Style

### Terrain (Canvas)
- **Corridor:** A multi-segment polyline drawn twice — a solid dark-green stroke (width + 6px) for the outline, then a semi-transparent green fill stroke on top, giving the path a hollow, map-like look.
- **Mountains:** Curved triangles using `quadraticCurveTo`, filled deep green — they sit inside the corridor and act as speed bumps.
- **Thorn bushes:** Spiky star-shapes (7-pointed) in deep magenta, placed at the corridor edges — visually read as "danger" instantly.
- **Grass:** Thin `quadraticCurveTo` strokes scattered outside the corridor, animated with a gentle sine-wave wobble over time.

### Player (Heart)
- Drawn with `bezierCurveTo` — a classic heart shape, solid heart-green with a dark stroke.
- Pulses subtly via a `scale` multiplier driven by `Math.sin(time)` — small enough to feel alive, not distracting.
- Flashes white briefly after taking damage (invincibility window).

### Goal
- A pulsing gold circle (`arc`) with a transparent fill — expands and contracts via `Math.sin(time)`.

### Trail
- Fading green dots left behind the heart as it moves, alpha decreasing each frame.

---

## Layout

```
[ h1: Foreast: Be careful! ]
[ p: description ]
[ HUD: ♥ ♥  |  status text ]
[ Stage: overflow:hidden ]
  └── Flex track (200% wide, slides left on end)
        ├── Panel 1: <canvas> (the game)
        └── Panel 2: message view (win / lose)
```

- The stage uses `overflow: hidden` with a flex container.
- On game end, `transform: translateX(-50%)` slides the canvas out and the message in, with a `cubic-bezier(.6,-0.1,.3,1.2)` spring feel.

---

## Animations

| Animation | Technique |
|---|---|
| Heart pulse | `Math.sin(time * 0.008)` scale in the draw loop |
| Grass sway | `Math.sin(time * 0.002 + offset)` per tuft |
| Goal glow | `Math.sin(time * 0.006)` radius offset |
| Damage flash | Invincibility timer + alternating white fill |
| Panel slide | CSS `transition: transform 0.9s cubic-bezier(...)` |
| Particles | Array of `{x,y,vx,vy,alpha,color}` objects, updated each frame |

---

## Interaction Model

- `mousedown` / `touchstart` → sets `isDrawing = true`, records position
- `mousemove` / `touchmove` → updates target position while drawing
- `mouseup` / `touchend` → sets `isDrawing = false`
- Each frame: heart moves toward mouse at `currentSpeed` px/frame, **clamped to the corridor** via `distToPath()`. If the new position exits the corridor, a wall-slide fallback tries x-only then y-only movement.

---

## Accessibility Notes

- Touch events fully supported (`touchstart`, `touchmove`, `touchend` with `passive: false`)
- Canvas scales responsively via CSS (`width: 100%; height: auto`)
- Colour contrast between corridor and grass is high enough for low-vision users
- Lose message avoids negative language — redirects to a paper-based alternative

---

*Made by Techature · https://sahanasview.vercel.app/*
