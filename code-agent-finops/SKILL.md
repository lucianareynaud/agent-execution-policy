---
name: code-agent-finops
description: Turns code-agent prompt design into execution policy. Use when a coding task is burning more credits than expected, an agent is touching unrelated files, a change passed tests but feels semantically wrong, the session lost context and needs cheap recovery, or you need to audit an existing prompt for drift and waste. Covers scope control, validation boundaries, stop conditions, and context recovery for spec-driven implementation tasks across Claude Code and other code agents.
---

# Code Agent FinOps

## Purpose

This skill governs code-agent execution for bounded implementation work.

It is designed to reduce drift, over-exploration, validation burn, expensive context recovery, and the class of failures where a change passes tests but still misses the design.

The goal is not the tightest prompt.
The goal is the cheapest execution contract that still produces the smallest semantically correct change.

## Use this skill when

Use this skill when the request is about one or more of the following:

- reducing credit or token burn on a concrete coding task
- stopping an agent from touching unrelated files or future work
- tightening a spec or task execution prompt
- fixing a prompt that passed tests but produced a semantically weak result
- recovering cheaply after the session lost context
- auditing an existing prompt for drift, waste, or weak stop conditions

Do not use this skill for open-ended ideation or broad architecture exploration where wide search is the point.

## Core thesis

In code-agent workflows, prompt design is execution policy.

Loose prompts waste credits through drift.
Over-tight prompts can reduce runtime spend while increasing total convergence cost through patched-but-passing implementations.

This skill exists to calibrate that trade-off.

## Operating modes

Choose the mode first. Then produce the smallest useful output for that mode.

### Mode A: Exploration under uncertainty

Use when the task is not yet well understood and the right integration surface is still unclear.

Good fit:
- unfamiliar legacy code
- unclear extension points
- integration discovery
- reverse engineering before implementation

Policy:
- allow wider reading, but bound it
- avoid premature implementation
- set an explicit exploration budget
- convert to another mode once the path is known

### Mode B: Local implementation

Use when the task is specified and should converge cheaply.

Good fit:
- small feature work
- bounded bug fixes
- spec/task execution
- local wiring inside a known boundary

Policy:
- narrow scope
- minimal file reads
- minimal diff
- explicit stop condition
- preserve design intent over smallest-diff patching

### Mode C: Validation and hardening

Use when implementation exists and the goal is confidence, not discovery.

Good fit:
- targeted hardening
- test expansion
- contract verification
- correctness checks after implementation

Policy:
- separate local iteration from broader validation
- constrain fixes to the current change
- avoid cleanup disguised as hardening

### Mode D: Context recovery after loss

Use when the session was summarized, truncated, restarted, or handed off.

Good fit:
- lost conversation state
- new agent session
- tool handoff
- resuming work without manual re-briefing

Policy:
- recover only what is needed
- load the minimum operating baseline
- inspect only authoritative files and local integration surface
- do not reconstruct project history unless the current task requires it

## Routing logic

Use this routing logic before producing a prompt contract or audit.

If the task is small, local, and already specified, use Mode B.

If the agent already produced a passing result but it feels patched or semantically weak, use Mode B with semantic correctness guardrails.

If the problem is not yet well understood, start with Mode A, impose an exploration budget, and define the condition for switching into Mode B.

If implementation exists but validation cost is noisy or high, use Mode C.

If the session lost context, use Mode D before doing anything else.

## Policy stack

A strong execution contract usually has five layers.

### 1. Scope boundary

Define exactly what the agent may implement and what it must not implement.

Include:
- current task or spec
- explicitly excluded later work
- anti-goals
- stop point

### 2. Credit-economy policy

Define how aggressively the agent should conserve effort.

Include:
- minimum necessary file reads
- minimum necessary file writes
- preference for localized edits
- whether exploration is allowed or tightly restricted
- prohibition on speculative improvements

### 3. Semantic correctness guardrails

Define what “cheap but wrong” looks like.

Include:
- do not bypass intended behavior just to satisfy tests
- do not use one-off special cases when the design implies a general rule
- do not duplicate logic if the correct integration point already exists
- follow design intent over smallest diff when they conflict

### 4. Validation boundary

Define what validation happens during iteration and what happens only at checkpoint.

Include:
- minimum relevant validation during iteration
- broader validation at the end
- rule against chasing unrelated failures

### 5. Stop condition

Define exactly when the agent must stop.

Include:
- task complete
- design intent preserved
- required validation passes
- no continuation into later tasks

## How to choose prompt hardness

Do not optimize for maximum strictness.

Optimize for the cheapest prompt that still preserves semantic correctness.

Make the prompt harder when:
- the task is well specified
- the expected diff is small
- the agent has a history of drift
- the repository is reasonably stable
- the main risk is wasted search

Make the prompt less hard when:
- the task has implicit dependencies
- the integration surface is not obvious
- the code is unfamiliar or legacy
- the design intent is broader than the diff suggests
- the main risk is under-solving

Never remove these, even in softer prompts:
- explicit scope boundary
- out-of-scope declaration
- semantic correctness guardrails
- validation boundary
- stop condition

## What to produce

For Mode A, produce a bounded exploration contract and the condition for switching into implementation.

For Mode B, produce a calibrated implementation prompt with scope boundary, credit-economy policy, semantic guardrails, validation boundary, and stop condition.

For Mode C, produce a validation-focused contract that narrows the test surface and constrains fixes to the current change.

For Mode D, produce a cheap context-recovery protocol and the smallest safe next step.

Keep the output direct. Do not narrate your process. Do not emit framework overhead before the contract itself.

## References

Load reference material only when needed.

- For reusable prompt contracts and patterns, consult `references/templates.md`
- For evaluation and benchmarking guidance, consult `references/metrics.md`
- For diagnostic patterns, repo hygiene implications, and common mistakes, consult `references/anti-patterns.md`

Only pull in the reference file that matches the current request.
