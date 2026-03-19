# Templates

Use these templates only when the current request requires them.

---

## Template 1: Scope containment

Use when the main risk is drift.

```text
Implement only the current task described in the authoritative spec, design, and task documents.

Rules:
- Follow the task list in order.
- Do not implement future tasks or future specs.
- Do not create speculative abstractions or shared infrastructure unless the current task explicitly requires them.
- Touch only the minimum required files.
- Prefer localized edits over broad changes.
- Stop immediately after the current task is complete and validation passes.
```

---

## Template 2: Economical but sound

Use when the main risk is patched-but-passing.

```text
Implement the smallest semantically correct change for the current task.

Constraints:
- Minimize file reads, file writes, and changed lines.
- Read enough of the local integration surface to preserve the design intent.
- Do not satisfy tests by bypassing intended behavior.
- Do not introduce one-off special cases if the design implies a general rule.
- Do not duplicate logic if the correct integration point already exists.
- Prefer the smallest semantically correct diff, not merely the smallest diff.
```

---

## Template 3: Recovery after context loss

Use when the session lost context and the goal is cheap re-entry.

The "minimum baseline" referred to in this template is whatever always-on operating contract your project defines. If you have an `AGENTS.md`, a short project contract, or a steering file, that is your baseline. If you have none of those, the baseline is: the anti-goals for this codebase, the stop policy, and the location of the canonical spec or task documents. Keep it to under ten lines. The goal is re-entry, not reconstruction.

```text
Recover context with minimum cost.

Steps:
- Restate the current task in one paragraph.
- Identify the authoritative documents and files for this task.
- Load only the minimum baseline operating contract for this project:
  the anti-goals, the stop policy, and the canonical document locations.
- Inspect only the local integration surface required for the task.
- Do not reconstruct project history or unrelated prior work unless
  the current task explicitly depends on it.
- Once context is recovered, propose the smallest safe next step and stop.
```

---

## Template 4: Validation boundary

Use when tests are becoming the hidden burn source.

```text
During implementation, run only the minimum relevant validation needed to iterate.
After the task is complete, run the required broader validation checkpoint.
If failures appear, fix only failures caused by the current task.
Do not chase unrelated failures or expand scope into cleanup.
```

---

## Template 5: Calibrated stop condition

Use when the agent overstays the task.

```text
Stop as soon as all of the following are true:
- the current task is implemented
- the implementation matches the design intent
- the required validation passes

Do not continue to later tasks.
Do not add optional improvements.
Do not reopen completed work unless the current task directly depends on it.
```

---

## Pattern A: Cheap local spec execution

Combines Templates 1, 2, 4, and 5. Use for bounded spec tasks with known boundaries.

```text
Implement only the current task using the task's spec, design, and task
documents as authoritative sources.

Goal:
Complete the task with the smallest semantically correct diff and the
lowest practical credit usage.

Rules:
- Follow tasks in order.
- Do not implement future tasks or later specs.
- Read only the minimum files required, but enough of the local integration
  surface to preserve design intent.
- Touch only the minimum required files.
- Prefer localized edits over broad changes.
- Do not refactor unrelated code.
- Do not add speculative abstractions.
- Do not satisfy tests by bypassing intended behavior.
- Do not use one-off special cases if the design implies a general rule.
- During iteration, run only the minimum relevant validation.
- At the end, run the required validation checkpoint.
- Stop immediately once the current task is complete, design-consistent,
  and validation passes.
```

---

## Pattern B: Cheap recovery from session loss

Combines Template 3 and stop condition. Use when context was summarized or lost.

```text
Recover the current task context with minimum cost.

Instructions:
- Restate the current task and current stop point.
- Identify the authoritative documents and files.
- Load only the minimum baseline operating contract:
  the anti-goals, stop policy, and canonical document locations.
- Inspect only the files required to continue safely.
- Do not rebuild project history.
- Do not summarize unrelated prior work.
- Once context is recovered, propose the smallest safe next step and stop.
```

---

## Pattern C: Prompt audit for likely waste

Use when reviewing an existing prompt before execution or after a bad run.

```text
Review this execution prompt for cost risk and implementation risk.

Classify the prompt on:
- drift risk
- under-solving risk
- validation burn risk
- context-recovery cost
- stop-condition quality

Then rewrite it to:
- tighten scope
- preserve semantic correctness
- reduce unnecessary validation
- prevent future-task bleed
- improve cheap recovery after context loss
```
