---
name: triage
description: Move issues through a small state machine, verify claims, and write agent-ready briefs.
---

# Triage

Keep the board honest: every issue carries one category (`bug` / `enhancement`) and one state.

## States

- `needs-triage`: needs evaluation
- `needs-info`: waiting on the reporter/human for more information
- `ready`: fully specified with a brief — an agent can take it
- `human`: needs human implementation or a human decision
- `wontfix`: will not be actioned (close it, say why)

Transitions: unlabeled → `needs-triage` → one of the others. `needs-info` returns to `needs-triage` when the reporter replies. The human can override any state at any time — trust an explicit instruction and apply it directly.

## Triaging an issue

1. **Gather context.** Read the full issue (body, comments, history). Check the codebase for an existing implementation of the requested behavior (search by domain concept, not just the request's wording) — if found, it's an already-implemented `wontfix`: point to where it lives and close.
2. **Verify the claim.** For a bug: reproduce it from the reporter's steps before anything else. Confirmed (with code path), failed, or insufficient detail (→ `needs-info`). A confirmed repro makes a much stronger brief.
3. **Apply the outcome:**
   - `ready` → attach an agent brief (below).
   - `needs-info` → post what's established so far + specific, actionable questions (never "please provide more info").
   - `human` → same brief structure, plus why it can't be delegated (judgment call, external access, manual testing).

## Agent briefs

The brief is the contract an agent works from. Rules:

- **Durable over precise**: describe interfaces, types, and behavioral contracts. Never file paths or line numbers — they go stale.
- **Behavioral, not procedural**: what the system should do, not how to edit which file.
- **Complete acceptance criteria**: each independently verifiable. "Works correctly" is not a criterion.
- **Explicit scope boundaries**: state what is out of scope to prevent gold-plating.

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** one line

**Current behavior:** what happens now (for bugs: the broken behavior; for enhancements: the status quo).

**Expected behavior:** what should happen, behaviorally.

**Acceptance criteria:**
- [ ] criterion 1
- [ ] criterion 2

**Out of scope:** what NOT to touch.
```
