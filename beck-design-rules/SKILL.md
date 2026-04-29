---
name: beck-design-rules
description: Apply Kent Beck's four rules of simple design during code review, refactoring, and new code generation.
trigger: When reviewing code quality, refactoring, designing new components, or when the user asks for a design review or simplification of code.
---

# Beck Design Rules — Simple Design

Apply Kent Beck's four rules of simple design, in priority order, to evaluate and improve code. These rules originated from Extreme Programming and are universally applicable across languages and paradigms.

## The Four Rules (in priority order)

1. **Passes the tests** — The code must work as intended. Correctness is non-negotiable and takes highest priority. Ensure tests exist and pass before considering any other design concern.

2. **Reveals intention** — The code should be easy to understand. Express your purpose clearly so readers can grasp *why* the code exists, not just *what* it does. Naming, structure, and flow should communicate intent. When in doubt, optimize for empathy with the reader.

3. **No duplication** — Eliminate duplicated logic (DRY / "Once and Only Once"). Watch for hidden duplication such as parallel class hierarchies, repeated conditional patterns, or similar code paths with minor variations. Removing duplication is a powerful driver of good design.

4. **Fewest elements** — Remove anything that doesn't serve the three rules above. No speculative abstractions, no "just in case" flexibility layers, no unnecessary classes or methods. Extra complexity usually makes systems *harder* to modify, not easier.

## How to Apply

When writing or reviewing code, evaluate against each rule in order:

- **First**, verify the code is correct and tested. If tests are missing, add them before refactoring.
- **Second**, check if the code clearly communicates its intent. Rename variables, extract methods, or restructure to improve clarity.
- **Third**, look for duplication. Extract shared logic, but only when doing so doesn't harm clarity (rules 2 and 3 reinforce each other).
- **Fourth**, remove anything unnecessary — dead code, unused abstractions, over-engineered extension points.

## Key Insights

- Rules 2 and 3 ("reveals intention" and "no duplication") feed off each other. Adding duplication to increase clarity is often papering over a deeper problem — solve the root cause instead.
- When rules 2 and 3 genuinely conflict, **empathy wins** over strictly technical metrics (per Kent Beck himself). Think of the reader.
- Do NOT add architectural elements for hypothetical future requirements. The "fewest elements" rule exists precisely because speculative flexibility typically *reduces* real flexibility.
- These rules are **evaluable right now** on any codebase, unlike the ultimate quality criterion ("minimizes cost and maximizes benefit over the software's lifetime") which can only be assessed after the fact.

## When Refactoring

Use the rules as a refactoring checklist:

1. Are all tests green? If not, fix that first.
2. Can I rename or restructure to make intent clearer?
3. Is there duplicated logic I can extract or unify?
4. Can I delete anything without violating rules 1–3?
