# Generate PRP

Generate a complete Product Requirements Prompt for a new EDGE journal feature.

## Feature file: $ARGUMENTS

Read the feature file first (default: `INITIAL.md`) to understand what needs to be built. Then research the codebase and produce a thorough PRP that gives an AI agent everything needed to implement the feature correctly in one pass.

---

## Research Process

### 1. Read Context Files
- Read `PLANNING.md` in full — architecture, state shape, key functions, constraints
- Read `CLAUDE.md` — design system, terminal aesthetic rules, non-negotiables
- Read `TASK.md` — confirm the feature is listed; add it if not

### 2. Codebase Analysis (search `C:\Users\Kory\Projects\index.html`)
- Find the page(s) this feature lives on (`id="page-X"`)
- Find existing render functions that follow a similar pattern
- Find CSS classes this feature should reuse or extend
- Find where in the `go()` function to add the render call
- Identify any state properties the feature will read or write
- Check how similar features handle empty state (zero trades)
- Check how similar features handle quickMode trades

Key grep targets:
```
function render     → all render functions and their patterns
calcPnL             → how P&L is computed
state\.trades       → how trades are accessed
class="card"        → card HTML patterns
class="metric"      → metric display patterns
go(                 → navigation wiring
```

### 3. Design System Check
Confirm the feature can be built using only:
- Colors: `var(--bg)`, `var(--bg2)`, `var(--bg3)`, `var(--accent)`, `var(--green)`, `var(--red)`, `var(--amber)`, `var(--blue)`, `var(--text)`, `var(--muted)`, `var(--dim)`
- Fonts: `var(--display)` (Geist) for labels, `var(--mono)` (Geist Mono) for numbers
- Radii: `var(--r)`, `var(--r2)`, `var(--r3)`
- Existing component classes before creating new ones

### 4. Clarify If Needed
If the feature request is ambiguous, ask one clarifying question before proceeding. Prefer asking over guessing.

---

## PRP Generation

Use `PRPs/templates/prp_base.md` as the template. Fill every section — do not leave placeholders.

### Critical sections to complete thoroughly:
- **Relevant Existing Patterns table** — minimum 3 rows with real function/class names from `index.html`
- **Known Gotchas** — at least the standard list plus feature-specific ones
- **Implementation Blueprint** — pseudocode specific enough that there's no ambiguity
- **Ordered Task List** — every discrete step, in order
- **Validation Gates** — all 5 gates with feature-specific checks filled in

### Score the PRP
After writing, score it 1–10 on confidence for one-pass implementation. If below 8, go back and fill gaps before saving.

---

## Output

Save the completed PRP as: `PRPs/{feature-name}.md`

Then tell the user:
- The PRP file path
- The confidence score and why
- The command to execute it: `/execute-prp PRPs/{feature-name}.md`
