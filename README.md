# code-agent-finops

A Claude skill for agent execution policy: tightening scope, reducing credit burn, and recovering context cheaply in code-agent workflows.

Usable in Claude Code and uploadable as a skill in Claude.ai.

---

## What you get

A structured Claude skill with four files:

- `SKILL.md` — core skill: operating modes, routing logic, policy stack, and output instructions
- `references/templates.md` — five atomic prompt templates and three composite patterns, ready to use or adapt
- `references/anti-patterns.md` — diagnostic guide for classifying bad runs, cheap context architecture, and repo hygiene factors
- `references/metrics.md` — evaluation and benchmarking guidance for measuring convergence cost

---

## The problem it solves

Most teams evaluate code agents on speed and output volume. Those signals are useful but shallow.

A workflow can look productive and still be economically sloppy. A change can pass tests and still violate the design. "Context recovery" can mean token burn with better branding.

This skill treats prompt design as execution policy. It produces the cheapest execution contract that still preserves semantic correctness — not the tightest one, not the loosest one.

**Failure modes it targets:**

**Drift burn** — the agent reads too much, touches unrelated files, or scaffolds future work that was not requested.

**Patched-but-passing** — the agent minimizes diff size and satisfies tests, but bypasses design intent. Tests pass. The system is still wrong.

**Memory theater** — session loss is compensated with large bootstrap dumps on every reset. Expensive, brittle, stale.

**Validation burn** — the full test suite runs on every micro-edit. Validation spend rivals implementation spend.

---

## Installation

**Clone the repo:**

```bash
git clone https://github.com/[usuario]/agent-execution-policy
```

For Claude Code, copy the skill folder to your skills directory:

```bash
cp -r agent-execution-policy/code-agent-finops ~/.claude/skills/
```

For Claude.ai, zip the skill folder and upload it:

```bash
cd agent-execution-policy
zip -r code-agent-finops.zip code-agent-finops/
```

Then go to Settings → Capabilities → Skills → Upload skill and select the zip.

---

**Download ZIP from GitHub:**

Click Code → Download ZIP, extract the archive, then re-zip only the `code-agent-finops/` folder:

```bash
cd agent-execution-policy-main
zip -r code-agent-finops.zip code-agent-finops/
```

Upload the resulting zip in Claude.ai under Settings → Capabilities → Skills.

---

Claude can load the skill automatically when a request matches its description and trigger patterns. No manual invocation required in most cases.

---

## Example: Mode B — local spec execution

**Situation:** A task is well-specified. The expected diff is small. The previous run burned more credits than expected and the result used a one-off special case instead of the existing integration point.

**Input:**
> The agent keeps touching files outside the feature boundary and the last run passed tests but used a one-off special case instead of the existing validation hook. The task is to add rate-limit headers to the API response layer. Write a tighter execution prompt.

**Skill output:**

Mode B — Local implementation. Main risks: drift and patched-but-passing. Hardness: high. Task is well-specified and the integration point is known.

```text
Implement only the rate-limit header addition to the API response layer
as described in the current task specification.

Scope:
- Modify only the response layer. Do not touch middleware, routing,
  auth, or any file outside the response handling boundary.
- Do not implement future tasks or add shared infrastructure the
  current task did not request.

Economy:
- Read only the files required to understand the existing response
  construction and the rate-limit data source.
- Prefer a localized edit over introducing new abstractions.

Semantic correctness:
- Use the existing validation hook. Do not create a one-off special case.
- Do not satisfy tests by bypassing the intended response pipeline.
- Follow design intent over smallest diff if they conflict.

Validation:
- During iteration, run only the response-layer tests.
- At the end, run the full API test suite once.
- Do not chase unrelated failures.

Stop condition:
- Stop as soon as the rate-limit headers appear correctly in the response,
  the implementation uses the existing hook, and the required tests pass.
- Do not continue to other tasks.
```

Why this hardness level: the integration point is known, the diff should be small, and the previous run demonstrated drift and patching risk. The semantic guardrail on the existing hook is the critical constraint — without it, the agent patches again.

---

## Trigger patterns

Claude can load the skill for requests matching patterns such as:

- "this is burning more credits than it should"
- "the agent is touching files it shouldn't"
- "it passed tests but the implementation feels wrong"
- "tighten this prompt"
- "the session got summarized, how do I recover cheaply"
- "audit this prompt for waste"
- "don't let it implement future specs"

Do not use for open-ended architecture exploration or brainstorming where wide search is the intent.

---

## File structure

```
code-agent-finops/
├── SKILL.md
└── references/
    ├── templates.md
    ├── metrics.md
    └── anti-patterns.md
```

---

## License

Commercial license. Included for personal and internal team use.
Redistribution and resale prohibited.


