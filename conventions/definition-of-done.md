# Definition of Done

A ticket is **done** when ALL of these hold. No exceptions, no "mostly done".

1. **The acceptance criteria on the ticket are met** — each one individually verifiable, and verified.
2. **Tests are green** — the tests covering the change pass, and the full suite passes at least once before opening the PR.
3. **Typecheck and lint pass** — using the repo's own commands (check its package.json / CLAUDE.md).
4. **A PR is open** — with evidence in the body: before/after screenshot for visual changes, test output otherwise (see the `pr` skill).
5. **Nothing merged, nothing deployed** — merging and deploying are human decisions. The work stops at the open PR.

A ticket that cannot reach this state is not "partially done" — it goes back with a comment explaining exactly what is blocking, and the agent stops working on it.

## What "verified" means

Claiming is not verifying. Run the thing: execute the test, load the page, call the endpoint. If a criterion cannot be exercised, say so on the ticket instead of assuming it works.
