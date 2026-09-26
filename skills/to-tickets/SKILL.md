---
name: to-tickets
description: Break a plan, spec, or request into tracer-bullet tickets, each declaring its blocking edges, published to the project's issue tracker.
---

# To Tickets

Break work into **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it.

## Process

### 1. Gather context

Work from what's in context. If given a reference (spec, issue), fetch and read its full body and comments.

### 2. Explore the codebase (if not already done)

Understand the current state of the code. Ticket titles and descriptions use the project's vocabulary. Look for opportunities to prefactor: "make the change easy, then make the easy change."

### 3. Draft vertical slices

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring is its own ticket, done first

</vertical-slice-rules>

Give each ticket its **blocking edges**: the tickets that must complete before it starts. A ticket with no blockers can start immediately. Never pad: a small request is ONE ticket.

**Wide refactors are the exception to vertical slicing.** One mechanical change (rename a column, retype a shared symbol) whose blast radius fans across the codebase can't land green as a vertical slice. Sequence it as **expand–contract**: expand (add the new form beside the old), migrate call sites in batches (each batch a ticket blocked by the expand), contract (delete the old form, blocked by every batch).

### 4. Sanity-check the breakdown

Present the numbered breakdown (title, blocked-by, what it delivers). If granularity or edges are uncertain in a way that changes the plan, ask the human once. Otherwise proceed.

### 5. Publish

One issue per ticket, in dependency order (blockers first) so edges reference real identifiers. Use the tracker's native blocking relationship where it exists; otherwise a "Blocked by" section. Mark tickets `ready` — they are agent-grabbable by construction. Do NOT close or modify any parent issue.

<issue-template>

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective — not layer-by-layer implementation.

## Acceptance criteria

- [ ] Criterion 1 (independently verifiable)
- [ ] Criterion 2

## Blocked by

References to blocking tickets, or "None (can start immediately)".

</issue-template>

Avoid file paths and code snippets — they go stale. Exception: a prototype-derived snippet that encodes a decision (state machine, reducer, schema) may be inlined, trimmed to the decision-rich part.
