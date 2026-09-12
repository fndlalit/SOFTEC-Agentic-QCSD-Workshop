# Workshop Lab — Copy-Paste Exercises

Six steps on this deliberately-flawed checkout app: **build a local knowledge graph (0) → Ideation → Refinement → Development → CI/CD (1–4) → Self-Learning (5)**, then a **Personal Adoption Roadmap**. The SDLC exercises build on each other (Refinement feeds Development; CI/CD verifies) and each ends by saving its learnings; Step 5 turns those into an instant handoff brief. Everything indexes and embeds with a **local on-device model — your code never leaves your machine** — and it's scoped token-cheap for a whole room on personal keys.

**Pick your prompt — each SDLC exercise (1–4) has two versions:**
- **Claude Code Users** — AQE skills / orchestrator (`/qcsd-ideation-swarm`, `qe-test-architect`, `qe-queen-coordinator`).
- **Non Claude Code Users** (Copilot, Codex, Gemini, …) — the same work as a generic step list via the AQE MCP tools.

Both write to the same report and end with **"Save learnings and persist patterns."** *(Steps 0 and 5 are MCP-tool calls — identical on every tool, no split.)*

**Before you start:** finish the [README](./README.md) Setup (clone → `npm install -g agentic-qe@3.14.1` → `aqe init --auto --with-<your-tool>` → `npm install`), then launch your agent here. **Don't skip `aqe init`** (it installs the agents, MCP config, and memory DB) and **run the exercises in order** (3 reads 2's output; 5 recalls what 0–4 saved). Paths are relative to the repo root.

---

## Exercise 0 — Warm-up: build the local knowledge graph + baseline (≈2 min)

> *Phase:* Setup · *Why:* before the fleet reasons about your code, give it a **map**. This runs entirely on your machine: static analysis plus a local embedding model, **no API key, no tokens**. Do it in your **terminal**, not through your coding agent.

```bash
aqe code index src/
aqe hg stats
aqe memory usage
```

**What you should see**

| Command | Expected on this repo |
|---------|----------------------|
| `aqe code index src/` | `Files indexed: 21` · `Nodes created: 102` · `Edges created: 117` · about 2 s |
| `aqe hg stats` | 140 nodes / 102 edges, broken down as function 80, file 26, module 22, test 12 |
| `aqe memory usage` | Entries 21 · Vectors 102 · Namespaces 1 |

The two node counts differ because they count different things: the indexer reports what it just created, `hg stats` reports every node type in the graph. Either is a fine "the map exists" check.

> *Heads-up:* if you have an LLM provider configured for AQE, indexing also runs an optional relationship-extraction pass that calls the model **once per file**. On this repo that is 21 calls. If you would rather not spend them, run the index before you export any provider key, or accept it once here. Lines reading `LLM relationship extraction failed` mean that pass was skipped. The graph is still complete; only the inferred design-pattern edges are missing.

---

## Exercise 1 — Ideation: gate the epic before any code

> *Phase:* Ideation · *Why:* apply the QE ideation lenses to the epic and render a release gate *before a line of code is written*.

**▸ Claude Code Users** — the orchestrated ideation swarm. `qcsd-ideation-swarm` is installed as a **skill**, not a command file, so if your Claude Code build does not offer it after a slash, ask for it by name instead ("Use the qcsd-ideation-swarm skill to ..."):

```
/qcsd-ideation-swarm

Analyze the guest-checkout epic in requirements/epic-checkout.md,
using user-stories.md and acceptance-criteria.md for context.
Save all reports under reports/01-ideation-swarm/.
Save learnings and persist patterns.
```

**▸ Non Claude Code Users** — the same assessment as explicit steps:

```
Assess the guest-checkout epic before any code is written. Read
requirements/epic-checkout.md (with user-stories.md and
acceptance-criteria.md for context), then:

1. Recommend the quality criteria that matter most (HTSM: capability,
   reliability, security, performance, usability, …)
2. Identify the top risks and score each by likelihood × impact
3. Validate requirements completeness and testability — flag gaps,
   contradictions, and unmeasurable acceptance criteria
4. Render a single GO / CONDITIONAL / NO-GO verdict with the top blockers
5. Save the assessment to reports/01-ideation-assessment.md
6. Save learnings and persist patterns
```

---

## Exercise 2 — Refinement: product factors on the checkout app

> *Phase:* Refinement · *Why:* break the product into its real elements (SFDIPOT) and turn them into prioritised test ideas — which Exercise 3 will use.

**▸ Claude Code Users** — the product-factors agent:

```
Use qe-product-factors-assessor to analyse the guest-checkout product
from requirements/epic-checkout.md and requirements/user-stories.md.
Produce a product-factors (SFDIPOT) assessment and save it to
reports/02-refinement-product-factors.md.
Save learnings and persist patterns.
```

**▸ Non Claude Code Users** — the same assessment as explicit steps:

```
Break the checkout product into its product factors before reasoning
about coverage. Read requirements/epic-checkout.md and
requirements/user-stories.md, then analyse the product across the
SFDIPOT dimensions:

  Structure, Function, Data, Interfaces, Platform, Operations, Time.

Then:
1. For each dimension, note what the requirements imply and produce
   prioritised test ideas
2. Save the assessment to reports/02-refinement-product-factors.md
3. Save learnings and persist patterns
```

---

## Exercise 3 — Development: generate tests from the refinement ideas

> *Phase:* Development · *Why:* turn Exercise 2's product-factors ideas into real, runnable tests for the highest-risk module.

**▸ Claude Code Users** — the test architect:

```
Use qe-test-architect to generate a comprehensive test file for the
payment-retry logic in src/lib/payment-retry.ts. Use the test ideas in
reports/02-refinement-product-factors.md as input, and include
property-based tests for the module's invariants.
Save the test file as tests/lib/payment-retry.architect.test.ts and a
short rationale to reports/03-development-tests.md.
Save learnings and persist patterns.
```

**▸ Non Claude Code Users** — the same as explicit steps:

```
Generate tests for the payment-retry logic in src/lib/payment-retry.ts:

1. Read reports/02-refinement-product-factors.md and src/lib/payment-retry.ts
2. Write a comprehensive vitest test file covering the happy path, edge
   cases, error paths, and the module's invariants (use property-style
   tests where useful — e.g. idempotency, backoff bounds, retry limits)
3. Make sure the file imports from src/lib/payment-retry.ts and runs
4. Save the test file as tests/lib/payment-retry.architect.test.ts and a
   short rationale to reports/03-development-tests.md
5. Save learnings and persist patterns
```

> *Tip:* after this runs, `npm test -- --run tests/lib/payment-retry.architect.test.ts` to see the generated tests actually execute.

---

## Exercise 4 — CI/CD: verify the module and decide on release

> *Phase:* CI/CD · *Why:* generate nothing new — *measure, scan, gate, and recommend*. The release decision on code that now has tests.

**▸ Claude Code Users** — the queen-coordinator orchestrates the verification fleet:

```
Use qe-queen-coordinator to run a verification-only quality assessment of
src/lib/payment-retry.ts (do NOT generate tests). Analyse coverage gaps
with risk scoring, security-scan the module, apply a 90% quality gate,
and give a GO / CONDITIONAL / NO-GO deployment recommendation with the
top blockers.
Save the consolidated report to reports/04-cicd-quality-assessment.md.
Save learnings and persist patterns.
```

**▸ Non Claude Code Users** — the same as explicit steps:

```
Run a verification-only quality assessment of src/lib/payment-retry.ts
and decide on release. Do NOT generate tests — assess what exists:

1. Analyse coverage gaps with risk scoring
2. Review the module for security issues
3. Apply a quality gate at a 90% threshold
4. Give a deployment recommendation (GO / CONDITIONAL / NO-GO) with the
   top release blockers, if any
5. Save the consolidated report to reports/04-cicd-quality-assessment.md
6. Save learnings and persist patterns
```

> *Note:* this app keeps its testable logic in `src/lib/` (payment, Luhn, validation, rate-limiting, email) — there is **no `src/services/`**. Scoped to one file so the run finishes fast; widen to `src/lib/` for a broader verification.

---

## Exercise 5 — Self-Learning: put the fleet's memory to work (≈5 min)

> *Why:* every exercise above ended with **"Save learnings and persist patterns."** Now feel the payoff — the fleet didn't just file those away, it can hand them back **consolidated, on demand**. That's institutional knowledge working *for* you. Same prompt for every tool.

```
Recall what the fleet has learned about this checkout app and consolidate
it into a one-page brief — top risks, testability gaps, contradictions,
and the release verdict — framed as either:
  • an onboarding brief for someone joining the project today, or
  • a handoff document for the next person enhancing the checkout app.

Pull the learnings from AQE memory (the patterns persisted across
Exercises 1–4; retrieve a specific one by its key if a recent learning
hasn't surfaced yet). Save the brief to reports/05-handoff-brief.md.
```

> *If nothing comes back:* the pattern store only persists when an embedder is configured (see the memory-layer step in the [README](./README.md)). Without one, every "Save learnings and persist patterns" above was a no-op and there is nothing to recall. Two ways forward, both legitimate:
>
> 1. Load the seed brain and recall from that: `aqe learning import -i seed/aqe-seed-patterns.json`, then re-run the prompt above.
> 2. Consolidate from the reports instead: "Read reports/01 through reports/04 and write the same one-page brief to reports/05-handoff-brief.md."
>
> Option 2 gets you the document. Option 1 is the one that demonstrates the point, which is that the fleet reconstructs the brief without re-reading anything.

> *Also expected:* lines mentioning `brain.rvf` or `VECTOR_SPACE_UNVERIFIED` during these steps. The persistent vector index stays unverified without native RuVector provenance and AQE falls back to SQLite, which is authoritative. Patterns are stored; only the ANN index is skipped. You may see `brain.rvf.corrupt-NNNN` files appear in `.agentic-qe/`. They are quarantined empty indexes, not lost data.

**Why this is the benefit.** You didn't re-read four reports — the fleet reconstructed the project's institutional knowledge in seconds from what each exercise saved, and a new teammate or the next run inherits all of it instantly. *(In Claude Code this capture is automatic — the ReasoningBank hooks + the `AQE Learning: N patterns loaded…` banner.)* That's the self-learning loop: agents that **remember** beat agents that start cold.

---

## After the runs — Apply PACT

For each report, ask:

- **Proactive?** Did it flag risk *before* you asked, or only answer the prompt?
- **Autonomous?** Did it decide what to inspect, or wait for your steers?
- **Collaborative?** Did it connect findings across concerns (and across exercises), or treat each in a silo?
- **Targeted?** Was the analysis fit to *this* checkout flow, or generic checklists?

In pairs, score each report 0–3 per property. Share the most surprising weakness.

> **Compare engines.** If your pair has both a Claude Code user and a non-Claude-Code user, diff the two reports for the same exercise: did the orchestrated swarm surface anything the step-list version missed (or vice versa)? That gap *is* the value of the orchestration layer.

---

## Your Adoption Roadmap — leave with a plan, not just reports

The point isn't the reports — it's what you do Monday. Fill this in for *your* context (≈10 min, in pairs):

1. **My context** — team, stack, and where quality hurts most today: ______
2. **The 70% I want back** — which clerical testing activity eats my team's time that an agent could take over *first*? ______
3. **First 3 agents I'll adopt** — pick from the fleet in `.claude/agents/v3/` (e.g. `qe-requirements-validator`, `qe-product-factors-assessor`, `qe-test-architect`, `qe-queen-coordinator`): ______
4. **First QCSD phase I'll start with** — Ideation gate, Refinement, Development, or CI/CD? ______
5. **One success metric (2 weeks)** — how will I know it worked? (e.g. contradictions caught *in refinement*, coverage on the riskiest module, faster GO/NO-GO calls): ______
6. **My first step on Monday** — the single smallest thing I'll actually do: ______

> Keep it small: one agent, one phase, one repo, one metric. The full fleet (60 agents and 86 skills, MIT-licensed) is already on your machine from `aqe init`, nothing held back. Start where the pain is.
