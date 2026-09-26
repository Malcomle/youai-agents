# Stop Rules

These rules exist because agent teams fail by **burning tokens on busywork**, not by lacking initiative. When in doubt: do less, report, stop.

## Forbidden — never do these

- **Never create meta-work.** No tickets about improving processes, writing internal reports, reorganising the team, "syncing", or planning-the-planning. Every ticket must have a concrete deliverable a human can use (a PR, a published document the human asked for).
- **Never invent work.** If the queue is empty, stop. An idle agent costs nothing; a self-occupying agent burns the budget.
- **Never role-play a company.** There is no hiring, no meetings, no org decisions. Those concepts do not exist here. If a task seems to require "hiring" or delegating to a role that doesn't exist, escalate to the human instead.
- **Never loop with another agent.** If the same ticket bounces between two agents more than twice (dev → qa → dev → qa on the same finding), stop and escalate to the human with a summary of the disagreement.
- **Never merge, deploy, or take any outward-facing action** (emails, posts, purchases, external API writes) without an explicit human instruction on the ticket.

## When to stop and escalate to the human

Stop working and leave a comment on the ticket when:

- An acceptance criterion is ambiguous and the ambiguity changes what you'd build.
- You are blocked by missing access (secret, repo, service) — do not work around it.
- The fix requires touching something outside the ticket's scope.
- You've made two failed attempts at the same problem — describe both attempts and what you learned.

Escalation format: one comment, three parts — **what I was doing / what blocks me / what I need**. Then stop. Don't keep polling or retrying.

## Token discipline

- Read only what the ticket needs. Don't re-explore a codebase you already know from the session context.
- One wake-up = progress on one ticket. Don't touch several tickets in one run.
- Keep comments short and factual. No status theatre ("Great progress today! 🎉").
