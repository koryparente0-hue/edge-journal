# EDGE — AI Trading Journal

## 🔄 Project Awareness & Context

- **Always read `PLANNING.md`** at the start of every new conversation to understand the current architecture, active decisions, and constraints before touching any code.
- **Check `TASK.md`** before starting any task. If the task isn't listed, add it with a brief description and today's date before beginning. Mark tasks completed immediately when done.
- **Never invent patterns.** All conventions — function naming, CSS class naming, state access, Firebase calls — must follow the patterns already established in `index.html`. When in doubt, grep for an existing example.
- **The working source file is `C:\Users\Kory\Projects\index.html`.** All edits happen there. Use `/deploy` to copy it to this repo and push to Vercel.

---

## Project

Single-file trading journal web app. The entire application lives in `index.html` — all HTML, CSS, and JavaScript in one file. No build step. No framework.

**GitHub repo:** `koryparente0-hue/edge-journal`  
**Deploy:** Vercel auto-deploys from `main` branch via `git push origin main`  
**API:** `/api/chat.js` and `/api/webhook.js` (Vercel serverless functions in the `api/` folder)

---

## Tech Stack

- **HTML/CSS/JS** — vanilla, no framework, no bundler
- **Single-file architecture** — `index.html` contains all pages, all styles, all logic
- **Vercel** for hosting + serverless API routes
- **Firebase** for auth and data persistence (Google Sign-In)
- **OpenAI API** via `/api/chat.js` for AI trade reviews and pattern analysis

---

## Design System

### Colors
| Token | Value | Use |
|---|---|---|
| `--bg` | `#0a0a0b` | App shell background |
| `--bg2` | `#111113` | Card / sidebar / panel backgrounds |
| `--bg3` | `#18181c` | Input fields, secondary surfaces |
| `--bg4` | `#1e1e24` | Hover states on surfaces |
| `--accent` | `#9B7FFF` | Primary purple — CTAs, active states, highlights |
| `--green` | `#00d084` | Positive P&L, wins, A-grade |
| `--red` | `#ff4d4d` | Negative P&L, losses, mistakes |
| `--amber` | `#f5a623` | Warnings, caution states |
| `--blue` | `#4a9eff` | Secondary info, B-grade |
| `--text` | `#EEEEF2` | Primary text |
| `--muted` | `#9898A8` | Secondary / label text |
| `--dim` | `#4A4A5A` | Disabled / placeholder text |

Hero / login background: `#080810` (slightly deeper than `--bg`)

### Typography
- **Display / UI:** `Geist` (weights 300–700)
- **Monospace / data:** `Geist Mono` (weights 400–600)
- **Landing page headings:** `Montserrat` (800)
- **Logo:** `Syne` (800)

### Spacing & Radii
- `--r: 8px` · `--r2: 12px` · `--r3: 16px`
- Page padding: `24px` (desktop), `16px` (mobile)
- Card gaps: `10–16px`

---

## Terminal Aesthetic — Non-Negotiable Rules

All inner app pages (dashboard, trades, stats, playbook, AI coach, daily routine) **must** maintain the futuristic terminal aesthetic:

1. **Background:** `#0a0a0b` with an animated dot-grid overlay (28px grid, `rgba(155,127,255,0.06)` dots, pulsing via `@keyframes termDotPulse`). Scanline texture at 2.2% opacity. No gradients, no blobs, no images as backgrounds.

2. **Cards & panels:** Dark `--bg2` fill with `1px solid var(--border)` edge. On hover: `rgba(155,127,255,...)` border glow + layered `box-shadow`. No card should have a bright or colorful background.

3. **Data values:** Green (`#00d084`) for positive numbers, red (`#ff4d4d`) for negative. Both use multi-layer `text-shadow` neon glow. Never use color alone — always pair with sign (+/−).

4. **Accent color:** Purple (`#9B7FFF`) only. No random color splashes. Green and red are reserved for P&L data only.

5. **Typography:** Monospace (`Geist Mono`) for all numbers. Sans (`Geist`) for labels and copy. Never serif.

6. **Page transitions:** `@keyframes pageEnter` — fade + translateY + blur-in over 280ms.

7. **No green anywhere** except positive P&L values and the A-grade badge. The login hero background is `#080810` with the particle canvas.

---

## Login Screen — Particle Canvas

The login/landing screen (`#login-screen`) has a full-viewport interactive canvas (`#heroBurst`) that renders:

- 130 floating purple particles with varying speeds and sizes
- Connection lines between particles within 130px proximity (brighten near cursor)
- Mouse hover: particles pull toward cursor with spring-back damping
- Click burst: radial impulse scatters particles, they drift back naturally
- Radial purple glow follows the mouse
- 28px dot-grid rendered to an offscreen canvas for performance
- 8% of particles pulse/flash like trading signals
- Canvas resizes with `window.resize`, wraps particles at edges
- Runs at 60fps via `requestAnimationFrame`

**Do not replace or simplify this animation.** It is a core part of the brand.  
The function is `initParticles()` called via `window._initBlob` or directly after a `setTimeout(50ms)` on page load.

---

## App Structure

```
index.html          ← entire app (HTML + CSS + JS, ~380KB)
api/
  chat.js           ← OpenAI proxy (serverless)
  webhook.js        ← webhook handler (serverless)
vercel.json         ← routing config
```

### Pages (all inside `index.html`)
| Page ID | Nav label | Function |
|---|---|---|
| `page-dashboard` | Dashboard | Equity curve, metrics, pattern feed, copilot |
| `page-log` | Log Trade | Quick-entry form |
| `page-history` | History | Trade table + calendar view |
| `page-setups` | Setups | Setup performance analysis |
| `page-copilot` | AI Copilot | Chat + live analysis |
| `page-routine` | Daily Routine | Pre/post session checklists |
| `page-risk` | Risk / Tilt | Risk meter, mistake analytics |
| `page-streaks` | Streaks | Habit tracking |
| `page-insights` | Insights | Pattern analysis |
| `page-playbook` | Playbook | Strategy cards |

Navigation is handled by `go(page)` which toggles `.active` on `.page` elements.

---

## Development

No dev server needed — open `index.html` directly in a browser, or use a simple static server:

```bash
npx serve .        # or python -m http.server
```

To deploy: commit `index.html` and push to `main`. Vercel picks it up automatically.

```bash
git add index.html
git commit -m "your message"
git push origin main
```

---

## Working File

The source of truth being actively edited is at:
```
C:\Users\Kory\Projects\index.html
```

Deploy copies it to `C:\projects\edge-journal\index.html` before pushing.
