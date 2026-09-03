---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 6 — Structured Data Extraction

> [!NOTE] What the blueprint states
> Extraction from **unstructured documents**, **JSON-schema validation**, high
> accuracy, **graceful edge cases**, downstream integration. Primary domains
> **D4, D5**. **No official sample question** · `extraction_pipeline` in all three
> CyberSkill sittings.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "**schema**-valid every time" | Tool input schema + `tool_choice`, then validate. Not prompt phrasing |
| "the field is **sometimes absent**" | Nullable and optional — never required. Required forces invention |
| "a **new category** keeps appearing" | Enum plus `"other"` and a detail string |
| "**high accuracy**" + "**downstream** integration" | Validation-retry, and provenance carried as structured fields |
| "**100,000** documents" / "cost matters" | Batch API — 50% discount, `custom_id` correlation |
| "within **30 minutes**" / "user is waiting" | Synchronous. Batch turnaround is up to 24h |
| "`context_length_exceeded`" | Chunk the **input** |
| "reviewers can only check **some** of it" | Confidence calibration + stratified sampling |
| "the totals **don't add up**" | Emit both, flag the mismatch. Never silently correct |

> [!IMPORTANT] The recurring split is fabrication vs absence
> When information is genuinely **not in the document**, no prompt technique
> recovers it — instruct `null` and accept it. When it *is* present but formatted
> unpredictably, that is a recall problem and **few-shot across varied document
> structures** is the answer. Misreading which one you are in produces the most
> confident wrong answers in this scenario.

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Guaranteed output shape | **Tool input schema + `tool_choice: {"type":"tool","name":...}`** | `{"type":"any"}` forces *a* tool, not *the* tool |
| Guaranteed-valid tool inputs | **`strict: true`** on the tool definition | Schema conformance at the API boundary |
| Missing data handled honestly | **Nullable fields + "return null when not stated"** | Making it required is an instruction to hallucinate |
| Open-ended categories | **Enum + `"other"` + free-text detail** | Bounded schema, lossless capture |
| Bad extractions corrected | **Validation-retry loop** feeding back the document, the failed extraction, and the **specific** error | A bare retry re-rolls the same dice |
| Format variety | **Few-shot across varied structures** — variety, not volume | Shows the canonical shape under different surfaces |
| Bulk cost | **Message Batches API** — 50% off, `custom_id`, results in any order | Correlate by `custom_id`; retry only the failed ones |
| Latency-bound work | **Synchronous `/v1/messages`** | Segment the workload by latency need, don't pick one API for everything |
| Oversized input | **Chunk the input** | `max_tokens` governs output |
| Scarce review capacity | **Field-level confidence**, calibrated on a labelled set, **stratified** sampling | Aggregate accuracy hides per-field failure |
| Traceability downstream | **Provenance in a structured field**, never inline prose | Prose provenance cannot be queried or validated |

Task statements in play: **4.1, 4.3, 4.4, 4.5, 5.5**.

---

## Trade-offs — what each mechanism costs

- **Batch** halves cost and forfeits latency. The SLA arithmetic is arrival wait **+ up to 24h turnaround + retry headroom** — budget all three, not just the cadence.
- **Strict schemas** buy downstream safety and cost graceful degradation: an over-tight schema turns a partial extraction into a total failure.
- **Validation-retry** buys accuracy and costs tokens and latency per document; cap the loop, and only retry what a specific error says is retryable.
- **Few-shot** buys consistency and costs context on every document in the run.
- **Confidence routing** buys reviewer leverage and is only as good as its calibration — an uncalibrated score is a proxy (`DF1`), not a measurement.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Ask for JSON in the prompt and parse the reply | `DF2` | Raises the odds. Schema + `tool_choice` guarantees the shape |
| `tool_choice: {"type":"any"}` to force the extraction tool | `DF7` | Forces *a* tool, not *that* tool |
| Make every field required so nothing is skipped | `DF2` | Forces fabrication when the document is silent |
| Retry to recover a field that isn't in the document | `DF4` | Solves a recall problem you don't have |
| Regex over the prose as a fallback | `DF1` | Pattern as a proxy for meaning |
| Keep expanding the enum as new categories appear | `DF5` | Unbounded schema churn; `"other"` + detail is bounded |
| Silently correct financial figures that don't reconcile | `DF6` | Destroys the discrepancy, which is the finding |
| Raise `max_tokens` on `context_length_exceeded` | `DF4` | Wrong budget entirely |
| Re-submit the whole batch when some documents fail | `DF5` | Retry only the failed `custom_id`s |
| Send everything through Batch to capture the discount | `DF4` | Misses the latency-bound segment |
| Send everything synchronously for simplicity | `DF5` | Discards the 50% saving on work that never needed to be fast |
| Automate on aggregate accuracy across all fields | `DF1` | Aggregate is a proxy; risk is per-field |
| Sample randomly to check coverage | `DF1` | Random sampling doesn't cover the strata that matter |
| Post-process to normalise what the model should normalise at extraction time | `DF7` | Adds a component instead of fixing the extraction contract |
| Record the source as a sentence in the output text | `DF6` | Provenance must be a field |

---

## Drill this scenario

- [[Answer Patterns Index]] § Part 3 drill set **3 (Grounding / null)**
- Unified Bank `extraction_pipeline` key — the largest single cluster in the vault
- [[D4 - Prompt Engineering & Structured Output]] § 4.5 for the batch SLA arithmetic
- Handbook: [[05-extraction-pipeline]] · [[08-quick-reference-tables]]

**Back to:** [[Scenario Index]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]]
