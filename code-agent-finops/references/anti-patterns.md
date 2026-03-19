# Anti-patterns and diagnostic guidance

Use this reference when the request is about diagnosis, audit, or why an agent workflow feels expensive or unreliable.

---

## Primary anti-patterns

### Anti-pattern: "Implement this spec"
Too vague. Grants open-ended search with no scope boundary.

### Anti-pattern: "Use the smallest possible diff"
Too narrow. Encourages patching around the design rather than implementing it.

### Anti-pattern: Giant bootstrap memory dump
Expensive, brittle, and usually stale. Reconstructing project history on every session reset is not context management — it is context theater.

### Anti-pattern: Full suite on every micro-edit
Turns validation into a burn engine. Reserve broad validation for checkpoints, not iteration loops.

### Anti-pattern: "Future-proof this while you are there"
Usually means paying for abstractions the current task did not earn.

### Anti-pattern: No stop condition
Invites task overstaying and optional helpfulness disguised as thoroughness.

---

## Cheap context architecture

The cheapest way to handle session loss is not perfect memory. It is cheap rehydration.

Design project context in three layers:

**Layer A: Always-on baseline**

Keep this tiny — under ten lines if possible. It should contain only:
- operational anti-goals for this codebase
- stop policy
- a few critical architectural invariants
- where the canonical spec, task, and design documents live

This can live in `AGENTS.md`, a short project contract file, or a steering note at the repo root.

**Layer B: Modular context**

Load on demand. This includes feature specs, design notes, ADRs, and task documents. Organize these so they are findable by file name without requiring a broad search.

**Layer C: Task-local context**

Reconstruct fresh for each task:
- current task and current stop point
- authoritative files for this task
- immediate integration surface
- relevant validation target

If the baseline is small and modular context is well-organized, session loss becomes a minor event, not a costly recovery operation.

---

## Diagnostic and environmental factors

A bad repository makes even strong prompts expensive.
A good repository makes even imperfect prompts cheaper.

Improve these first:
- obvious locations for specs and design documents
- stable, predictable naming for task files
- small ADRs instead of giant architecture dumps
- discoverable integration points
- tests that reflect design intent, not just incidental behavior
- predictable repo layout

---

## Diagnostic framing

When auditing a bad run, classify the failure mode first.

Typical classes:
- **drift burn**: agent read too much, touched too many files, or anticipated future work
- **patched-but-passing**: agent optimized for smallest diff and bypassed design intent
- **memory theater**: session recovery burned more tokens than the actual task
- **validation burn**: broad test runs during local iteration dominated total spend

Then decide whether the right correction is:
- harder scope containment (drift burn)
- stronger semantic guardrails (patched-but-passing)
- cheaper context architecture (memory theater)
- narrower validation boundary (validation burn)
- better repository ergonomics (underlying cause of multiple classes)
