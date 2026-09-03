---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - extraction-pipeline
date: 2026-08-28
status: done
---

# Extraction Pipeline — Unified Bank Answer Key

[← Unified Bank index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **20** `extraction_pipeline` questions in the deduplicated bank — `U3` · `U4` · `U13` · `U18` · `U19` · `U21` · `U22` · `U27` · `U33` · `U35` · `U36` · `U46` · `U49` · `U51` · `U56` · `U57` · `U63` · `U64` · `U79` · `U80`. Each entry reproduces its stem, names every sitting the item appeared in, and states how strongly its answer is verified. Numbers are **`U#`, this folder's own** — never carry them to another set.

**Answers:** **U3** B · **U4** B · **U13** C · **U18** C · **U19** D · **U21** B · **U22** B · **U27** B · **U33** D · **U35** D · **U36** B · **U46** B · **U49** D · **U51** A · **U56** A · **U57** D · **U63** B · **U64** A · **U79** A · **U80** A

**Authority:** U3 🥇🏛 · U4 🥈🏛 · U13 🥇 · U18 🥇 · U19 🥇 · U21 🥇🏛 · U22 🥇 · U27 🥇 · U33 🥇 · U35 🥇🏛 · U36 🥈 · U46 🥇🏛 · U49 🥇 · U51 🥇🏛 · U56 🥈🏛 · U57 🥇 · U63 🥇 · U64 🥇🏛 · U79 📘 · U80 📘
_🥇 the site's own `correct_key` · 🥈 confirmed by the site's grader · 📘 doc-verified, no grader · 🤔 reasoned only · 🏛 **also** named in the [[Official Exam Blueprint]] — outranks all four_

---

### U3 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q3` · `T-Q17` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> An extractor pulls line items and an invoice total from a receipt. The strongest integrity check before accepting the output is to:

**Correct: B — "Verify that the line items sum to the extracted total, and on a mismatch retry or flag the record."**

The document carries its own redundancy — the parts must sum to the whole. Cross-checking derived values against each other is a free, deterministic integrity test that catches both OCR damage and extraction mistakes before the record reaches downstream accounting.

**Wrong answers**

- **A. "Trust the total field because it is printed prominently."** — Visual prominence has nothing to do with extraction accuracy. A large, bold total is exactly as easy to misread as a small one.
- **C. "Check only that the total is a number."** — A type check confirms the field is well-formed, not that it's *correct*. `$1,240.00` misread as `$1.240.00` is still a number.
- **D. "Accept the first extraction without checking."** — No validation at all; the failure mode is a silently wrong ledger entry.

**Takeaway:** When the source contains an arithmetic or logical invariant, assert it. Schema validation proves the shape; invariants prove the content.

---

### U4 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q4` · `O-Q60` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> After your daily batch of 10,000 documents completes, 300 documents (3%) failed with "`context_length_exceeded`" errors. The results file identifies each failure by `custom_id`. What's the most cost-effective approach to process these failures?

**Correct: B — "Resubmit only the 300 failed documents after chunking them into smaller pieces, then combine the partial extractions."**

`custom_id` exists so you can correlate each result back to its source request. Only the 300 oversized documents failed, and their failure cause is real — they exceed the context window — so chunking is the actual fix. Reprocessing only the failures is 3% of the cost of a full re-run.

**Wrong answers**

- **A. "Reprocess the entire batch with prompt caching enabled to reduce the cost of retrying requests with identical system prompts."** — Caching trims the system-prompt cost but does nothing about document length; the 300 would fail again, and you'd have paid to re-run 9,700 already-successful documents.
- **C. "Resubmit the entire 10,000 document batch using a model tier with a larger context window."** — Even if a larger window fits them, you're re-billing 9,700 good extractions to fix 300 — and a bigger window only postpones the ceiling for the next oversized document.
- **D. "Increase the `max_tokens` parameter for the 300 failed documents and resubmit them in a new batch."** — `max_tokens` caps the **output** length. `context_length_exceeded` is an **input** problem; raising `max_tokens` makes it worse, since output tokens count against the same window.

**Takeaway:** `custom_id` is the retry key for the Batch API. And know which knob is input vs. output — `max_tokens` is output-only.

**Sources** _(carried over from `O-Q60` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Batch processing — custom_id matching & result types — https://platform.claude.com/docs/en/build-with-claude/batch-processing
- [2] Models overview / context windows — https://platform.claude.com/docs/en/models/overview

---

### U13 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q13` · `O-Q55` · `T-Q6` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your pipeline uses a tool called `extract_metadata` with a JSON schema for paper details. You've also defined `lookup_citations` and `verify_doi` tools for enrichment. During testing, you notice that when users include requests like "extract the metadata and tell me how cited it is," Claude sometimes calls `lookup_citations` first, which fails because it needs the DOI that `extract_metadata` would provide. What's the most effective way to ensure structured metadata extraction happens first?

**Correct: C — "Set `tool_choice` to `{"type": "tool", "name": "extract_metadata"}` and process the enrichment requests in subsequent turns after receiving the extracted metadata."**

There's a real data dependency: `lookup_citations` needs the DOI that `extract_metadata` produces. Forcing the first tool on the **first turn** guarantees the ordering, and leaving later turns unforced lets Claude run the enrichment tools once the DOI is actually in context.

**Wrong answers**

- **A. "Set `tool_choice` to 'any' so Claude must use a tool, combined with system prompt instructions prioritizing `extract_metadata`."** — `"any"` forces *some* tool, not a *specific* one; Claude can still pick `lookup_citations` first. The prompt instruction is advisory, which is exactly the guarantee you don't have today.
- **B. "Set `tool_choice` to 'auto' and reorder the tool definitions so `extract_metadata` appears first in the tools array, since Claude prioritizes earlier-listed tools."** — The stated premise is false: array position is not a documented priority mechanism. This is superstition dressed as configuration.
- **D. "Set `tool_choice` to {\"type\": \"tool\", \"name\": \"`extract_metadata`\"} for every API call in the pipeline, ensuring Claude always extracts metadata before any enrichment can occur."** — Right mechanism, wrong scope. Forcing it on *every* call means the enrichment tools can never fire; the pipeline extracts metadata forever and never answers "how cited is it."

**Takeaway:** `tool_choice: {"type":"tool","name":...}` is a per-request lever. Force the first turn, then release.

**Sources** _(carried over from `O-Q55` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Define tools — tool_choice options (auto/any/tool/none) — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

---

### U18 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q18` · `O-Q58` · `T-Q27` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Documents arrive continuously throughout business hours and need structured data extracted. To reduce costs, you want to use the `Message Batches API` (50% discount, up-to-24-hour processing window). Your SLA specifies that extraction results must be available within 30 hours of document arrival with 99.9% reliability. Which batching strategy is most appropriate?

**Correct: C — "Submit batches every 4 hours containing documents from that window."**

Do the arithmetic against the SLA. A document arriving at the *start* of a window waits the full window before submission, then up to 24 hours for the batch. 4-hour windows: 4 + 24 = **28 hours**, inside the 30-hour SLA with 2 hours of headroom for the 99.9% reliability requirement.

**Wrong answers**

- **A. "Submit batches every 6 hours containing documents from that window"** — 6 + 24 = **30 hours** exactly. Zero margin: any document that arrives at the top of a window and hits the full 24-hour batch window lands precisely on the deadline, so a 99.9% guarantee is impossible.
- **B. "Submit a single batch at end of day containing all documents from that day"** — A document arriving just after the previous cutoff waits up to 24 hours to be *submitted*, then up to 24 hours to process: **~48 hours** worst case, far past the SLA.
- **D. "Use the real-time API for all documents instead of batch processing"** — Meets the SLA trivially but forfeits the 50% discount, which is the entire stated reason for batching.

**Takeaway:** Batch latency = *your* accumulation window + the batch API's up-to-24-hour window. Budget both, and leave margin — an SLA met exactly is an SLA missed at scale.

**Sources** _(carried over from `O-Q58` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Batch processing — 24-hour window, 50% pricing, expiry — https://platform.claude.com/docs/en/build-with-claude/batch-processing

---

### U19 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q19` · `O-Q46` · `T-Q7` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your extraction system processes two document types: standard monthly reports (archived after processing) and urgent exception reports (must trigger business alerts within 30 minutes of receipt). Both use the same JSON schema. You want to minimize API costs while meeting latency requirements. How should you architect the processing pipeline?

**Correct: D — "Route standard reports to the `Batch API` for 50% cost savings, and route urgent exception reports to the real-time `Messages API`."**

Segment traffic by latency requirement. The monthly reports are archived and have no latency need, so they take the 50% discount; the exception reports have a hard 30-minute alert window, so they take the synchronous path. Same schema, two routes.

**Wrong answers**

- **A. "Submit all documents to the real-time Messages API to ensure consistent processing latency across document types."** — Consistent and needlessly expensive. You pay full price for the bulk of the volume that had no latency requirement at all.
- **B. "Submit all documents to the `Batch API` with `custom_ids` for tracking. When results arrive, immediately process urgent documents and trigger delayed alerts for exceptions."** — The phrase "delayed alerts" concedes the failure. The batch window runs up to 24 hours; no amount of promptness *after* results arrive can meet a 30-minute SLA.
- **C. "Queue all documents and submit hourly batches, flagging urgent documents for expedited handling when batch results return."** — Hourly submission doesn't shorten the batch's own up-to-24-hour completion window, and there is no priority lane within a batch. A flagged urgent document can still miss 30 minutes by a wide margin.

**Takeaway:** Batch is a *throughput* discount, not a latency tier. Split the traffic; never try to make batch behave synchronously.

**Sources** _(carried over from `O-Q46` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Batch processing — https://platform.claude.com/docs/en/build-with-claude/batch-processing
- [2] Models / API overview — https://platform.claude.com/docs/en/models/overview

---

### U21 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q21` · `O-Q56` · `T-Q59` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> Your extraction uses tool use with a JSON schema where `property_type` is defined as an enum: ['house', 'apartment', 'condo', 'townhouse']. After deployment, 8% of extractions fail schema validation. Investigation reveals listings mention many uncommon property types—"studio", "loft", "duplex", "mobile home", "tiny house", "converted warehouse"—and new types continue appearing regularly. What's the most effective long-term solution?

**Correct: B — "Add an "other" value to your enum with a separate `property_type_detail` string field for specifics when "other" is selected."**

The escape-hatch pattern. The enum keeps its clean, queryable values for the common cases, `"other"` gives the model a valid choice when nothing fits — so validation stops failing — and `property_type_detail` preserves the actual text so you don't lose information and can mine it for the next enum value.

**Wrong answers**

- **A. "Continuously expand the enum to include newly observed property types and add monitoring for additional edge cases."** — Perpetual chase. The question states new types "continue appearing regularly," so you are permanently one deploy behind, and validation keeps failing in the gap.
- **C. "Change `property_type` from an enum to a free-form string and implement a normalization step in post-processing."** — Throws away the constraint entirely. You trade an 8% validation-failure rate for 100% unconstrained output and an open-ended normalization mapping to maintain — with no signal at all about which values were unexpected.
- **D. "Add few-shot examples to your prompt demonstrating how to map unexpected property types to the closest existing enum value."** — Forces lossy coercion: a "converted warehouse" becomes "house" and the data is now silently wrong. Validation passes; correctness doesn't.

**Takeaway:** Constrained vocabularies need an escape hatch plus a detail field. Never make the model choose between failing validation and lying.

**Sources** _(carried over from `O-Q56` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Define tools — enum in input_schema — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

---

### U22 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q22` · `T-Q12` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A contract is too long to fit in one context window, and you need fields from across the whole document. The dependable approach is to:

**Correct: B — "Chunk the document with slight overlap, extract per chunk, then merge and reconcile the fields."**

You need fields from across the whole contract, so every part must be read. Chunking covers all of it, the overlap prevents fields from being lost at chunk boundaries, and the merge-and-reconcile step resolves the duplicates the overlap creates.

**Wrong answers**

- **A. "Truncate the document to what fits and extract from the first part."** — Guarantees you miss every field in the discarded remainder — and contracts put the important terms at the end as often as the beginning.
- **C. "Summarize the document first, then extract from the summary."** — Extracts from a lossy derivative. Summarisation drops exactly the specific values (dates, amounts, party names) that extraction targets.
- **D. "Raise the temperature so the model fills in the missing parts."** — Asks the model to invent content it never saw. Higher temperature increases variance, not knowledge.

**Takeaway:** Overlapping chunks + reconciliation is the standard long-document extraction pattern. Overlap is insurance against boundary loss; reconciliation is the premium you pay for it.

---

### U27 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q27` · `O-Q52` · `T-Q25` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your system extracts event metadata (date, location, organizer, `attendee_count`) from news articles using a JSON schema with all nullable fields. During evaluation, you observe the model frequently generates plausible but incorrect values for fields not mentioned in the article—for example, outputting "500" for `attendee_count` when the source contains no attendance information. What's the most effective way to reduce these false extractions?

**Correct: B — "Add prompt instructions to return null for any field where information is not directly stated in the source."**

The schema already permits `null`; the model just wasn't told that `null` is the *preferred* answer over a plausible guess. Making "don't know" an explicitly sanctioned output is the direct fix for grounding failures.

**Wrong answers**

- **A. "Add a post-processing step using a second LLM call to verify each extracted value exists in the source document."** — Loses on **cost and timing**, not on soundness: it doubles cost and latency to catch an error you can prevent at extraction time. Resist the tempting second argument that *"the verifier inherits the same hallucination risk"* — the docs recommend precisely this technique (*"have Claude verify each claim by finding a supporting quote after it generates a response. If it can't find a quote, it must retract the claim"*). A is a real, documented method that is simply more expensive than B [1].
- **C. "Make all schema fields required (non-nullable) with strict validation rules to ensure the model only outputs verifiable data."** — Exactly backwards. Removing `null` *forces* the model to invent a value for every absent field; you'd take the fabrication rate up, not down.
- **D. "Upgrade to a more capable model tier with improved instruction-following to reduce hallucination tendencies."** — There is no instruction to follow better. Fix the prompt before you pay for a bigger model.

**Takeaway:** Explicitly license "not found." An unstated permission to say nothing reads as a requirement to say something.

> [!TIP] Both A and B are documented anti-hallucination techniques — the discriminator is cost, not validity
> The docs list *"allow Claude to say 'I don't know'"* as a **basic** strategy and citation-based self-verification as an **advanced** one. So this item is not "right answer vs broken answer"; it is *cheapest sufficient fix first*. The blueprint backs B independently: scenario 3 names *"nullable fields — verify the model returns `null` instead of fabricating."* Where a stem rules out prompt changes, A becomes the answer.
> Source: <https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations> · verified 2026-09-02

**Sources** _(carried over from `O-Q52` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Reduce hallucinations — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

---

### U33 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q33` · `O-Q51` · `T-Q2` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your extraction pipeline processes restaurant menus and must output structured JSON with fields for item names, descriptions, prices, and dietary tags. Some menus use inconsistent formatting—prices as "$12" vs "12.00", dietary info as icons vs text. What's the most reliable approach?

**Correct: D — "Define a strict output schema and include format normalization rules in your prompt."**

The two levers work together: the schema fixes the output *shape* (which fields, which types), and the prompt's normalization rules fix the *conventions* ("prices as decimal numbers without currency symbols; dietary tags from this list, including icon equivalents"). Normalizing at extraction time means the model resolves the ambiguity while it can still see the source.

**Wrong answers**

- **A. "Use separate extraction calls for each field to ensure consistent handling of each type."** — Multiplies cost by the field count and destroys cross-field context: an item's price and dietary tags are understood together, from the same menu line.
- **B. "Extract data as-is and normalize formats in post-processing code after Claude returns."** — Workable for prices; fails for the icons. Once the icon is flattened to raw text the semantic information is gone, and post-processing can't recover what the model didn't interpret. You end up maintaining a growing normalization codebase per menu variant.
- **C. "Request multiple extraction attempts per document and select the most common format."** — Pays N× for a vote among outputs that are all *validly* formatted in different ways. Consensus on an arbitrary convention isn't the same as conforming to your convention.

**Takeaway:** Schema constrains structure; the prompt constrains convention. Normalize where the source is still visible.

**Sources** _(carried over from `O-Q51` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Define tools / output schemas & best practices — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools
- [2] Use examples (multishot) — https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively

---

### U35 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q35` · `O-Q53` · `T-Q14` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> After implementing tool use with strict schema definitions, JSON syntax errors are eliminated, but 5% of extractions still have valid JSON with empty arrays or null values for required fields like citations and methodology. Spot-checking reveals that source documents contain this information, but in varied formats—inline citations vs. bibliographies, methodology sections vs. details embedded in introductions. What's the most effective way to address these failures?

**Correct: D — "Add few-shot examples demonstrating extractions from documents with varied structures—showing how to identify citations in different formats and locate methodology details across section types."**

The information is present; the model just doesn't recognise it in unfamiliar arrangements. That's a *recognition* gap, and diverse few-shot examples are the direct remedy — show an inline-citation document and a bibliography document, a methodology section and a methodology buried in the introduction.

**Wrong answers**

- **A. "Implement retry logic that re-sends requests when validation detects empty required fields."** — Retrying an identical prompt on an identical document reproduces the identical failure. Nothing about the second attempt makes the bibliography easier to recognise. (Compare [[#U49 — extraction_pipeline]]: retries only help when the error feedback itself adds information.)
- **B. "Build a regex-based post-processing layer that scans source documents for citation patterns and methodology keywords, populating empty fields when the model fails to extract."** — Rebuilds the hard part of the extractor in regex, and the varied-format problem that defeats the model defeats the patterns too — plus you now maintain two extraction systems that disagree.
- **C. "Modify your schema to make citations and methodology optional, and flag incomplete records for manual review rather than failing validation."** — Reclassifies the failure as acceptable. The data *is* in the document; routing 5% to humans is a workaround, not a fix.

**Takeaway:** Few-shot examples should be chosen for *variety*, not quantity — they teach recognition across the formats you actually receive.

**Sources** _(carried over from `O-Q53` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Use examples (multishot) — https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively
- [2] Reduce hallucinations (direct-quote grounding) — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

> [!WARNING] U64 and U35 are a matched pair, and you inverted both
> You applied few-shot where verification was needed (U64) and verification/review where few-shot was needed (U35). The diagnostic is one question — **is the information present and correct in the source?**
>
> | Source state | Model's failure | Fix |
> |---|---|---|
> | Info present, varied format | can't *find* it | **few-shot examples** showing the variants (U35, U57) |
> | Info absent | invents it | **instruct null-when-absent** (U46, U27) |
> | Info present but *inconsistent* | can't *verify* it | **emit both values + flag** (U64, U3) |
>
> Never choose "make the field optional and review by hand" when the field is genuinely extractable. That is capitulation dressed as pragmatism.

---

### U36 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q36` — **one sitting only**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting

> An invoice extractor reads dates like 03/04/2025 that could be March 4 or April 3. The design that avoids silent errors is to:

**Correct: B — "Require an ISO date in the output schema, and when the input is ambiguous, flag the field for review instead of guessing."**

`03/04/2025` genuinely is undecidable without knowing the vendor's locale. The schema forces an unambiguous output format, and the flag makes the model's uncertainty *visible* rather than silently resolved — which is the whole point when a wrong date means a wrong payment due date.

**Wrong answers**

- **A. "Assume the United States month-first format everywhere."** — Silently wrong for every non-US vendor, and wrong in a way that produces a perfectly valid-looking date. This is the exact silent error the question asks you to avoid.
- **C. "Store the date as the raw string and sort it out later."** — Defers the decision to a point where you have *less* information, and pushes unnormalized data into downstream systems that expect a date.
- **D. "Drop any date that is ambiguous."** — Destroys real data. Many ambiguous dates are resolvable later from vendor context; discarding them makes recovery impossible.

**Takeaway:** Ambiguity should surface as a flag, never as a guess. Silent plausible errors cost more than loud uncertain ones.

---

### U46 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q46` · `T-Q24` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> A field the schema expects is simply not present in the source document. The extractor should:

**Correct: B — "Return null for that field and mark it as not found, leaving the rest of the extraction intact."**

`null` is the accurate representation of absent information, and marking it as not-found distinguishes "the document didn't say" from "the extractor missed it." The other fields were extracted correctly, so there's no reason to lose them.

**Wrong answers**

- **A. "Fill the field with a plausible value inferred from the rest of the document."** — Fabrication that passes validation. Downstream consumers can't tell an inferred value from an observed one, which is the most dangerous possible failure. (See [[#U27 — extraction_pipeline]].)
- **C. "Fail the entire extraction because one field is missing."** — Discards correct data over one absent field. Absence is normal in real documents, not an exception.
- **D. "Repeat the previous record value for that field."** — Contaminates this record with an unrelated document's data — silently wrong, and the error propagates through every subsequent record.

**Takeaway:** Null is a valid answer. Partial-with-honest-gaps beats complete-with-invented-values.

---

### U49 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q49` · `O-Q50` · `T-Q5` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your extraction system implements automatic retries when validation fails. On each retry, the specific validation error is appended to the prompt. This retry-with-error-feedback approach resolves most failures within 2-3 attempts. For which failure pattern would additional retries be LEAST effective?

**Correct: D — "The model extracts "et al." for co-authors when the full list exists only in an external document not in the input."**

Retry-with-error-feedback works when the model *has* the information and formatted it wrongly. Here the full author list is not in the input at all, so no amount of error feedback can produce it — retrying just re-derives "et al." and burns tokens. The fix is upstream: supply the missing document.

**Wrong answers** (all three are *format* errors, which retry feedback fixes readily)

- **A. "The model extracts keywords as a nested object organized by category when the schema requires a flat array of strings"** — The keywords are all present; it's a restructuring task. A validation error naming the expected shape is enough to flatten it.
- **B. "The model extracts citation counts as locale-formatted strings ('1,234') when the schema requires integers"** — Pure type coercion of data already extracted correctly. `"1,234"` → `1234` on the next attempt.
- **C. "The model extracts dates as ISO 8601 datetime strings ('2023-03-15T00:00:00Z') when the schema requires only the date portion (YYYY-MM-DD)"** — Truncation of an already-correct value. The error message states the required format precisely.

**Takeaway:** Retries fix *representation*, never *absence*. Ask first whether the information was ever in the input.

**Sources** _(carried over from `O-Q50` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Reduce hallucinations — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / output schemas — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

---

### U51 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q51` · `O-Q59` · `T-Q42` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> After deployment, you find that 12% of extractions contain semantic errors that pass JSON schema validation (e.g., a duration like "30 minutes" incorrectly placed in an ingredient quantity field). Human reviewers have capacity to check only 20% of extractions. Which approach most effectively allocates reviewer attention?

**Correct: A — "Have the model output field-level confidence scores, then calibrate review thresholds using a labeled validation set."**

Reviewer capacity is the scarce resource, so it should go to the extractions most likely to be wrong. Field-level confidence gives a per-extraction risk signal, and calibrating it against labelled data is what turns a raw score into a defensible 20% cutoff rather than a guess.

**Wrong answers**

- **B. "Randomly sample 20% of extractions for review, using corrections to track accuracy and identify error patterns."** — Random sampling estimates the error *rate* well but catches only ~20% of actual errors, since it's blind to which records are risky. Good for measurement, poor for allocation.
- **C. "Prioritize review of all extractions where required fields are empty or explicitly marked as not found."** — Targets the wrong population. The stated failure is *semantic* errors that pass validation — "30 minutes" sitting in a quantity field. Those records are fully populated, so this filter systematically misses them.
- **D. "Review all extractions from documents with formatting anomalies such as unusual layouts or mixed content types."** — A plausible heuristic, but unvalidated: nothing establishes that layout anomalies correlate with the semantic errors observed. It also can't be tuned to fit exactly 20% capacity.

**Takeaway:** Route scarce human review by calibrated risk. Confidence scores are only useful once you've checked what they predict.

**Sources** _(carried over from `O-Q59` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Building a multi-agent research system — evaluation / calibration — https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Reduce hallucinations — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

> [!WARNING] The trap: an "empty field" heuristic is cheap, and cheap ≠ targeted
> With finite review capacity, the winning answer is always the one that **ranks by likelihood of error** and **calibrates the cut-off against labeled data**. A filter that keys off a property the errors don't have is worthless no matter how easy it is to implement. Note also what makes A complete rather than half-right: confidence scores *alone* are uncalibrated — the labeled validation set is not decoration.
>
> **Status of this reasoning:** statistically sound, but *not* documented Anthropic doctrine — no official page covers confidence calibration. Official guidance on reducing hallucination covers letting the model say "I don't know", direct quotes, and citation verification. Defend the argument on its merits; don't cite it as documented guidance.

---

### U56 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q56` · `O-Q48` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> Your system has been operating with 100% human review for 3 months. Analysis shows that extractions with model confidence >90% have 97% accuracy overall. To reduce reviewer workload, you plan to automate high-confidence extractions. Before deploying, what validation step is most critical?

**Correct: A — "Analyze accuracy by document type and field to verify high-confidence extractions perform consistently across all segments, not just in aggregate."**

97% is an average, and averages hide segments. If one document type or one field sits at 80% inside that high-confidence bucket, automating the bucket ships those errors straight through. Segment analysis is the check that must happen *before* you rely on the aggregate at all.

**Wrong answers**

- **B. "Compare accuracy at different confidence thresholds (85%, 90%, 95%) to find the optimal cutoff that maximizes automation while minimizing errors."** — Useful tuning, but it optimises the same aggregate metric. A better-chosen threshold on an average that conceals a weak segment still automates that segment's errors.
- **C. "Run a two-week pilot routing 25% of high-confidence extractions directly to downstream systems and monitor error reports."** — Detects the problem by shipping it. A pilot is a reasonable *later* step; as the pre-deployment check it means real bad data reaches production first.
- **D. "Verify that 97% accuracy meets requirements for all downstream systems that consume the extracted data."** — A necessary business question, but it takes the 97% at face value — which is exactly the number whose validity is in doubt.

**Takeaway:** Never automate on an aggregate you haven't decomposed. Segment by document type and field before trusting a headline accuracy figure.

**Sources** _(carried over from `O-Q48` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Building a multi-agent research system — evaluation of agents — https://www.anthropic.com/engineering/multi-agent-research-system

---

### U57 — extraction_pipeline

🅰 full MCQ · **Seen as:** `N-Q57` · `O-Q57` · `T-Q31` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your extraction system parses e-commerce product descriptions to extract specifications like dimensions, weight, and materials into JSON. Despite having a well-defined schema, the model inconsistently extracts the "materials" field—sometimes returning "cotton blend", other times "Cotton/Polyester mix", and occasionally omitting the field when material information is clearly present in the source. What's the most effective way to improve extraction consistency?

**Correct: D — "Add few-shot examples showing 2-3 complete input-output pairs with standardized material description formats."**

Two symptoms, one cause: the model has no reference for what a "good" materials value looks like. Worked examples define the target format *and* demonstrate that the field should be populated whenever material information appears — fixing the inconsistent phrasing and the omissions together.

**Wrong answers**

- **A. "Make the 'materials' field required instead of optional in the schema to force the model to always extract a value"** — Stops omissions by forcing fabrication when materials genuinely aren't listed, and does nothing about "cotton blend" vs. "Cotton/Polyester mix" — both satisfy a required string.
- **B. "Switch to a more capable model tier since inconsistent extraction indicates insufficient model capability"** — Diagnoses an underspecified prompt as a capability ceiling. A stronger model still has to guess your preferred format, because you haven't specified one.
- **C. "Set temperature to 0 to eliminate randomness and ensure deterministic outputs"** — Makes outputs *repeatable*, not *correct*. Temperature 0 will consistently produce whichever format the model favours per input, and different inputs still yield different conventions.

**Takeaway:** "Inconsistent output format" is almost always a missing-specification problem. Show the target with examples before reaching for model tier or temperature.

**Sources** _(carried over from `O-Q57` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Use examples (multishot) — https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively

---

### U63 — extraction_pipeline

✍️ open-response · **Seen as:** `T-Q10` — **one sitting only**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> An extractor must label each support ticket with one of five priority levels. To stop the model from inventing new labels, you should:

**Correct: B — "Constrain the field to the five allowed values in the schema or tool definition, and reject anything else."**

*Takeaway: enforce closed vocabularies in the schema, not in prose. Compare **U21** for when the vocabulary is genuinely open-ended.*

---

### U64 — extraction_pipeline

✍️ open-response · **Seen as:** `O-Q54` · `T-Q13` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page · 🏛 Also named in the [[Official Exam Blueprint]] — the strongest backing an item in this folder can carry

> Your extraction pipeline processes invoices and extracts line items, subtotals, tax amounts, and grand totals. During evaluation, you discover that in 18% of extractions, the sum of extracted line item amounts doesn't match the extracted grand total—sometimes due to OCR errors in the source document, sometimes due to extraction mistakes by the model. Downstream accounting systems reject records with mismatched totals. What's the most effective approach to improve extraction reliability?

**Correct: A — "Add a "`calculated_total`" field where the model sums extracted line items alongside a "`stated_total`" field. Flag records for human review when values differ."**

**Why it's correct:** Capturing both a derived value and the source-stated value turns the discrepancy into a first-class, machine-checkable signal — catching OCR errors and extraction mistakes uniformly, and letting you route only the mismatched ~18% to humans. This follows Anthropic's grounding philosophy (verifiable, source-anchored outputs) [1] and good schema design where the structured output exposes the signals downstream systems need [2]. It never silently rewrites financial data.

**Why the others are wrong:**
- **B:** A reconciliation model that "picks" the likely-correct number can mask OCR errors silently — accounting still gets wrong data with no flag.
- **C:** Few-shot examples of consistent invoices nudge the model, but when the *source itself* is internally inconsistent (OCR), the model must choose a wrong value anyway.
- **D:** Proportionally adjusting line items fabricates financial data for an accounting system — dangerous and unauditable.

**Key takeaway:** For numeric consistency, extract both the computed and the stated value and surface the mismatch as a reviewable signal — never auto-correct financial figures silently.

**Sources**

- [1] Reduce hallucinations — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / output schemas — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

---

### U79 — extraction_pipeline

✍️ open-response · **Seen as:** `O-Q47` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> A `skills: string[]` field shows three problems: compound phrases ("Python and SQL") split inconsistently, implied-but-unstated skills appear, and array lengths vary wildly (5–10 vs 40+). The prompt only says "Extract all skills mentioned." Most effective fix?

**Correct: A — "Add few-shot examples demonstrating compound-phrase handling, explicit-mention criteria, and appropriate granularity."**

**Why it's correct:** All three symptoms are interpretation problems — what counts as "a skill," whether to split, and what to infer. "Examples are one of the most reliable ways to steer Claude's output format... A few well-crafted examples (known as few-shot or multishot prompting) can dramatically improve accuracy and consistency" [1]. The docs specifically advise making examples **diverse** to "cover edge cases" and wrapping them in `<example>` tags [1] — exactly the lever needed to teach split-vs-not-split, mentioned-vs-inferred, and granularity concretely.

**Why the others are wrong:**
- **B:** Hard count caps ("10–20 maximum") are arbitrary; they force the model to drop real skills or pad to hit a range, and they do nothing about compound-phrase splitting or inference.
- **C:** Post-extraction normalization/dedup can canonicalize text but cannot recover from inferred-but-unstated skills (already wrong) or decide the correct split of "Python and SQL" — that decision must be made at extraction time.
- **D:** Adding `confidence` and `source_quote` metadata is useful signal but does not teach the model the parsing rules causing the inconsistency.

**Key takeaway:** When extraction inconsistency is about *interpretation* (what to include, how to split), demonstrate the rule with diverse few-shot examples rather than bolting on caps or post-processing.

**Sources**

- [1] Use examples (multishot prompting) — https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively

---

### U80 — extraction_pipeline

✍️ open-response · **Seen as:** `O-Q49` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> Contracts include amendments (original "30-day payment terms" vs Amendment 1 "45 days"). The model inconsistently extracts one value with no indication of which applies. Most effective fix?

**Correct: A — "Redesign the schema so amended fields capture multiple values, each with source location and effective date."**

**Why it's correct:** The problem is structural: an amended term is genuinely a *versioned* value, and a single-value field forces the model to pick arbitrarily. Modeling it as value + source location + effective date matches the domain and removes the forced choice. This pairs with Anthropic's grounding guidance — for long documents, having the model surface the supporting text/location reduces hallucination and makes each value verifiable [1]. A well-shaped schema is the tool-use contract the model fills [2].

**Why the others are wrong:**
- **B:** "Always take the most recent" discards the original term, which downstream consumers may need (dispute resolution, audit) — a lossy hard-coded rule.
- **C:** A classifier that removes "superseded" sections can strip clauses that are still legally effective, since amendments often modify only subsets of terms.
- **D:** Pattern-matching to flag every amended contract for manual review is expensive and still gives downstream systems no structured answer.

**Key takeaway:** When the source legitimately carries multiple valid values, fix the *schema* to represent versioning (value + location + effective date) instead of forcing the model to choose one.

**Sources**

- [1] Reduce hallucinations (direct-quote grounding) — https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / tool-use best practices — https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

---

**Back to:** [README.md](../README.md) · [Questions.md](../Questions.md)