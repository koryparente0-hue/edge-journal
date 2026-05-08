# EDGE — Architecture & Planning

> Read this at the start of every session. It is the single source of truth for how this project is structured and why decisions were made.

---

## Project Goal

EDGE is an AI-powered trading journal for NQ/MNQ futures traders. It grades every trade A–F using OpenAI, surfaces hidden patterns in trading behavior, and acts as a coach — not just a logger. The target user is a retail futures trader who scalps the NY session using ICT methodology.

**Core loop:** Log trade → AI grades it → patterns emerge → trader improves.

---

## Architecture

### Single-File App

The entire frontend is **one file: `index.html`** (~380KB). All HTML pages, all CSS, all JavaScript live in this file. There is no build step, no bundler, no framework.

**Why:** Simplicity. The app can be opened directly in a browser, deployed with zero config, and edited without tooling. The tradeoff is that the file is large — work carefully within it.

**Working copy:** `C:\Users\Kory\Projects\index.html`  
**Deployed copy:** `C:\projects\edge-journal\index.html` (synced via `/deploy`)

### Page Routing

All pages exist in the DOM simultaneously as `<div class="page" id="page-{name}">`. Only one has the `.active` class at a time.

```javascript
function go(page) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  const pageEl = document.getElementById('page-' + page);
  if (pageEl) pageEl.classList.add('active');
  // page-specific render calls follow...
}
```

Page transition: `@keyframes pageEnter` — fade + translateY + blur-in, 280ms.

### State Management

All runtime data lives in a single `state` object (module-level in the script). There is no Redux, no signals, no reactivity library — just direct mutation and manual re-renders.

Key state shape:
```javascript
const state = {
  user: null,           // Firebase auth user object
  trades: [],           // Array of trade objects (see Trade Schema below)
  routines: [],         // Daily routine entries
  playbooks: [],        // Strategy/playbook cards
  setupLibrary: [],     // User-defined setups
  settings: {},         // User preferences
}
```

State is persisted to **Firestore** and loaded on auth. Local mutations call a `save()` or equivalent to sync back.

### Serverless API

Two Vercel functions in `/api/`:
- **`chat.js`** — OpenAI proxy. Receives a prompt, returns AI response. Used for trade grading, pattern analysis, and the AI Copilot chat.
- **`webhook.js`** — Stripe/payment webhook handler.

---

## Data Schema

### Trade Object
```javascript
{
  id: string,           // UUID
  ticker: string,       // "NQ", "MNQ", "ES", "MES"
  side: "long"|"short",
  entry: number,
  exit: number,
  sl: number,           // Stop loss price
  tp: number,           // Take profit price
  qty: number,          // Contracts
  fees: number,
  datetime: string,     // ISO string, EST
  setups: string[],     // e.g. ["Mech Model", "Continuation"]
  mistakes: string[],   // e.g. ["FOMO", "Early exit"]
  notes: string,
  grade: string,        // "A"|"B"|"C"|"D"|"F" — AI assigned
  gradeReason: string,  // AI explanation
  quickMode: boolean,   // If true, use quickPnL instead of calculated
  quickPnL: number,
  quickRR: string,
  photos: string[],     // Firebase Storage URLs
}
```

### P&L Calculation
```javascript
function calcPnL(t) {
  if (t.quickMode && t.quickPnL !== undefined) return t.quickPnL - (t.fees || 0);
  const m = { NQ: 20, MNQ: 2, ES: 50, MES: 5 }; // point values
  const mult = m[t.ticker?.toUpperCase()] || 1;
  const dir = t.side === 'long' ? 1 : -1;
  return dir * (t.exit - t.entry) * mult * (t.qty || 1) - (t.fees || 0);
}
```

---

## Pages Inventory

| Page ID | Nav label | Render function | Purpose |
|---|---|---|---|
| `page-dashboard` | Dashboard | `renderDashboard()` | Equity curve, metrics, pattern feed, copilot |
| `page-log` | Log Trade | `renderLogPage()` | Quick-entry trade form |
| `page-history` | History | `renderHistory()` | Trade table + calendar view |
| `page-setups` | Setups | `renderSetupsPage()` | Setup performance breakdown |
| `page-copilot` | AI Copilot | `renderCopilotPage()` | Chat interface + live analysis |
| `page-routine` | Daily Routine | `renderRoutinePage()` | Pre/post session checklists |
| `page-risk` | Risk / Tilt | `renderRisk()` + `renderMistakeAnalytics()` | Risk meter, mistake tracking |
| `page-streaks` | Streaks | `renderStreaks()` | Habit streak tracking |
| `page-insights` | Insights | *(inline)* | Pattern analysis cards |
| `page-playbook` | Playbook | *(inline)* | Strategy documentation |
| `page-setuplibrary` | Setup Library | `renderSetupLibrary()` | User-defined setup catalog |
| `page-propfirms` | Prop Firms | `renderPropFirmsPage()` | Prop firm tracking |

---

## CSS Architecture

All styles are in the `<style>` block inside `index.html`. No external stylesheet. No CSS modules.

**Class naming:** BEM-lite. Component prefix + element, e.g. `.routine-block`, `.routine-q`, `.routine-label`. No utility classes except for layout helpers like `.grid2`, `.span2`.

**CSS custom properties** (defined in `:root`): All colors, radii, shadows, fonts. Always use these — never hardcode color hex values inside component CSS.

**Key class groups:**
- Layout: `.shell`, `.sidebar`, `.main`, `.page`, `.card`, `.metric`, `.grid2`, `.grid3`
- Data display: `.metric-val.pos`, `.metric-val.neg`, `.pnl-pos`, `.pnl-neg`, `.grade-A` through `.grade-F`
- Interactive: `.btn`, `.btn-green`, `.btn-sm`, `.btn-red`
- Table: `.trades-table`, `.side-pill`, `.long-pill`, `.short-pill`
- Forms: `.qe-field`, `.modal-field`, `.filter-input`
- Terminal FX: `body::before` (dot grid), `body::after` (scanlines), `@keyframes termDotPulse`, `@keyframes pageEnter`

---

## Firebase

- **Auth:** Google Sign-In via Firebase Auth
- **Database:** Firestore — each user's data is isolated under their UID
- **Storage:** Firebase Storage — trade screenshots/photos
- **Config:** Embedded directly in `index.html` (not in env vars — this is a frontend-only config, keys are safe to expose)

Firestore collection structure:
```
users/{uid}/
  trades/{tradeId}     → trade objects
  routines/{date}      → daily routine entries
  settings/prefs       → user preferences
  playbooks/{id}       → strategy cards
```

---

## Vercel Deployment

- **Trigger:** Any push to `main` on `koryparente0-hue/edge-journal`
- **Config:** `vercel.json` — routes `/api/*` to serverless functions, everything else to `index.html`
- **Env vars:** Set in Vercel dashboard (OpenAI key, Stripe key, Firebase service account for server-side if needed)
- **Deploy time:** ~30 seconds from push to live

**Deploy flow:**
1. Edit `C:\Users\Kory\Projects\index.html`
2. Run `/deploy` (or manually copy + `git push origin main`)
3. Vercel auto-deploys

---

## Key Constraints & Gotchas

1. **Single file — no imports.** Everything must be in `index.html`. No `import`, no `require`, no `<script src>` for local files (CDN links are fine).

2. **No build step.** What you write is what runs. No transpilation. Use browser-native JS (ES2020+). No TypeScript.

3. **File size matters.** The file is already ~380KB. Before adding a large chunk of code, consider if it can be done more concisely. Avoid copying large blocks unnecessarily.

4. **CSS specificity is flat.** Since all CSS is in one block, later rules win over earlier ones. Put overrides at the end of the `<style>` block.

5. **State resets on page reload.** There's no service worker / offline cache. Firebase reloads data on auth. Don't assume state is preserved across sessions unless it's in Firestore.

6. **The `go()` function is the only router.** Never manipulate `.page` visibility directly. Always call `go(pageName)` to navigate.

7. **Terminal aesthetic is non-negotiable.** See CLAUDE.md "Terminal Aesthetic" rules. Never add gradients, bright backgrounds, or colors outside the design system palette.

8. **Particle canvas must not be simplified.** The `initParticles()` animation on the login screen is a brand asset. Do not replace or reduce it.

9. **AI grading calls OpenAI via `/api/chat`.** Keep prompts in the JS, not hardcoded in the API route. The route is a thin proxy.

10. **Date/time is always EST.** Trades are logged in EST. The AI prompts note this explicitly. Don't convert to UTC in the UI.

---

## Style Conventions

- **Functions:** camelCase, verb-first — `renderDashboard()`, `calcPnL()`, `openTradeModal()`
- **Variables:** camelCase — `tradeId`, `pageEl`, `totalPnL`
- **IDs:** kebab-case — `page-dashboard`, `nav-dashboard`, `modal-review-0`
- **CSS classes:** kebab-case — `.metric-val`, `.pnl-pos`, `.chat-bubble-ai`
- **Constants:** camelCase (no SCREAMING_SNAKE — this is casual frontend JS)
- **Comments:** `// ─── SECTION NAME ────` for major sections, `//` for inline notes
- **No semicolons are optional** — use them consistently (file uses semicolons)
