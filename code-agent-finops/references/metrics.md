# Metrics

Use this reference only when the request involves evaluation, benchmarking, or proving the method is working.

## What to measure

Do not evaluate prompt policy only on raw token count.

The better question is whether it reduces total convergence cost.

Suggested metrics:
- files read per task
- files changed per task
- lines changed per task
- attempts to converge
- validation runs per task
- broad-vs-local validation ratio
- percentage of spend after the solution was locally available
- human rework after “tests passing”
- context-recovery cost after session reset
- time-to-first-correct-diff
- cost per accepted diff

## Evaluation guidance

A useful comparison needs:
- a defined baseline prompt or baseline workflow
- representative task categories
- the same repo state
- the same success condition
- a semantic correctness check, not just a passing test suite

## Minimum benchmark set

A practical benchmark set should include:
- one bounded bug fix
- one small spec/task implementation
- one recovery-from-context-loss scenario
- one prompt-audit-and-rewrite scenario
- one validation-boundary scenario

## Core principle

The most important metric is not raw token count.

It is total convergence cost, including human correction after the agent claims success.
