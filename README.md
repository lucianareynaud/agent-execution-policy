# code-agent-finops

A Claude skill for agent execution policy: tightening scope, reducing credit burn, and recovering context cheaply in code-agent workflows.

Repository: https://github.com/lucianareynaud/agent-execution-policy

Usable in Claude Code and uploadable as a skill in Claude.ai.

---

## What you get

This repository includes a structured Claude skill with four files:

- `code-agent-finops/SKILL.md` — the core skill: operating modes, routing logic, policy stack, and execution behavior
- `code-agent-finops/references/templates.md` — five atomic prompt templates and three composite prompt patterns
- `code-agent-finops/references/anti-patterns.md` — a diagnostic guide for classifying bad runs, cheap context recovery, and repository hygiene issues
- `code-agent-finops/references/metrics.md` — evaluation and benchmarking guidance for measuring convergence cost

---

## What problem this solves

Most teams still evaluate code agents by speed and output volume.

Those signals are useful, but shallow.

A workflow can look productive and still be economically sloppy. A change can pass tests and still violate the design. “Context recovery” can quietly become token burn with better branding.

This skill treats prompt design as execution policy.

Its job is not to produce the tightest prompt or the loosest one. Its job is to produce the cheapest execution contract that still preserves semantic correctness.

### Failure modes it targets

**Drift burn**  
The agent reads too much, touches unrelated files, or scaffolds future work that was never requested.

**Patched-but-passing**  
The agent minimizes diff size and satisfies tests, but bypasses design intent. The tests pass. The implementation is still wrong.

**Memory theater**  
Session loss is “solved” by repeatedly dumping large bootstrap context into every new run. Expensive, brittle, stale.

**Validation burn**  
The full test suite runs on every micro-edit. Validation cost starts rivaling implementation cost.

---

## How this fits into your workflow

This skill is best used **between sessions**, not inside the middle of a bad run.

The typical pattern is:

1. a Claude Code session went wrong  
2. the agent drifted, burned more credits than expected, or produced something that passed tests but still feels semantically off  
3. you open Claude.ai with this skill available  
4. you describe what went wrong  
5. the skill generates a calibrated execution contract  
6. you paste that contract as the first instruction in the next Claude Code session

In other words, this skill is not mainly there to “help code.” It is there to improve the policy that governs the next coding session.

### The loop

```text
session ends badly
      ↓
open Claude.ai with the skill available
describe what went wrong
      ↓
skill identifies the failure mode, selects the operating mode,
and produces a prompt contract
      ↓
paste that contract as the first instruction in the next Claude Code session
      ↓
agent executes inside the contract
```

Two minutes between a bad run and a better one.

---

## Example

A session added a metrics exporter. Tests passed. But the diff duplicated logic from an existing collector, touched three unrelated files, and introduced an abstraction nothing else uses.

### You open Claude.ai and say:

> The agent added a metrics exporter and tests pass, but it duplicated logic from an existing collector, touched unrelated files, and created an abstraction nothing else uses. I need to redo this. Write me a tighter execution prompt.

### The skill responds with something like:

Mode B — Local implementation.  
Risks: drift and patched-but-passing.  
Hardness: high.

```text
Implement only the metrics exporter as specified.

Scope:
- Modify only the exporter layer.
- Do not touch the existing collector, routing, or any file outside the exporter boundary.
- Do not create shared abstractions the current task did not request.

Economy:
- Read the existing collector before writing anything.
- Use its collection interface. Do not duplicate it.

Semantic correctness:
- The exporter must consume the collector's output, not reimplement it.
- Do not satisfy tests by bypassing the intended pipeline.

Validation:
- During iteration, run only exporter tests.
- At the end, run the full observability suite once.

Stop condition:
- Stop when the exporter works via the existing collector and the required tests pass. Nothing else.
```

### Then you paste that into the next Claude Code session.

That is the intended usage pattern.

---

## Installation

### Option 1: clone the repository

```bash
git clone https://github.com/lucianareynaud/agent-execution-policy.git
cd agent-execution-policy
```

### Install for Claude Code

Copy the skill folder into your Claude skills directory:

```bash
mkdir -p ~/.claude/skills
cp -r code-agent-finops ~/.claude/skills/
```

### Install for Claude.ai

Create a zip containing only the `code-agent-finops/` folder:

```bash
cd agent-execution-policy
zip -r code-agent-finops.zip code-agent-finops/
```

Then in Claude.ai go to:

**Settings → Capabilities → Skills → Upload skill**

and upload `code-agent-finops.zip`.

---

## Install from GitHub ZIP download

If you prefer downloading the repository as a ZIP from GitHub:

1. Click **Code → Download ZIP**
2. Extract the archive
3. Re-zip only the `code-agent-finops/` folder

Example:

```bash
cd agent-execution-policy-main
zip -r code-agent-finops.zip code-agent-finops/
```

Then upload that zip in Claude.ai under:

**Settings → Capabilities → Skills**

---

## How Claude knows when to use it

Claude can load the skill automatically when your request matches the description and trigger patterns closely enough.

That usually means you do **not** need a special command.

The skill is likely to activate for requests such as:

- “this is burning more credits than it should”
- “the agent is touching files it shouldn’t”
- “it passed tests but the implementation feels wrong”
- “tighten this prompt”
- “the session got summarized, how do I recover cheaply”
- “audit this prompt for waste”
- “don’t let it implement future specs”

Do not use this skill for open-ended architecture exploration or brainstorming where wide search is the point.

---

## File structure

```text
code-agent-finops/
├── SKILL.md
└── references/
    ├── templates.md
    ├── metrics.md
    └── anti-patterns.md
```

---

## What this is not

This is not a generic prompt pack.

It is not a guarantee of lower spend on every task, every repo, or every agent.

It does not replace engineering judgment.

It works best for bounded implementation work where scope, design intent, and validation boundaries can be made explicit.

That limitation is intentional.

This is a method with an operating envelope, not a magic trick.

---

## License

Commercial license.

Included for personal and internal team use. Redistribution and resale are prohibited.
