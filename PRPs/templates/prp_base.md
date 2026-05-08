# PRP Base Template — EDGE Journal

> This template is for implementing features in the EDGE trading journal (vanilla HTML/CSS/JS, single-file app).
> Fill every section before implementation. Incomplete context = broken output.

---

## Goal

**Feature:** [One-line description]

**Outcome:** [What the user can do after this is implemented, and what it looks like]

**Page(s) affected:** [e.g., `page-history`, `page-dashboard`, or "login screen"]

**Files to edit:** `C:\Users\Kory\Projects\index.html` (always — it's the only file)

---

## Context

### Read First
- `PLANNING.md` — architecture, state shape, key functions, constraints
- `CLAUDE.md` — design system, terminal aesthetic rules
- `TASK.md` — confirm this task is listed and marked in-progress

### Relevant Existing Patterns

List the specific functions, CSS classes, and HTML structures in `index.html` to reference:

| What | Where in index.html | Why relevant |
|---|---|---|
| [function name] | [approx line or grep term] | [what pattern to follow] |
| [CSS class] | [style block] | [layout/visual pattern to match] |
| [HTML structure] | [page ID or component] | [DOM pattern to replicate] |

### External Documentation

- [URL if any external API/library is involved]

### Known Gotchas for This Feature

- [ ] Quick-mode trades: always check `t.quickMode` before accessing `t.entry`/`t.exit`/`t.sl`/`t.tp`
- [ ] P&L: always use `calcPnL(t)`, never compute inline
- [ ] Navigation: always call `go(page)`, never toggle `.active` directly
- [ ] CSS: new rules go at the END of the `<style>` block (later = higher precedence)
- [ ] AI calls: keep prompts in JS, `/api/chat.js` is a thin proxy
- [ ] Dates: always EST, never convert to UTC in UI
- [Add feature-specific gotchas here]

---

## Implementation Blueprint

### Data / State Changes

```javascript
// Describe any new properties added to state, or new data shapes
// e.g.:
state.newFeature = {
  field: defaultValue,
};
```

### HTML Structure (pseudocode)

```html
<!-- Describe the DOM structure to add/modify -->
<!-- Reference an existing similar structure in the file -->
<div class="[existing-class]">
  <!-- new elements here -->
</div>
```

### CSS Classes Needed

```css
/* List new CSS classes — these go at the END of the <style> block */
/* Must follow terminal aesthetic: dark bg, purple accent, no new colors */
.new-component {
  background: var(--bg2);
  border: 1px solid var(--border);
  /* ... */
}
.new-component:hover {
  border-color: rgba(155,127,255,0.28);
  box-shadow: 0 0 0 1px rgba(155,127,255,0.10), 0 4px 28px rgba(155,127,255,0.10);
}
```

### JavaScript Logic (pseudocode)

```javascript
// Step-by-step pseudocode — be specific enough that there's no ambiguity
// Reference real function names from index.html

function renderNewFeature() {
  // 1. Get relevant trades from state
  const trades = state.trades.filter(/* condition */);
  
  // 2. Calculate needed values
  const result = trades.map(t => calcPnL(t));
  
  // 3. Build HTML string
  const html = `<div class="...">...</div>`;
  
  // 4. Inject into the right container
  document.getElementById('container-id').innerHTML = html;
}
```

### Ordered Task List

Implement in this exact order:

1. [ ] Add new HTML structure to the correct `<div class="page" id="page-X">` section
2. [ ] Add CSS classes at the end of the `<style>` block
3. [ ] Add/update JavaScript render function
4. [ ] Wire up the render call in `go()` if it's a new page, or in the parent render function
5. [ ] Wire up any event listeners (use event delegation where possible)
6. [ ] Test: open in browser, navigate to the page, verify no console errors
7. [ ] Test: edge cases (empty state, single trade, quickMode trades)
8. [ ] Mark task complete in `TASK.md`

---

## Validation Gates

Run these checks before marking the feature done:

### 1. No JS Errors
Open browser DevTools console. Navigate to the feature. Confirm zero errors and zero warnings (except pre-existing ones).

### 2. Visual Check
- [ ] Background is `#0a0a0b` or `--bg` — no white/light areas
- [ ] All numbers use `font-family: var(--mono)` (Geist Mono)
- [ ] Positive values: `color: var(--green)` with neon text-shadow
- [ ] Negative values: `color: var(--red)` with neon text-shadow  
- [ ] Cards glow on hover with purple border + box-shadow
- [ ] Page transition animates in (blur + translateY)
- [ ] No colors outside the design system palette

### 3. State / Data Check
- [ ] Feature works with 0 trades (empty state handled gracefully)
- [ ] Feature works with quickMode trades (`t.quickMode === true`)
- [ ] Feature works with both long and short trades
- [ ] P&L always calculated via `calcPnL(t)`, not inline

### 4. Navigation Check
- [ ] Can navigate away and back without breaking the feature
- [ ] `go()` function correctly renders/re-renders the page
- [ ] No stale DOM state from previous renders

### 5. Deploy Check
- [ ] Run `/deploy [description]` and confirm Vercel builds without error
- [ ] Check live URL — feature works in production

---

## Anti-Patterns to Avoid

- ❌ Don't hardcode hex colors — use CSS custom properties (`var(--accent)`, `var(--green)`, etc.)
- ❌ Don't manipulate `.page` visibility directly — always use `go()`
- ❌ Don't compute P&L inline — always use `calcPnL(t)`
- ❌ Don't add `background: white` or any light background to inner app pages
- ❌ Don't add new external JS libraries without discussing first (file size matters)
- ❌ Don't put AI prompts in `/api/chat.js` — they belong in the JS in `index.html`
- ❌ Don't use `innerHTML` for user-generated content without sanitization
- ❌ Don't forget to handle the empty state (no trades)

---

## Confidence Score

Rate the PRP completeness: **[ ] / 10**

A score of 8+ means this PRP has enough context to implement correctly in one pass.
Below 8 = go back and fill in missing context before starting.
