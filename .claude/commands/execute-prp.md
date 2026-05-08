# Execute PRP

Implement a feature in the EDGE journal using a PRP file as the specification.

## PRP File: $ARGUMENTS

---

## Execution Process

### 1. Load Context
- Read the specified PRP file in full
- Read `PLANNING.md` — architecture, constraints, existing patterns
- Read `CLAUDE.md` — terminal aesthetic rules (these are non-negotiable)
- Read `TASK.md` — mark the task as in-progress

### 2. Explore the Codebase
Open `C:\Users\Kory\Projects\index.html` and locate:
- The target page section (`id="page-X"`)
- The render functions listed in the PRP's "Relevant Existing Patterns" table
- The end of the `<style>` block (new CSS always goes here)
- The `go()` function (if wiring up a new page)
- Any state properties the feature reads/writes

Do not start writing code until you understand where every piece goes.

### 3. Plan
Before touching the file, write out the implementation plan:
- Which HTML goes where (exact container ID)
- Which CSS classes are new vs. reused
- Which function(s) to add and where they're called from
- The order of edits (HTML → CSS → JS → wiring)

### 4. Implement

Follow the PRP's Ordered Task List exactly:

1. Add HTML to the correct page section
2. Add CSS at the end of the `<style>` block — use `var(--*)` for all colors/fonts
3. Add JavaScript render function(s)
4. Wire up in `go()` or parent render function
5. Add event listeners (prefer event delegation on the page container)

**Terminal aesthetic checklist while writing:**
- [ ] All backgrounds use CSS variables, no hardcoded colors
- [ ] Numbers: `font-family: var(--mono)`
- [ ] Positive values: `color: var(--green)` + `text-shadow: 0 0 10px rgba(0,208,132,0.70), ...`
- [ ] Negative values: `color: var(--red)` + `text-shadow: 0 0 10px rgba(255,77,77,0.70), ...`
- [ ] Card hover: `border-color: rgba(155,127,255,0.28)` + purple `box-shadow`
- [ ] No white backgrounds, no bright colors, no gradients on inner pages

### 5. Validate

Run each gate from the PRP's Validation section. Fix failures before moving on.

**Gate 1 — No JS errors:**
Open browser DevTools on the affected page. Zero errors in console.

**Gate 2 — Visual:**
Visually confirm the terminal aesthetic is intact. Screenshot if helpful.

**Gate 3 — Data edge cases:**
Test with: no trades, 1 quickMode trade, mixed long/short trades.

**Gate 4 — Navigation:**
Navigate away from the page and back. Re-renders correctly, no stale state.

**Gate 5 — Deploy:**
Run `/deploy [feature description]` and confirm Vercel build succeeds.

### 6. Complete

- Mark the task done in `TASK.md` with today's date
- Report what was implemented, what files changed, and the live URL

### On Failure

If a validation gate fails:
1. Read the error carefully — don't guess
2. Check `PLANNING.md` for the relevant constraint or pattern
3. Fix the root cause — don't patch around it
4. Re-run the gate
5. If still failing after 2 attempts, stop and ask the user

---

## Reference

You can re-read the PRP at any point during implementation. It is the spec — trust it over assumptions.
