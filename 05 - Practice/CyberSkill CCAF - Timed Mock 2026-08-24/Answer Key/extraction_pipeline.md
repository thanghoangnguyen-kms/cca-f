---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - extraction-pipeline
date: 2026-08-24
status: done
---

# Timed Mock 2026-08-24 — `extraction_pipeline` Answer Key

**15 answers** — Q2, 5, 6, 7, 10, 12, 13, 14, 17, 24, 25, 27, 31, 42, 59. Scored **10/15 (67%)**.

Question numbers are this sitting's own and interleave with the other domains; they match [../Questions.md](../Questions.md). Back to [../README.md](../README.md). Every entry now reproduces its stem verbatim from [../Questions.md](../Questions.md) above the answer, so you can read this file without switching.

> [!NOTE] Every answer here is grader-authoritative
> These are the site's own `correct_key` values as rendered on the review page. Where an item was answered correctly, the correct answer *is* the recorded selection. Distractors were not recoverable — see [../README.md](../README.md) § Fidelity.

---

## ❌ Missed — work these first

### Q13 — Line items don't sum to the grand total (18% of invoices)

> Your extraction pipeline processes invoices and extracts line items, subtotals, tax amounts, and grand totals. During evaluation, you discover that in 18% of extractions, the sum of extracted line item amounts doesn't match the extracted grand total—sometimes due to OCR errors in the source document, sometimes due to extraction mistakes by the model. Downstream accounting systems reject records with mismatched totals.
>
> What's the most effective approach to improve extraction reliability?

- **Correct: A.** Add a "`calculated_total`" field where the model sums extracted line items alongside a "`stated_total`" field. Flag records for human review when values differ.
- **You answered C.** "Add few-shot examples demonstrating invoices where extracted line items sum correctly to the stated total, encouraging the model to produce mathematically consistent extractions."

**Why A wins.** Read the cause in the stem: *"sometimes due to OCR errors in the source document."* When the source itself is wrong, no amount of prompting can fix it — the mismatch is a real signal that must be **detected and surfaced**, not smoothed over. A makes the discrepancy explicit in the output schema, which turns an invisible corruption into a routable review item. C is actively harmful here: teaching the model that line items *should* sum to the total pressures it to silently reconcile them, destroying the evidence that something went wrong.

> [!IMPORTANT] The generalizable rule — you missed this one twice
> **Few-shot examples fix how the model behaves. They cannot fix bad input, and they must never be used to suppress a discrepancy you need to see.** Compare with your **Q14** miss, where few-shot *was* the answer — the difference is the cause, not the technique.

**Takeaway.** Mismatch between two derivable values → emit both fields and flag the delta. Don't prompt the disagreement away.

---

### Q14 — Valid JSON, but empty `citations` / `methodology` (5%)

> After implementing tool use with strict schema definitions, JSON syntax errors are eliminated, but 5% of extractions still have valid JSON with empty arrays or null values for required fields like citations and methodology. Spot-checking reveals that source documents contain this information, but in varied formats—inline citations vs. bibliographies, methodology sections vs. details embedded in introductions.
>
> What's the most effective way to address these failures?

- **Correct: D.** Add few-shot examples demonstrating extractions from documents with varied structures—showing how to identify citations in different formats and locate methodology details across section types.
- **You answered C.** "Modify your schema to make citations and methodology optional, and flag incomplete records for manual review rather than failing validation."

**Why D wins.** Again, read the cause: *"source documents contain this information, but in varied formats."* The data **is there** and the model is failing to **recognize** it — a capability gap, which is exactly what few-shot examples close. C surrenders: it relabels a fixable extraction failure as an acceptable outcome and dumps 5% of volume onto human reviewers forever.

> [!WARNING] Q13 and Q14 are a matched pair, and you inverted both
> You applied few-shot where verification was needed (Q13) and verification/review where few-shot was needed (Q14). The diagnostic is one question — **is the information present and correct in the source?**
>
> | Source state | Model's failure | Fix |
> |---|---|---|
> | Info present, varied format | can't *find* it | **few-shot examples** showing the variants (Q14, Q31) |
> | Info absent | invents it | **instruct null-when-absent** (Q24, Q25) |
> | Info present but *inconsistent* | can't *verify* it | **emit both values + flag** (Q13, Q17) |
>
> Never choose "make the field optional and review by hand" when the field is genuinely extractable. That is capitulation dressed as pragmatism.

**Takeaway.** Present-but-varied → few-shot. Absent → null. Contradictory → flag.

---

### Q27 — `Message Batches API` under a 30-hour SLA at 99.9%

> Documents arrive continuously throughout business hours and need structured data extracted. To reduce costs, you want to use the `Message Batches API` (50% discount, up-to-24-hour processing window). Your SLA specifies that extraction results must be available within 30 hours of document arrival with 99.9% reliability.
>
> Which batching strategy is most appropriate?

- **Correct: C.** Submit batches every 4 hours containing documents from that window.
- **You answered B.** "Submit a single batch at end of day containing all documents from that day."

**Why C wins.** Do the arithmetic. The SLA is 30 hours from **document arrival**, not from submission, and 24 hours is the batch limit. With one end-of-day batch, a document that arrived at 9am waits ~9 hours to be submitted, then up to 24 hours in the batch — ~33 hours, already blown before anything goes wrong. And **99.9% reliability needs retry headroom**: if a batch fails you must be able to resubmit inside the window. B leaves none. Four-hour windows cap pre-submission wait at 4 hours, so worst case is ~28 hours with time in hand for a resubmission.

> [!IMPORTANT] 24 hours is an **expiration**, not a worst-case latency — verified against official docs
> Most batches finish within an hour, but requests still incomplete at 24 hours come back with result type **`expired`** and must be resubmitted from scratch. They do not merely arrive late. And **no SLA is documented at all**: "processing may be slowed down based on current demand and your request volume… you may see more requests expiring after 24 hours."
>
> That is what makes retry headroom **mandatory** rather than merely prudent, and it is the real reason B fails. Retry with `custom_id` so you resubmit only the failed items.
> Source: <https://platform.claude.com/docs/en/build-with-claude/batch-processing>

**Treat these as arithmetic, not vibes.** Compute **max wait before submission + 24h ≤ SLA**, then ask whether a failed or expired batch could still be resubmitted inside the SLA. Both conditions must hold — "once a day" is intuitive and almost always wrong when the SLA sits only slightly above 24 hours.

**Takeaway.** Batch cadence must leave slack for the full window *plus* one retry, measured from arrival.

---

### Q42 — 12% semantic errors pass schema validation; 20% review capacity

> After deployment, you find that 12% of extractions contain semantic errors that pass JSON schema validation (e.g., a duration like "30 minutes" incorrectly placed in an ingredient quantity field). Human reviewers have capacity to check only 20% of extractions.
>
> Which approach most effectively allocates reviewer attention?

- **Correct: A.** Have the model output field-level confidence scores, then calibrate review thresholds using a labeled validation set.
- **You answered C.** "Prioritize review of all extractions where required fields are empty or explicitly marked as not found."

**Why A wins.** The errors described are **semantically wrong values in populated fields** — "30 minutes" sitting in an ingredient quantity. Those records are complete and schema-valid; emptiness is uncorrelated with the failure mode, so C sends reviewers to look at exactly the records that aren't broken. A gives you a *ranking* signal, and the labeled validation set is what turns raw confidence into a threshold that actually fills 20% capacity with the highest-yield 20%.

> [!WARNING] The trap: an "empty field" heuristic is cheap, and cheap ≠ targeted
> With finite review capacity, the winning answer is always the one that **ranks by likelihood of error** and **calibrates the cut-off against labeled data**. A filter that keys off a property the errors don't have is worthless no matter how easy it is to implement. Note also what makes A complete rather than half-right: confidence scores *alone* are uncalibrated — the labeled validation set is not decoration.
>
> **Status of this reasoning:** statistically sound, but *not* documented Anthropic doctrine — no official page covers confidence calibration. Official guidance on reducing hallucination covers letting the model say "I don't know", direct quotes, and citation verification. Defend the argument on its merits; don't cite it as documented guidance.

**Takeaway.** Scarce review capacity → per-field confidence + thresholds calibrated on labeled data.

---

### Q59 — `property_type` enum keeps failing validation (8%)

> Your extraction uses tool use with a JSON schema where `property_type` is defined as an enum: `['house', 'apartment', 'condo', 'townhouse']`. After deployment, 8% of extractions fail schema validation. Investigation reveals listings mention many uncommon property types—"studio", "loft", "duplex", "mobile home", "tiny house", "converted warehouse"—and new types continue appearing regularly.
>
> What's the most effective long-term solution?

- **Correct: B.** Add an "other" value to your enum with a separate `property_type_detail` string field for specifics when "other" is selected.
- **You answered C.** "Change `property_type` from an enum to a free-form string and implement a normalization step in post-processing."

**Why B wins.** B keeps the closed vocabulary that makes the field queryable *and* gives the long tail a legal home, so validation stops failing without any information being lost. C throws away the constraint entirely — now every consumer depends on a normalization step that must be maintained forever and will itself drift, and you have reintroduced exactly the free-text inconsistency that **Q10** and **Q31** punish.

> [!IMPORTANT] The enum escape hatch is a named CCA-F pattern
> Enum too narrow → **`"other"` + a detail field**. Not free-form-plus-cleanup, and not an ever-growing enum you redeploy every time a new type appears. This is the same instinct as **Q24** (null + "not found" marker rather than a guess): give the model a legal way to say "none of the above" so it never has to choose between lying and failing.

**Takeaway.** Keep the enum closed; add `"other"` + `<field>_detail` for the tail.

---

## ✅ Answered correctly

The option shown is the keyed correct answer.

### Q2 — Menus with inconsistent price/dietary formatting

> Your extraction pipeline processes restaurant menus and must output structured JSON with fields for item names, descriptions, prices, and dietary tags. Some menus use inconsistent formatting—prices as "$12" vs "12.00", dietary info as icons vs text.
>
> What's the most reliable approach?

**D.** Define a strict output schema and include format normalization rules in your prompt.
*Takeaway: schema constrains the shape; the prompt carries the normalization rules. Both, not either.*

### Q5 — Which failure retries help LEAST

> Your extraction system implements automatic retries when validation fails. On each retry, the specific validation error is appended to the prompt. This retry-with-error-feedback approach resolves most failures within 2-3 attempts.
>
> For which failure pattern would additional retries be LEAST effective?

**D.** The model extracts "et al." for co-authors when the full list exists only in an external document not in the input.
*Takeaway: retry-with-error-feedback fixes model mistakes, never missing input. Information not in the context cannot be retried into existence.*

### Q6 — `lookup_citations` fires before `extract_metadata`

> Your pipeline uses a tool called `extract_metadata` with a JSON schema for paper details. You've also defined `lookup_citations` and `verify_doi` tools for enrichment. During testing, you notice that when users include requests like "extract the metadata and tell me how cited it is," Claude sometimes calls `lookup_citations` first, which fails because it needs the DOI that `extract_metadata` would provide.
>
> What's the most effective way to ensure structured metadata extraction happens first?

**C.** Set `tool_choice` to `{"type": "tool", "name": "extract_metadata"}` and process the enrichment requests in subsequent turns after receiving the extracted metadata.
*Takeaway: `tool_choice` forces the first call. Real ordering dependencies are enforced turn-by-turn, not by prompt pleading.*

### Q7 — Standard monthly vs urgent 30-minute reports

> Your extraction system processes two document types: standard monthly reports (archived after processing) and urgent exception reports (must trigger business alerts within 30 minutes of receipt). Both use the same JSON schema. You want to minimize API costs while meeting latency requirements.
>
> How should you architect the processing pipeline?

**D.** Route standard reports to the `Batch API` for 50% cost savings, and route urgent exception reports to the real-time `Messages API`.
*Takeaway: split by latency requirement, not by document type. Same schema, two transports.*

### Q10 — Five priority levels, no invented labels

> An extractor must label each support ticket with one of five priority levels. To stop the model from inventing new labels, you should:

**B.** Constrain the field to the five allowed values in the schema or tool definition, and reject anything else.
*Takeaway: enforce closed vocabularies in the schema, not in prose. Compare **Q59** for when the vocabulary is genuinely open-ended.*

### Q12 — Contract longer than the context window

> A contract is too long to fit in one context window, and you need fields from across the whole document. The dependable approach is to:

**B.** Chunk the document with slight overlap, extract per chunk, then merge and reconcile the fields.
*Takeaway: overlap exists so facts straddling a boundary survive; reconciliation is a required step, not optional.*

### Q17 — Strongest integrity check on a receipt

> An extractor pulls line items and an invoice total from a receipt. The strongest integrity check before accepting the output is to:

**B.** Verify that the line items sum to the extracted total, and on a mismatch retry or flag the record.
*Takeaway: the same rule as **Q13** — cross-check derivable values and route disagreements.*

### Q24 — Schema field absent from the source

> A field the schema expects is simply not present in the source document. The extractor should:

**B.** Return null for that field and mark it as not found, leaving the rest of the extraction intact.
*Takeaway: null + explicit not-found marker. One missing field must not fail the whole record.*

### Q25 — Plausible-but-invented `attendee_count`

> Your system extracts event metadata (date, location, organizer, `attendee_count`) from news articles using a JSON schema with all nullable fields. During evaluation, you observe the model frequently generates plausible but incorrect values for fields not mentioned in the article—for example, outputting "500" for `attendee_count` when the source contains no attendance information.
>
> What's the most effective way to reduce these false extractions?

**B.** Add prompt instructions to return null for any field where information is not directly stated in the source.
*Takeaway: nullable in the schema is permission, not instruction. You must also tell the model to prefer null over a guess.*

### Q31 — "materials" extracted inconsistently

> Your extraction system parses e-commerce product descriptions to extract specifications like dimensions, weight, and materials into JSON. Despite having a well-defined schema, the model inconsistently extracts the "materials" field—sometimes returning "cotton blend", other times "Cotton/Polyester mix", and occasionally omitting the field when material information is clearly present in the source.
>
> What's the most effective way to improve extraction consistency?

**D.** Add few-shot examples showing 2-3 complete input-output pairs with standardized material description formats.
*Takeaway: format inconsistency on a present field → few-shot. Same rule as **Q14**.*

---

## Domain pattern summary

| Rule | Items |
|---|---|
| Match the fix to the cause: varied format → few-shot · absent → null · inconsistent → flag | Q13, Q14, Q24, Q25, Q31 |
| Cross-check derivable values; emit both and flag the delta | Q13, Q17 |
| Never prompt away a discrepancy you need to detect | Q13 |
| Retries can't fix missing input | Q5 |
| Closed vocabulary in the schema; `"other"` + detail for the tail | Q10, Q59 |
| Batch vs realtime is a latency-requirement split; cadence is arithmetic + retry headroom | Q7, Q27 |
| Force real tool ordering with `tool_choice`, resolve across turns | Q6 |
| Scarce human review → rank by calibrated confidence | Q42 |
| Chunk with overlap, then reconcile | Q12 |

**Related:** [[Weak Areas Deep Dive]] · [[Answer Patterns Index]] · [[D4 - Prompt Engineering & Structured Output]]
