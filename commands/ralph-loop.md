# ralph-loop

You are the orchestrator for a structured implementation loop. Work through all open issues in `.agents/tasks/prd-homestock.json` in dependency order, running independent issues in parallel in isolated git worktrees.

---

## Step 1 — Load state

Read `.agents/tasks/prd-homestock.json`. The file contains a `userStories` array where each item has:
- `id` — e.g. `"001"`
- `title`
- `status` — `"open"` or `"done"`
- `markdownFile` — relative path to the issue spec (e.g. `docs/issues/001-solution-scaffold-dev-auth.md`)
- `blockedBy` — array of IDs this issue depends on (e.g. `["001"]`)

Read each issue's `markdownFile` too, so you have the full spec for each open issue.

---

## Step 2 — Compute the ready wave

A **ready** issue satisfies both:
1. `status === "open"`
2. Every ID in `blockedBy` has `status === "done"` (or `blockedBy` is empty)

Collect all ready issues. This is the current wave.

- If the wave is **empty** and open issues still exist → deadlock. Stop and report which issues are blocked and why.
- If the wave is **empty** and no open issues remain → all done. Report completion and stop.

---

## Step 3 — Spawn agents in parallel

For each issue in the current wave, spawn one Agent with `isolation: "worktree"`. **Send all wave agents in a single message** (multiple Agent tool calls in one response) so they run in parallel.

Use the following prompt for each agent, filling in the placeholders:

---

> You are implementing a single feature for **HomeStock**, a household inventory tracking app (two users, shared data — consumables + fixed assets across named storage locations).
>
> **Stack**
> - .NET 8, Blazor Server (not WASM), single Docker container deployment
> - Solution: `HomeStock.Domain` → `HomeStock.Infrastructure` → `HomeStock.Web`
> - Vertical Slice Architecture: each feature lives in `HomeStock.Web/Features/<FeatureName>/`
> - Plain C# handler classes registered in DI — no MediatR, no pipeline abstractions
> - SQLite via EF Core; `HomeStockDbContext` injected directly into handlers (no repository pattern)
> - MudBlazor UI components, mobile-first (375px target)
> - All user-facing text **in Dutch**
> - EF Core migrations live in `HomeStock.Infrastructure/Migrations/`
>
> **Conventions**
> - Handler naming: `<Action>Handler` (e.g. `CheckInHandler`, `DeleteLocationHandler`)
> - Blazor pages in `HomeStock.Web/Features/<FeatureName>/<PageName>.razor`
> - No code comments unless the WHY is non-obvious
> - No backwards-compatibility shims or unused exports
>
> ---
>
> **YOUR ISSUE**
>
> {{ISSUE_MARKDOWN_CONTENT}}
>
> ---
>
> **Instructions**
>
> 1. Read the existing codebase first to understand what is already in place before writing anything.
> 2. Invoke the `/tdd` skill. The acceptance criteria listed in the issue above are your **pre-approved behavior list** — treat them as the completed output of the TDD planning step. Skip any "confirm with user" or "get user approval" planning steps and go straight to the tracer bullet.
> 3. Work through each acceptance criterion using the red→green→refactor cycle: write a failing test, write minimal code to pass it, refactor, then move to the next criterion.
> 4. Run `dotnet build` from the solution root. Fix all errors and warnings before finishing.
> 5. Stage and commit all changes: `git commit -m "feat({{ISSUE_ID}}): {{ISSUE_TITLE}}"`
> 6. Do NOT push. Do NOT open a PR.

---

## Step 4 — Merge completed branches

After **all agents in the wave** have finished, for each agent that returned a branch name:

```
git merge --no-ff <branch-name> -m "Merge issue {{ISSUE_ID}}: {{ISSUE_TITLE}}"
```

If a merge produces conflicts, stop immediately and report the conflict details to the user. Do not continue to the next wave until conflicts are resolved.

---

## Step 5 — Update status

After successfully merging each issue's branch, open `.agents/tasks/prd-homestock.json` and set that issue's `status` from `"open"` to `"done"`. Stage the file and amend it into the merge commit:

```
git add .agents/tasks/prd-homestock.json
git commit --amend --no-edit
```

---

## Step 6 — Clean up worktrees

After updating the status for all issues in the wave, remove the worktrees created for the agents in this wave:

```
git worktree remove <worktree-path>
```

Replace `<worktree-path>` with the actual path of each worktree used by the agents in the current wave.

---

## Step 7 — Repeat

Return to **Step 2** and compute the next ready wave. Continue until no open issues remain.

---

## Rules

- Always run all agents in a wave as **parallel tool calls** (one message, multiple Agent invocations).
- Never start a dependent issue before all its blockers are merged **and** marked `"done"` in the JSON.
- A branch with a failing `dotnet build` must not be merged — the agent must fix it first.
- Merge branches in the order they complete; you do not need to wait for the slowest agent in a wave before merging faster ones.
- Keep the user informed between waves: report which wave just completed and what starts next.
