---
name: code-review
description: "Review the changes since a fixed point along two axes: Standards (does the code follow the repo's documented conventions?) and Spec (does the code match what the ticket asked for?). Reports them side by side, never merged."
---

Two-axis review of the diff between `HEAD` and a fixed point (usually the PR's merge-base with the default branch):

- **Standards**: does the code conform to the repo's documented conventions?
- **Spec**: does the code faithfully implement the originating ticket/brief?

Run the two axes as parallel sub-agents where the environment allows, so they don't pollute each other; otherwise run them as two strictly separate passes.

## Process

### 1. Pin the fixed point

`git diff <fixed-point>...HEAD` (three-dot: against the merge-base) and `git log <fixed-point>..HEAD --oneline`. Confirm the ref resolves and the diff is non-empty before going further.

### 2. Identify the spec source

The originating ticket and its agent brief (linked from the PR). If none exists, the Spec axis reports "no spec available" — don't invent requirements.

### 3. Identify the standards sources

Whatever the repo documents (CLAUDE.md, CONTRIBUTING.md, coding standards docs). On top of that, the **smell baseline** below always applies — with two rules: a documented repo standard overrides the baseline, and every smell is a judgement call ("possible Feature Envy"), never a hard violation. Skip anything tooling already enforces.

Smell baseline (Fowler, _Refactoring_ ch.3) — *what it is → how to fix*:

- **Mysterious Name**: name doesn't reveal what it does/holds → rename; if no honest name comes, the design's murky.
- **Duplicated Code**: same logic shape in several hunks/files → extract the shared shape.
- **Feature Envy**: method reaches into another object's data more than its own → move it onto the data it envies.
- **Data Clumps**: same few fields/params always travel together → bundle into one type.
- **Primitive Obsession**: a primitive standing in for a domain concept → give the concept its own small type.
- **Repeated Switches**: same switch/if-cascade on the same type recurs → polymorphism or one shared map.
- **Shotgun Surgery**: one logical change forces scattered edits across many files → gather into one module.
- **Divergent Change**: one module edited for several unrelated reasons → split per reason.
- **Speculative Generality**: abstraction for needs the spec doesn't have → delete it; inline until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation → hide the walk behind one method.
- **Middle Man**: a class that mostly delegates onward → cut it, call the target direct.
- **Refused Bequest**: an implementer ignoring most of what it inherits → composition instead.

### 4. The two passes

**Standards pass**: per file/hunk, (a) every place the diff violates a documented standard (cite the rule), (b) any baseline smell (name it, quote the hunk). Distinguish hard violations from judgement calls. Under 400 words.

**Spec pass**: (a) requirements the ticket asked for that are missing or partial, (b) behaviour that wasn't asked for (scope creep), (c) requirements that look implemented but wrong. Quote the criterion for each finding. Under 400 words.

### 5. Aggregate

Report under `## Standards` and `## Spec` headings — never merged, never reranked across axes. A change can pass one axis and fail the other; keeping them separate stops one from masking the other. End with a one-line summary: findings per axis and the worst issue within each.
