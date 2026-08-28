---
tags:
  - CCA-F
  - handbook
  - domain-4
date: 2026-08-24
status: done
domain: "4 of 5"
---

# 05 — Extraction Pipeline

Covers: batch vs synchronous, schema design, few-shot, grounding/null,
tool_choice ordering, enums, confidence routing & segment evals, `custom_id`.
(Mock Q46–Q60; concept chapters 11, 12.)

---

## Core concepts (memorize)

### Batch API vs synchronous API
- **Batch API**: **50% cheaper**, asynchronous, for high-volume work with **no
  immediate-response need**. Results ready **when all complete OR after 24h,
  whichever first**; requests **expire after 24h** if not done.
- **Synchronous Messages API**: for **latency-bound** work / a user waiting.
- **Segment traffic by latency requirement**: batch the cost-sensitive bulk,
  use sync only for the truly latency-bound minority. (A 30-min alert **cannot**
  be served by batch, even hourly batches — batch rides the up-to-24h SLO.)
- **Batch SLA math**: `worst case = max wait before batching + up to 24h`.
  Leave margin. SLA 30h → every **4h** (4+24 = 28h, 2h buffer) > every 6h (=30h, none).
- **`custom_id`**: carries correlation/metadata and matches each result back to
  its request (**batch results may not be in input order**). Use it to retry
  **only** the failed items.
- A context-length error (e.g. `context_length_exceeded` — exact error-string
  name illustrative/unverified) → **chunk the input** and merge partial
  extractions. (It's an *input* problem; raising `max_tokens`, which is
  *output*, won't help.)

### Structured output & schema design
- Reliable JSON = **strict output schema (tool `input_schema`) + explicit
  format-normalization rules in the prompt** (e.g. "prices as decimal, 2 places";
  "dietary as enumerated tags"). Let the model **normalize during extraction**,
  not in brittle post-processing code.
- **Schema models the domain:**
  - Source has **multiple valid values** (contract amendments) → field captures
    **multiple values, each with source location + effective date.** Don't force
    "most recent" (lossy) or strip "superseded" sections (may still be effective).
  - Closed **enum** keeps failing on new real-world values → add **`"other"` +
    a `*_detail` string field.** Don't endlessly expand the enum (whack-a-mole),
    don't switch to free-form (loses validation), don't map "tiny house"→"house" (lossy).
  - Numbers must reconcile (invoice totals) → extract **`calculated_total` AND
    `stated_total`, flag mismatches for review.** Never silently auto-correct or
    "reconcile" financial data.
- **Schema guarantees shape, not recall or correctness.** Still need few-shot
  for recall and semantic validation for correctness.

### Few-shot examples (fix interpretation, format, recall)
- Examples are **"one of the most reliable ways to steer output format,"** and
  should be **diverse to cover edge cases** (wrap in `<example>` tags).
- Use when the problem is:
  - **Interpretation** — what counts as a skill, compound-phrase splitting
    ("Python and SQL"), explicit vs inferred, granularity.
  - **Format** — canonical shape of a free-text field ("cotton/polyester").
  - **Recall** — info appears in varied places (citations inline vs bibliography;
    methodology in intro vs a section). Schema strictness fixes shape, **not recall**.
- Construction order: system context → example user msg → example assistant
  answer → repeat a few → **real input last.**

### Grounding & hallucination control
- Nullable fields but the model **invents** values → instruct **"return null for
  any field not directly stated in the source."** Grounding by policy beats a
  second verification pass. Making fields **required forces** invention.
- **Retry-with-feedback fixes formatting/shape errors, NOT missing information.**
  If the data simply isn't in the input (e.g. author list only in an external
  doc), no feedback can conjure it — the model can only hallucinate. Distinguish
  "formatted wrong" from "data isn't there."
- For long docs, have the model surface **supporting quotes/locations** to
  reduce hallucination and make values verifiable.
- Computed/logical fields need **reasoning/scratchpad**, not just examples.

### Forcing tool order
- Tool B depends on A's output but Claude sometimes calls B first → force A with
  **`tool_choice: {"type":"tool","name":"A"}` on turn 1**, then return to `auto`
  so dependent tools run with A's output in context.
- `tool_choice:"any"` forces *a* tool, not the *right* one. Pinning A on **every**
  call means B never runs.

### Confidence routing & evaluation
- Limited review capacity → **model outputs field-level confidence; calibrate
  the threshold on a labeled validation set;** route the lowest-confidence items
  to humans (that's where errors cluster). Random sampling **measures** accuracy
  but **under-covers** errors.
- Semantic errors can **pass schema validation** ("30 minutes" in a quantity
  field) → schema alone can't catch them; a **calibrated confidence signal** can.
- Before automating on a "97% accurate" gate → **disaggregate accuracy by
  document type × field.** Aggregate metrics **hide** a weak segment. Use
  **stratified sampling** so QA covers every important segment.
- **Validation** catches *known* errors; **evaluation + human review** catch
  *new* errors, drift, unforeseen failure modes. Production systems need
  **continuous sampling/monitoring**, not just deploy-time testing.
- Human review should target cases the AI can **misinterpret / has multiple
  readings**, not just a raw confidence threshold. In sensitive domains
  (healthcare), use a **glossary + examples + an "unknown" option + human review**
  for ambiguous abbreviations — never unconstrained "best judgment."

---

## Decision rules

| Trigger | Answer |
|---|---|
| High volume, archived, no latency need | **Batch API** (50% off) |
| Must fire alert within N minutes | **Synchronous API** (batch can't guarantee it) |
| Choose batching interval for an SLA | `wait + 24h ≤ SLA` with **margin** |
| Retry only failed batch records | **`custom_id`**; chunk input on a context-length error (`context_length_exceeded` — name illustrative) |
| Inconsistent format / splitting / granularity | **Diverse few-shot examples** |
| Model invents values for absent fields | Instruct **return null when not stated** |
| Retry keeps failing on one pattern | If info is **absent from input**, retry can't fix it |
| Source carries multiple valid values | Schema: **value + location + effective date** |
| Enum fails on new values | **`"other"` + detail field** |
| Numbers don't reconcile | Extract **computed + stated**, flag mismatch |
| Tool B needs A's output but runs first | `tool_choice:{"type":"tool","name":"A"}` then `auto` |
| Limited reviewers, 12% semantic errors | **Calibrated field-level confidence** routing |
| "97% aggregate — safe to automate?" | **Segment/field analysis first** |
| Reliable structured JSON from messy input | **Strict schema + normalization rules in prompt** |

---

## Anti-patterns

- Sending **everything to the sync API** (wastes the 50% batch discount) or
  **everything to batch** when some work is latency-bound.
- Fixing recall by **retrying the same prompt** or with **regex over prose**.
- Making missing fields **required** (forces hallucination).
- **Post-processing normalization** for something the model can normalize at
  extraction time; **silent auto-correction** of financial numbers.
- **Endlessly expanding an enum** / switching to free-form / lossy mapping.
- Automating on **aggregate accuracy**; **random sampling** for error coverage.
- Trusting **uncalibrated** model confidence.
- Raising **`max_tokens`** to fix a context-length error (e.g. `context_length_exceeded` — exact name illustrative) (that's input).

---

## Key phrases to recognize

"Batch API 50% / up to 24h" · "segment by latency" · "SLA = wait + 24h + margin" ·
"custom_id" · "chunk the input" · "strict schema + normalization rules" ·
"value + location + effective date" · "'other' + detail" · "computed vs stated" ·
"diverse few-shot / cover edge cases" · "return null when not stated" ·
"retry fixes format not missing data" · "calibrated field-level confidence" ·
"disaggregate by segment / stratified sampling."

## Sources
- Batch processing — https://platform.claude.com/docs/en/build-with-claude/batch-processing
- Use examples (multishot prompting) — https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively
- Reduce hallucinations — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- Define tools / output schemas — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools
