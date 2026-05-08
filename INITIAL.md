# INITIAL.md — Feature Request Template

> Copy this file, fill it out, then run `/generate-prp INITIAL.md` to produce a full PRP.
> Or fill in the sections below directly for a new feature.

---

## FEATURE

<!-- What do you want to build? Be specific. Include the page(s) it lives on, what user action triggers it, and what the output/result looks like. -->

[Describe the feature here]

---

## EXAMPLES

<!-- Point to existing patterns in index.html that this feature should follow.
     E.g.: "Follow the pattern of renderHistory() for the table structure"
     Or describe a similar UI element that already exists and works well. -->

- Similar pattern in `index.html`: [function or component name]
- CSS classes to reuse: [list classes]
- State access pattern: [e.g., `state.trades.filter(...)`]

---

## DOCUMENTATION

<!-- Any external docs, APIs, or references needed.
     E.g. Firebase docs URL, OpenAI API reference, a specific CSS technique. -->

- 
- 

---

## OTHER CONSIDERATIONS

<!-- Gotchas, constraints, or edge cases the AI should know about.
     E.g.: "trades can be in quickMode — always check t.quickMode before accessing t.entry/exit"
     Or: "this touches the AI grading flow — keep prompts in the JS, not in /api/chat.js" -->

- Must maintain terminal aesthetic (dark bg, purple accent, no new colors)
- All numbers must use Geist Mono font-family
- New page sections need a `go()` case if they're top-level pages
- 
