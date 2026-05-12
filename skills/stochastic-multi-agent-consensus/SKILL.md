---
name: stochastic-multi-agent-consensus
description: Probabilistic multi-agent consensus orchestration for robust research convergence
---

# Stochastic Multi-Agent Consensus Skill

Probabilistic multi-agent consensus orchestration for complex research questions.

## Meaning

Stochastic multi-agent consensus combines:

- Multi-agent consensus: multiple autonomous agents iteratively exchange local views to reach a shared decision.
- Stochastic process: randomness is intentionally introduced (for example random peer matching, random critique routing, varied seeds/constraints) to reduce coordination bias and improve exploration.

In this skill, the pattern is used for research quality: multiple agents independently investigate the same question, then converge through randomized critique and aggregation rounds.

## Web-Grounded Basis

This skill is based on common consensus and gossip concepts from distributed systems:

- Consensus requires termination, agreement, and validity under partial failures and uncertainty.
- Randomized consensus can improve practical liveness where deterministic consensus is limited in asynchronous settings.
- Gossip/epidemic protocols use random pairwise exchanges and redundancy to spread information and compute aggregates robustly.

## When To Use

Use this skill when:

- the question is high-stakes or ambiguous
- single-run answers are likely to miss key evidence
- you want both diversity and convergence

Avoid this skill for trivial factual lookups.

## Workflow

### 1) Define objective and acceptance criteria

Specify:

- root question
- scope boundaries
- required evidence bar
- decision rubric

### 2) Initialize agents (minimum 5)

Spawn `N >= 5` researcher agents in parallel.

All agents receive the same root question and output schema, but each gets a stochastic variation:

- random seed
- random source ordering
- role emphasis (risk-first, implementation-first, counterexample-first, cost-first)
- optional time or token budget variance

### 3) Independent first-pass research

Each agent returns:

- answer hypothesis
- supporting evidence
- confidence score (0-1)
- explicit unknowns

### 4) Stochastic critique rounds

Run `R` consensus rounds (`R = 2..4` typical):

- randomly pair or subgroup agents each round
- each agent critiques another agent's hypothesis
- agents update their own hypothesis after receiving critique
- require a delta note: what changed and why

This acts like gossip-style iterative information mixing.

### 5) Compute consensus state

Aggregate after each round:

- agreement score (semantic similarity across final hypotheses)
- evidence overlap score
- unresolved contradiction count
- minority novel-findings set

Convergence gate:

- stop when agreement score crosses threshold and contradictions are below threshold
- otherwise continue until max rounds

### 6) Final synthesis

Send all final hypotheses, critiques, and metrics to one synthesizer.

Synthesizer must output:

- final answer first
- strongest supporting evidence
- retained minority insights
- contradictions and resolution rationale
- residual risks and next validation actions

## Execution Requirements

- Minimum researcher count: `N >= 5`
- Same root question across all researchers
- Randomness must be explicit and logged per agent
- At least 2 stochastic critique rounds unless convergence is already strong
- Keep minority findings; do not force premature unanimity
- Fail loud if evidence is weak or contradictory at convergence cutoff

## Prompt Templates

### Researcher template

```
You are Research Agent {ID}.
Root question: {ROOT_QUESTION}
Seed: {SEED}
Variation profile: {PROFILE}

Deliver:
1) Hypothesis
2) Evidence
3) Confidence (0-1)
4) Unknowns
5) Potential failure cases
```

### Critique template

```
Critique target hypothesis:
{TARGET_HYPOTHESIS}

Evaluate:
1) strongest evidence
2) weakest assumption
3) missing evidence
4) contradiction risks
5) revision suggestion
```

### Synthesizer template

```
Synthesize these post-consensus artifacts:
- final agent hypotheses
- critique logs
- agreement metrics
- unresolved contradictions

Return:
1) Final answer
2) Why this is the consensus outcome
3) Minority but important findings
4) Known risks and confidence limits
5) Immediate next validation step
```

## Suggested Defaults

- `N = 5` researchers
- `R = 3` stochastic critique rounds
- agreement threshold: `>= 0.75`
- contradiction threshold: `<= 2` unresolved material conflicts

## Definition Of Done

- At least 5 independent first-pass outputs exist
- Stochastic parameters were recorded for each agent
- At least 2 critique rounds completed (or early convergence justified)
- Final answer includes consensus and minority views
- Remaining risks are explicit and testable
