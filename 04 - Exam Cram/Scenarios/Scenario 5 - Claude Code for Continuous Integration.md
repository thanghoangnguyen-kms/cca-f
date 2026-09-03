---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 5 — Claude Code for Continuous Integration

> [!NOTE] What the blueprint states
> A **CI/CD pipeline** running automated code review, test generation, and PR
> feedback. Prompts that give **actionable feedback** and **minimise false
> positives**. Primary domains **D3, D4**. Official sample questions Q10–Q12 ·
> **no third-party bank tests this scenario**.

> [!WARNING] Under-drilled relative to how often it is tested
> One passer reported "quite a few questions on git pipeline commands, more than
> I expected from the mock." Combined with D3 being the most-failed domain, this
> is the highest-leverage scenario in the set per hour spent.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "runs in **CI** / non-interactive / the pipeline hangs" | `claude -p`. Without it, Claude Code waits for a human who isn't there |
| "the pipeline needs to **parse** the result" | `--output-format json` |
| "too many **false positives**" | A specification problem: explicit criteria and severity thresholds |
| "feedback must be **actionable**" | File, line, why it matters, and a suggested fix — not a verdict |
| "**blocks the merge** / must not merge if…" | A deterministic gate, not a prompt instruction |
| "reviewing **many files** at once, findings are inconsistent" | Multi-pass or multi-instance architecture |
| "**before merge** / blocking the developer" | Synchronous API. Batch latency is disqualifying |

> [!IMPORTANT] "Minimise false positives" is a prompt-engineering requirement
> The exam answer is almost never "use a stronger model" or "tell it to be more
> careful". It is **explicit criteria** — what counts as a finding, at what
> severity, with what evidence — plus **few-shot examples** of the boundary cases.

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Runs unattended in a pipeline | **`claude -p`** (`--print`) | Non-interactive mode; the default waits for input |
| Machine-readable result | **`--output-format json`** | Lets the pipeline branch on the result |
| Findings in a fixed shape | **Tool input schema + `tool_choice`**, then validate | "Return JSON please" in the prompt is not enforcement |
| Fewer false positives | **Explicit criteria in the prompt** + severity thresholds | Names what a finding *is*, so the model stops guessing |
| Consistent judgement on edge cases | **Few-shot examples** — including negatives that should *not* be flagged | Shows the boundary rather than describing it |
| Merge blocked on a hard rule | **Hook / gate in code** | Compliance-shaped requirement; a prompt cannot guarantee it |
| Many files, degrading quality | **Split into focused passes or instances** | Reviewing 10+ files at once causes attention dilution and contradictory findings |
| A pre-merge check | **Synchronous `/v1/messages`** | Batch is up to 24h — unusable for anything blocking |
| Team-wide CI conventions | **Project `CLAUDE.md`** + `.claude/rules/` with `paths:` | Committed, so the pipeline and the humans agree |

Task statements in play: **3.6, 4.1, 4.2, 4.6**.

---

## Trade-offs — what each mechanism costs

- **Explicit criteria** cut false positives and cost recall — an over-tight rule hides real defects. The stem tells you which error is worse; read it.
- **Few-shot examples** buy consistency and cost tokens on every invocation, which in CI means on every push.
- **Splitting into focused passes** buys quality and costs runtime and money per PR. Fine on a nightly sweep, painful on a blocking gate.
- **A blocking gate** buys guaranteed enforcement and costs developer velocity when it misfires — hence fail-closed only for things that genuinely must never ship.
- **Synchronous calls** cost the 50% batch discount and buy the only latency a pre-merge check can tolerate.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Run `claude` without `-p` in the pipeline | `DF7` | Hangs waiting for interactive input |
| Use the Message Batches API for the pre-merge gate | `DF4` | Solves cost; the constraint is latency. Up to 24h against a blocking check |
| Meet a 30-minute SLA with hourly batch runs | `DF4` | Cadence isn't the binding term — batch turnaround still allows up to 24h |
| Switch to a stronger model to cut false positives | `DF2` | Raises the odds; the specification is what's missing |
| Add "please be more careful and avoid false positives" | `DF2` | Same family, more politely |
| Instruct the model to double-check its own findings | `DF2` | Self-checking is not a gate |
| Enforce "never merge on a critical finding" in the prompt | `DF2` | A must-never rule belongs in code |
| Raise `max_tokens` when a large diff overflows | `DF4` | `max_tokens` is the *output* budget. Chunk the input |
| Review all 15 changed files in one pass to save cost | `DF5` | Attention dilution; contradictory findings |
| Assume `--bare` still runs hooks and loads `CLAUDE.md` | `DF7` | It does not |
| Report findings as prose in the PR comment only | `DF6` | Loses the structure the pipeline needs to gate on |
| Rewrite the reviewer prompt when the *upstream* output contract changed | `DF7` | Repair the contract, don't patch the consumer |

---

## Drill this scenario

- Official items **Q10–Q12** in `05 - Practice/Exam Guide - Sample Questions/`
- `05 - Practice/Vault-authored - Claude Code Scenario Drills/` Q11–Q20 — the only extended drilling that exists for this frame
- Connectry MCP task statement **3.6**
- Handbook: [[09-cli-flags]] · [[06-claude-code-operations]] · [[00-golden-rules-cheatsheet]]

**Back to:** [[Scenario Index]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]
