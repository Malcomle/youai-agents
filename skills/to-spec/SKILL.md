---
name: to-spec
description: "Turn a request or conversation into a spec published on the project's issue tracker: synthesis of what was discussed, no interview."
---

Turn the current request/conversation plus codebase understanding into a spec. Do NOT interview the human beyond what's needed; synthesize what you already know. If something ambiguous would change the design, ask ONE batched set of questions first.

## Process

1. Explore the repo enough to understand the current state of the code in the area the request touches. Use the project's existing vocabulary (README, CLAUDE.md, domain docs).

2. Sketch the seams at which the feature will be tested. Prefer existing seams to new ones; use the highest seam possible. The fewer seams, the better.

3. Write the spec using the template below and publish it to the project's issue tracker.

<spec-template>

## Problem Statement

The problem, from the user's perspective.

## Solution

The solution, from the user's perspective.

## User Stories

A numbered list: `As an <actor>, I want <feature>, so that <benefit>`. Cover all aspects of the feature.

## Implementation Decisions

Decisions made: modules built/modified, their interfaces, architectural decisions, schema changes, API contracts. Do NOT include file paths or code snippets — they go stale. Exception: a prototype-derived snippet that encodes a decision (state machine, schema, type shape) may be inlined, trimmed to the decision-rich part.

## Testing Decisions

What makes a good test here (external behavior only), which modules get tested, prior art in the codebase.

## Out of Scope

What this spec explicitly does not cover.

</spec-template>
