---
name: fofi-researcher
description: Fan-out/fan-in research orchestration with Sonnet researchers and Opus synthesis
---

# FoFi Researcher Skill

Fan-out/fan-in research orchestration for complex questions.

## Purpose

Use this skill to answer a complex question by sending the same root question to multiple researcher subagents, then synthesizing results into one high-quality answer.

Pattern:

- Fan-out: `N` researcher subagents in parallel (minimum `5`)
- Fan-in: `1` synthesizer subagent

Model policy:

- Researchers: Sonnet
- Synthesizer: Opus

## When To Use

Use this skill when the question is broad, ambiguous, or requires multiple perspectives (technical, product, risk, implementation, alternatives).

Do not use this skill for trivial or single-fact questions.

## Workflow

### 1) Frame the research question

Define:

- Core question
- Scope boundaries
- Decision criteria
- Expected output format

### 2) Plan fan-out

Use redundant-coverage fan-out only:

- same question for all researchers
- same deliverable schema
- optionally vary constraints (for example: speed-first, risk-first, evidence-first) to widen discovery

### 3) Spawn researchers in parallel (Sonnet)

Launch one subagent per researcher using Sonnet. Each prompt must include:

- the same root question
- assumptions to validate
- required evidence level
- output schema (facts, trade-offs, open questions, recommendation)

Rules:

- Minimum `5` researcher subagents
- Intentionally allow overlapping scope
- Require explicit uncertainties

### 4) Normalize outputs

Collect all researcher outputs and normalize into a common structure:

- Key findings
- Evidence
- Conflicts with other tracks
- Unique findings not found by other researchers
- Confidence level
- Open questions

### 5) Spawn synthesizer (Opus)

Launch one synthesizer subagent using Opus with:

- original question
- all normalized track outputs
- synthesis rubric

Synthesis must produce:

- direct answer first
- consolidated reasoning
- conflict resolution between tracks
- clear recommendation
- residual risks and unknowns
- next validation steps

## Execution Requirements

- Fan-out count: `N >= 5`
- Research model: Sonnet only
- Synthesis model: Opus only
- Prefer parallel fan-out execution
- Fail fast on missing evidence or contradictory high-impact claims

## Prompt Templates

### Researcher prompt template (Sonnet)

```
You are Research Run {ID}.
Question: {ROOT_QUESTION}
Global objective: {ROOT_QUESTION}

Deliver in this structure:
1) Findings (bullet points)
2) Evidence and rationale
3) Trade-offs
4) Risks and uncertainties
5) Open questions
6) Track recommendation

Constraints:
- Stay within scope: {SCOPE}
- Call out unknowns explicitly
- Focus on completeness and call out what might have been missed
```

### Synthesizer prompt template (Opus)

```
Synthesize these research tracks into one decision-grade answer.

Root question: {ROOT_QUESTION}
Decision criteria: {CRITERIA}
Research outputs:
{TRACK_OUTPUTS}

Return:
1) Final answer (first)
2) Supporting reasoning
3) Conflicts and how resolved
4) Recommended path
5) Risks and mitigations
6) What to validate next
```

## Orchestration Notes

If your environment supports explicit model routing for subagents:

- set each researcher subagent to Sonnet
- set synthesizer subagent to Opus

If model routing is unavailable, stop and report that the skill cannot guarantee policy compliance.

## Definition Of Done

- At least 3 researcher outputs are present
- All researchers used Sonnet
- Synthesizer used Opus
- Final output contains answer, rationale, conflicts, recommendation, and risks
