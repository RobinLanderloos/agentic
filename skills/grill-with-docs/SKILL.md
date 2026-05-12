---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and writes documentation (ADRs) once at the end when all decisions are settled. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

<what-to-do>

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time, waiting for feedback on each question before continuing.

If a question can be answered by exploring the codebase, explore the codebase instead.

</what-to-do>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation.

## Decision tracking

Throughout the session, maintain a running internal list of candidate decisions — do **not** write any files yet. Decisions often evolve mid-session; writing too early risks documenting a half-baked conclusion.

At the end of the session (when the user signals they are done, or when all branches of the design tree are resolved), flush the settled decisions to ADR files. If no `{git root}/docs/adr/` exists, create it at that point.

## During the session

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Tracking ADR candidates

During the session, silently note a decision as an ADR candidate when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

Do not write files or interrupt the flow to announce candidates. At the end of the session, present the list of candidate ADRs and ask the user to confirm before writing anything. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
