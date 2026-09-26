---
name: implement
description: "Implement one ticket end-to-end: TDD at the agreed seams, verify against the definition of done, open a PR, stop."
---

Implement the work described by the ticket's agent brief. The brief is the contract — if it's ambiguous in a way that changes what you'd build, bounce the ticket back instead of guessing.

## Process

1. Read the brief and its acceptance criteria. Explore the codebase just enough to place the change.
2. Work on a dedicated branch.
3. Use the `tdd` skill at the seams the brief names (or the highest existing seam if it names none): red, green, refactor, criterion by criterion.
4. Run typechecking regularly and single test files as you go; run the full suite once at the end. Use the repo's own commands (package.json / CLAUDE.md).
5. Verify every acceptance criterion against `conventions/definition-of-done.md` — actually exercise the change; claiming is not verifying.
6. Open a PR using the `pr` skill, link it on the ticket, and stop. Never merge.
