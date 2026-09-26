---
name: pr
description: "Use when writing a PR body."
metadata:
  credits:
    skill: show-me
    author: Dex Horthy
    organisation: Humanlayer
    url: "https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md"
---

Use this template for the PR body. Skip preambles, keep prose brief, use the project's own vocabulary.

```markdown
## Summary

<the smallest view that makes the key point clear>

## Evidence

- **Before:** <screenshot / output / failing test run>
  **After:** <screenshot / output / passing test run>

## Merge Danger

**Door:** <one-way or two-way>
**Blast Radius:** <one-word description, plus ramifications if any>
```

### Summary

Pick ONE (rarely two) of these forms — whichever makes the change obvious fastest:

- **Pseudocode** for logic/algorithm changes
- **Call tree** for runtime control flow
- **Component tree** for UI structure
- **Shallow file tree** for file responsibility / broad refactors
- **Mermaid sequence diagram** for component interaction
- **`diff` blocks** when the point is what changed and the surrounding shape already exists (works for component trees, file layouts, call trees, control flow)

Don't overwhelm: only the calls, files, props, and boundaries needed to make the point.

### Evidence

Concrete proof the change works, before/after. Screenshots are S-tier for visual changes. Test results and console output are A-tier: show the exact test that failed and now passes.

### Merge Danger

Two-way doors can be walked back; one-way doors can't (destructive actions, hard-to-reverse decisions). Blast radius = the potential scope of impact (layout shift, breaking consumers, mobile, etc.).
