---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - extraction-pipeline
date: 2026-08-23
status: done
---

# Extraction Pipeline — New Mock Exam Answer Key

[← New Mock Exam index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **16** questions tagged `extraction_pipeline` in [Questions.md](../Questions.md). Numbers are the **sitting's original numbering**, so they interleave with the other three domains and are not contiguous — `Q3` here is `Question 3` there. All `[[#Q…]]` cross-references in this file point to other entries **within this file**.

**Answers:** **Q3** B · **Q4** B · **Q13** C · **Q18** C · **Q19** D · **Q21** B · **Q22** B · **Q27** B · **Q33** D · **Q35** D · **Q36** B · **Q46** B · **Q49** D · **Q51** A · **Q56** A · **Q57** D

**Verification:** Q3 ✅ · Q4 ✅ · Q13 ✅ · Q18 ✅ · Q19 ✅ · Q21 ✅ · Q22 ✅ · Q27 📘 · Q33 ✅ · Q35 ✅ · Q36 ✅ · Q46 🤔 · Q49 ✅ · Q51 📘 · Q56 ✅ · Q57 ✅
_✅ confirmed by the practice site's grader · 📘 matches the doc-verified key in `CyberSkill CCAF - Mock Exam/` · 🤔 reasoned judgment, not yet confirmed_

---

### Q3 — extraction_pipeline

**Correct: B — "Verify that the line items sum to the extracted total, and on a mismatch retry or flag the record."**

The document carries its own redundancy — the parts must sum to the whole. Cross-checking derived values against each other is a free, deterministic integrity test that catches both OCR damage and extraction mistakes before the record reaches downstream accounting.

**Wrong answers**

- **A. "Trust the total field because it is printed prominently."** — Visual prominence has nothing to do with extraction accuracy. A large, bold total is exactly as easy to misread as a small one.
- **C. "Check only that the total is a number."** — A type check confirms the field is well-formed, not that it's *correct*. `$1,240.00` misread as `$1.240.00` is still a number.
- **D. "Accept the first extraction without checking."** — No validation at all; the failure mode is a silently wrong ledger entry.

**Takeaway:** When the source contains an arithmetic or logical invariant, assert it. Schema validation proves the shape; invariants prove the content.

---

### Q4 — extraction_pipeline

**Correct: B — "Resubmit only the 300 failed documents after chunking them into smaller pieces, then combine the partial extractions."**

`custom_id` exists so you can correlate each result back to its source request. Only the 300 oversized documents failed, and their failure cause is real — they exceed the context window — so chunking is the actual fix. Reprocessing only the failures is 3% of the cost of a full re-run.

**Wrong answers**

- **A. "Reprocess the entire batch with prompt caching enabled to reduce the cost of retrying requests with identical system prompts."** — Caching trims the system-prompt cost but does nothing about document length; the 300 would fail again, and you'd have paid to re-run 9,700 already-successful documents.
- **C. "Resubmit the entire 10,000 document batch using a model tier with a larger context window."** — Even if a larger window fits them, you're re-billing 9,700 good extractions to fix 300 — and a bigger window only postpones the ceiling for the next oversized document.
- **D. "Increase the `max_tokens` parameter for the 300 failed documents and resubmit them in a new batch."** — `max_tokens` caps the **output** length. `context_length_exceeded` is an **input** problem; raising `max_tokens` makes it worse, since output tokens count against the same window.

**Takeaway:** `custom_id` is the retry key for the Batch API. And know which knob is input vs. output — `max_tokens` is output-only.

---

### Q13 — extraction_pipeline

**Correct: C — "Set `tool_choice` to {\"type\": \"tool\", \"name\": \"`extract_metadata`\"} and process the enrichment requests in subsequent turns after receiving the extracted metadata."**

There's a real data dependency: `lookup_citations` needs the DOI that `extract_metadata` produces. Forcing the first tool on the **first turn** guarantees the ordering, and leaving later turns unforced lets Claude run the enrichment tools once the DOI is actually in context.

**Wrong answers**

- **A. "Set `tool_choice` to 'any' so Claude must use a tool, combined with system prompt instructions prioritizing `extract_metadata`."** — `"any"` forces *some* tool, not a *specific* one; Claude can still pick `lookup_citations` first. The prompt instruction is advisory, which is exactly the guarantee you don't have today.
- **B. "Set `tool_choice` to 'auto' and reorder the tool definitions so `extract_metadata` appears first in the tools array, since Claude prioritizes earlier-listed tools."** — The stated premise is false: array position is not a documented priority mechanism. This is superstition dressed as configuration.
- **D. "Set `tool_choice` to {\"type\": \"tool\", \"name\": \"`extract_metadata`\"} for every API call in the pipeline, ensuring Claude always extracts metadata before any enrichment can occur."** — Right mechanism, wrong scope. Forcing it on *every* call means the enrichment tools can never fire; the pipeline extracts metadata forever and never answers "how cited is it."

**Takeaway:** `tool_choice: {"type":"tool","name":...}` is a per-request lever. Force the first turn, then release.

---

### Q18 — extraction_pipeline

**Correct: C — "Submit batches every 4 hours containing documents from that window"**

Do the arithmetic against the SLA. A document arriving at the *start* of a window waits the full window before submission, then up to 24 hours for the batch. 4-hour windows: 4 + 24 = **28 hours**, inside the 30-hour SLA with 2 hours of headroom for the 99.9% reliability requirement.

**Wrong answers**

- **A. "Submit batches every 6 hours containing documents from that window"** — 6 + 24 = **30 hours** exactly. Zero margin: any document that arrives at the top of a window and hits the full 24-hour batch window lands precisely on the deadline, so a 99.9% guarantee is impossible.
- **B. "Submit a single batch at end of day containing all documents from that day"** — A document arriving just after the previous cutoff waits up to 24 hours to be *submitted*, then up to 24 hours to process: **~48 hours** worst case, far past the SLA.
- **D. "Use the real-time API for all documents instead of batch processing"** — Meets the SLA trivially but forfeits the 50% discount, which is the entire stated reason for batching.

**Takeaway:** Batch latency = *your* accumulation window + the batch API's up-to-24-hour window. Budget both, and leave margin — an SLA met exactly is an SLA missed at scale.

---

### Q19 — extraction_pipeline

**Correct: D — "Route standard reports to the `Batch API` for 50% cost savings, and route urgent exception reports to the real-time Messages API."**

Segment traffic by latency requirement. The monthly reports are archived and have no latency need, so they take the 50% discount; the exception reports have a hard 30-minute alert window, so they take the synchronous path. Same schema, two routes.

**Wrong answers**

- **A. "Submit all documents to the real-time Messages API to ensure consistent processing latency across document types."** — Consistent and needlessly expensive. You pay full price for the bulk of the volume that had no latency requirement at all.
- **B. "Submit all documents to the `Batch API` with `custom_ids` for tracking. When results arrive, immediately process urgent documents and trigger delayed alerts for exceptions."** — The phrase "delayed alerts" concedes the failure. The batch window runs up to 24 hours; no amount of promptness *after* results arrive can meet a 30-minute SLA.
- **C. "Queue all documents and submit hourly batches, flagging urgent documents for expedited handling when batch results return."** — Hourly submission doesn't shorten the batch's own up-to-24-hour completion window, and there is no priority lane within a batch. A flagged urgent document can still miss 30 minutes by a wide margin.

**Takeaway:** Batch is a *throughput* discount, not a latency tier. Split the traffic; never try to make batch behave synchronously.

---

### Q21 — extraction_pipeline

**Correct: B — "Add an 'other' value to your enum with a separate `property_type_detail` string field for specifics when 'other' is selected."**

The escape-hatch pattern. The enum keeps its clean, queryable values for the common cases, `"other"` gives the model a valid choice when nothing fits — so validation stops failing — and `property_type_detail` preserves the actual text so you don't lose information and can mine it for the next enum value.

**Wrong answers**

- **A. "Continuously expand the enum to include newly observed property types and add monitoring for additional edge cases."** — Perpetual chase. The question states new types "continue appearing regularly," so you are permanently one deploy behind, and validation keeps failing in the gap.
- **C. "Change `property_type` from an enum to a free-form string and implement a normalization step in post-processing."** — Throws away the constraint entirely. You trade an 8% validation-failure rate for 100% unconstrained output and an open-ended normalization mapping to maintain — with no signal at all about which values were unexpected.
- **D. "Add few-shot examples to your prompt demonstrating how to map unexpected property types to the closest existing enum value."** — Forces lossy coercion: a "converted warehouse" becomes "house" and the data is now silently wrong. Validation passes; correctness doesn't.

**Takeaway:** Constrained vocabularies need an escape hatch plus a detail field. Never make the model choose between failing validation and lying.

---

### Q22 — extraction_pipeline

**Correct: B — "Chunk the document with slight overlap, extract per chunk, then merge and reconcile the fields."**

You need fields from across the whole contract, so every part must be read. Chunking covers all of it, the overlap prevents fields from being lost at chunk boundaries, and the merge-and-reconcile step resolves the duplicates the overlap creates.

**Wrong answers**

- **A. "Truncate the document to what fits and extract from the first part."** — Guarantees you miss every field in the discarded remainder — and contracts put the important terms at the end as often as the beginning.
- **C. "Summarize the document first, then extract from the summary."** — Extracts from a lossy derivative. Summarisation drops exactly the specific values (dates, amounts, party names) that extraction targets.
- **D. "Raise the temperature so the model fills in the missing parts."** — Asks the model to invent content it never saw. Higher temperature increases variance, not knowledge.

**Takeaway:** Overlapping chunks + reconciliation is the standard long-document extraction pattern. Overlap is insurance against boundary loss; reconciliation is the premium you pay for it.

---

### Q27 — extraction_pipeline

**Correct: B — "Add prompt instructions to return null for any field where information is not directly stated in the source."**

The schema already permits `null`; the model just wasn't told that `null` is the *preferred* answer over a plausible guess. Making "don't know" an explicitly sanctioned output is the direct fix for grounding failures.

**Wrong answers**

- **A. "Add a post-processing step using a second LLM call to verify each extracted value exists in the source document."** — Doubles cost and latency to catch an error you can prevent at extraction time, and the verifier inherits the same hallucination risk it's meant to police.
- **C. "Make all schema fields required (non-nullable) with strict validation rules to ensure the model only outputs verifiable data."** — Exactly backwards. Removing `null` *forces* the model to invent a value for every absent field; you'd take the fabrication rate up, not down.
- **D. "Upgrade to a more capable model tier with improved instruction-following to reduce hallucination tendencies."** — There is no instruction to follow better. Fix the prompt before you pay for a bigger model.

**Takeaway:** Explicitly license "not found." An unstated permission to say nothing reads as a requirement to say something.

---

### Q33 — extraction_pipeline

**Correct: D — "Define a strict output schema and include format normalization rules in your prompt."**

The two levers work together: the schema fixes the output *shape* (which fields, which types), and the prompt's normalization rules fix the *conventions* ("prices as decimal numbers without currency symbols; dietary tags from this list, including icon equivalents"). Normalizing at extraction time means the model resolves the ambiguity while it can still see the source.

**Wrong answers**

- **A. "Use separate extraction calls for each field to ensure consistent handling of each type."** — Multiplies cost by the field count and destroys cross-field context: an item's price and dietary tags are understood together, from the same menu line.
- **B. "Extract data as-is and normalize formats in post-processing code after Claude returns."** — Workable for prices; fails for the icons. Once the icon is flattened to raw text the semantic information is gone, and post-processing can't recover what the model didn't interpret. You end up maintaining a growing normalization codebase per menu variant.
- **C. "Request multiple extraction attempts per document and select the most common format."** — Pays N× for a vote among outputs that are all *validly* formatted in different ways. Consensus on an arbitrary convention isn't the same as conforming to your convention.

**Takeaway:** Schema constrains structure; the prompt constrains convention. Normalize where the source is still visible.

---

### Q35 — extraction_pipeline

**Correct: D — "Add few-shot examples demonstrating extractions from documents with varied structures—showing how to identify citations in different formats and locate methodology details across section types."**

The information is present; the model just doesn't recognise it in unfamiliar arrangements. That's a *recognition* gap, and diverse few-shot examples are the direct remedy — show an inline-citation document and a bibliography document, a methodology section and a methodology buried in the introduction.

**Wrong answers**

- **A. "Implement retry logic that re-sends requests when validation detects empty required fields."** — Retrying an identical prompt on an identical document reproduces the identical failure. Nothing about the second attempt makes the bibliography easier to recognise. (Compare [[#Q49 — extraction_pipeline]]: retries only help when the error feedback itself adds information.)
- **B. "Build a regex-based post-processing layer that scans source documents for citation patterns and methodology keywords, populating empty fields when the model fails to extract."** — Rebuilds the hard part of the extractor in regex, and the varied-format problem that defeats the model defeats the patterns too — plus you now maintain two extraction systems that disagree.
- **C. "Modify your schema to make citations and methodology optional, and flag incomplete records for manual review rather than failing validation."** — Reclassifies the failure as acceptable. The data *is* in the document; routing 5% to humans is a workaround, not a fix.

**Takeaway:** Few-shot examples should be chosen for *variety*, not quantity — they teach recognition across the formats you actually receive.

---

### Q36 — extraction_pipeline

**Correct: B — "Require an ISO date in the output schema, and when the input is ambiguous, flag the field for review instead of guessing."**

`03/04/2025` genuinely is undecidable without knowing the vendor's locale. The schema forces an unambiguous output format, and the flag makes the model's uncertainty *visible* rather than silently resolved — which is the whole point when a wrong date means a wrong payment due date.

**Wrong answers**

- **A. "Assume the United States month-first format everywhere."** — Silently wrong for every non-US vendor, and wrong in a way that produces a perfectly valid-looking date. This is the exact silent error the question asks you to avoid.
- **C. "Store the date as the raw string and sort it out later."** — Defers the decision to a point where you have *less* information, and pushes unnormalized data into downstream systems that expect a date.
- **D. "Drop any date that is ambiguous."** — Destroys real data. Many ambiguous dates are resolvable later from vendor context; discarding them makes recovery impossible.

**Takeaway:** Ambiguity should surface as a flag, never as a guess. Silent plausible errors cost more than loud uncertain ones.

---

### Q46 — extraction_pipeline

**Correct: B — "Return null for that field and mark it as not found, leaving the rest of the extraction intact."**

`null` is the accurate representation of absent information, and marking it as not-found distinguishes "the document didn't say" from "the extractor missed it." The other fields were extracted correctly, so there's no reason to lose them.

**Wrong answers**

- **A. "Fill the field with a plausible value inferred from the rest of the document."** — Fabrication that passes validation. Downstream consumers can't tell an inferred value from an observed one, which is the most dangerous possible failure. (See [[#Q27 — extraction_pipeline]].)
- **C. "Fail the entire extraction because one field is missing."** — Discards correct data over one absent field. Absence is normal in real documents, not an exception.
- **D. "Repeat the previous record value for that field."** — Contaminates this record with an unrelated document's data — silently wrong, and the error propagates through every subsequent record.

**Takeaway:** Null is a valid answer. Partial-with-honest-gaps beats complete-with-invented-values.

---

### Q49 — extraction_pipeline

**Correct: D — "The model extracts 'et al.' for co-authors when the full list exists only in an external document not in the input"**

Retry-with-error-feedback works when the model *has* the information and formatted it wrongly. Here the full author list is not in the input at all, so no amount of error feedback can produce it — retrying just re-derives "et al." and burns tokens. The fix is upstream: supply the missing document.

**Wrong answers** (all three are *format* errors, which retry feedback fixes readily)

- **A. "The model extracts keywords as a nested object organized by category when the schema requires a flat array of strings"** — The keywords are all present; it's a restructuring task. A validation error naming the expected shape is enough to flatten it.
- **B. "The model extracts citation counts as locale-formatted strings ('1,234') when the schema requires integers"** — Pure type coercion of data already extracted correctly. `"1,234"` → `1234` on the next attempt.
- **C. "The model extracts dates as ISO 8601 datetime strings ('2023-03-15T00:00:00Z') when the schema requires only the date portion (YYYY-MM-DD)"** — Truncation of an already-correct value. The error message states the required format precisely.

**Takeaway:** Retries fix *representation*, never *absence*. Ask first whether the information was ever in the input.

---

### Q51 — extraction_pipeline

**Correct: A — "Have the model output field-level confidence scores, then calibrate review thresholds using a labeled validation set."**

Reviewer capacity is the scarce resource, so it should go to the extractions most likely to be wrong. Field-level confidence gives a per-extraction risk signal, and calibrating it against labelled data is what turns a raw score into a defensible 20% cutoff rather than a guess.

**Wrong answers**

- **B. "Randomly sample 20% of extractions for review, using corrections to track accuracy and identify error patterns."** — Random sampling estimates the error *rate* well but catches only ~20% of actual errors, since it's blind to which records are risky. Good for measurement, poor for allocation.
- **C. "Prioritize review of all extractions where required fields are empty or explicitly marked as not found."** — Targets the wrong population. The stated failure is *semantic* errors that pass validation — "30 minutes" sitting in a quantity field. Those records are fully populated, so this filter systematically misses them.
- **D. "Review all extractions from documents with formatting anomalies such as unusual layouts or mixed content types."** — A plausible heuristic, but unvalidated: nothing establishes that layout anomalies correlate with the semantic errors observed. It also can't be tuned to fit exactly 20% capacity.

**Takeaway:** Route scarce human review by calibrated risk. Confidence scores are only useful once you've checked what they predict.

---

### Q56 — extraction_pipeline

**Correct: A — "Analyze accuracy by document type and field to verify high-confidence extractions perform consistently across all segments, not just in aggregate."**

97% is an average, and averages hide segments. If one document type or one field sits at 80% inside that high-confidence bucket, automating the bucket ships those errors straight through. Segment analysis is the check that must happen *before* you rely on the aggregate at all.

**Wrong answers**

- **B. "Compare accuracy at different confidence thresholds (85%, 90%, 95%) to find the optimal cutoff that maximizes automation while minimizing errors."** — Useful tuning, but it optimises the same aggregate metric. A better-chosen threshold on an average that conceals a weak segment still automates that segment's errors.
- **C. "Run a two-week pilot routing 25% of high-confidence extractions directly to downstream systems and monitor error reports."** — Detects the problem by shipping it. A pilot is a reasonable *later* step; as the pre-deployment check it means real bad data reaches production first.
- **D. "Verify that 97% accuracy meets requirements for all downstream systems that consume the extracted data."** — A necessary business question, but it takes the 97% at face value — which is exactly the number whose validity is in doubt.

**Takeaway:** Never automate on an aggregate you haven't decomposed. Segment by document type and field before trusting a headline accuracy figure.

---

### Q57 — extraction_pipeline

**Correct: D — "Add few-shot examples showing 2-3 complete input-output pairs with standardized material description formats"**

Two symptoms, one cause: the model has no reference for what a "good" materials value looks like. Worked examples define the target format *and* demonstrate that the field should be populated whenever material information appears — fixing the inconsistent phrasing and the omissions together.

**Wrong answers**

- **A. "Make the 'materials' field required instead of optional in the schema to force the model to always extract a value"** — Stops omissions by forcing fabrication when materials genuinely aren't listed, and does nothing about "cotton blend" vs. "Cotton/Polyester mix" — both satisfy a required string.
- **B. "Switch to a more capable model tier since inconsistent extraction indicates insufficient model capability"** — Diagnoses an underspecified prompt as a capability ceiling. A stronger model still has to guess your preferred format, because you haven't specified one.
- **C. "Set temperature to 0 to eliminate randomness and ensure deterministic outputs"** — Makes outputs *repeatable*, not *correct*. Temperature 0 will consistently produce whichever format the model favours per input, and different inputs still yield different conventions.

**Takeaway:** "Inconsistent output format" is almost always a missing-specification problem. Show the target with examples before reaching for model tier or temperature.

---

## Related notes

- [New Mock Exam index](../README.md) · [Questions.md](../Questions.md)
- Other domains: [Research Pipeline](research_pipeline.md) · [Code Exploration](code_exploration.md) · [Customer Support](customer_support.md)
- [[Weak Areas Deep Dive]] — log the ones you missed here
