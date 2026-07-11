# extraction_pipeline — CCAF Study Guide

> Domain scope: the 15 questions tagged `— extraction_pipeline` (Q46–Q60).
> Citations point to official Anthropic / Claude documentation that was retrieved
> live while building this guide. The `claude-api` bundled skill was **not present**
> on this machine (`~/.claude/skills/claude-api/` does not exist), so all citations
> are to docs.claude.com pages actually fetched. Source URLs are listed per question.

---

### Q46 — extraction_pipeline
**Question:** A pipeline processes two document types that share one JSON schema: standard monthly reports (archived after processing, no latency need) and urgent exception reports (must fire business alerts within 30 minutes). How should you architect processing to minimize API cost while meeting the latency requirement?

**Correct answer: D** — Route standard reports to the Batch API (50% savings) and urgent exception reports to the real-time Messages API.

**Why it's correct:** The Batch API is "a powerful, cost-effective way to asynchronously process large volumes of Messages requests... well-suited to tasks that do not require immediate responses," priced at "50% of standard API prices" [1]. Crucially, batch results are available only "when all messages have completed or after 24 hours, whichever comes first" [1] — so batch cannot guarantee a 30-minute alert. The right design matches latency profile to urgency: archive-bound monthly reports go to batch for the discount; the latency-sensitive exception reports go to the synchronous Messages API [1][2].

**Why the others are wrong:**
- **A:** Sending everything to the real-time API is consistent but pays full price for monthly reports that have no latency need — it wastes the 50% batch discount [1].
- **B:** Batch has an up-to-24-hour window; "immediate responses are not required" is the explicit use case [1]. You cannot meet a 30-minute SLA by processing exception reports through batch, no matter how fast you act on the results.
- **C:** Hourly batches still ride the batch SLO (up to 24h to complete), so a flagged urgent document can still miss the 30-minute window [1].

**Key takeaway:** Segment your traffic by latency requirement: batch the cost-sensitive bulk, use the synchronous API only for the truly latency-bound minority.

**Sources:**
- [1] Batch processing — https://docs.claude.com/en/docs/build-with-claude/batch-processing
- [2] Models / API overview — https://docs.claude.com/en/docs/about-claude/models/overview

---

### Q47 — extraction_pipeline
**Question:** A `skills: string[]` field shows three problems: compound phrases ("Python and SQL") split inconsistently, implied-but-unstated skills appear, and array lengths vary wildly (5–10 vs 40+). The prompt only says "Extract all skills mentioned." Most effective fix?

**Correct answer: A** — Add few-shot examples demonstrating compound-phrase handling, explicit-mention criteria, and appropriate granularity.

**Why it's correct:** All three symptoms are interpretation problems — what counts as "a skill," whether to split, and what to infer. "Examples are one of the most reliable ways to steer Claude's output format... A few well-crafted examples (known as few-shot or multishot prompting) can dramatically improve accuracy and consistency" [1]. The docs specifically advise making examples **diverse** to "cover edge cases" and wrapping them in `<example>` tags [1] — exactly the lever needed to teach split-vs-not-split, mentioned-vs-inferred, and granularity concretely.

**Why the others are wrong:**
- **B:** Hard count caps ("10–20 maximum") are arbitrary; they force the model to drop real skills or pad to hit a range, and they do nothing about compound-phrase splitting or inference.
- **C:** Post-extraction normalization/dedup can canonicalize text but cannot recover from inferred-but-unstated skills (already wrong) or decide the correct split of "Python and SQL" — that decision must be made at extraction time.
- **D:** Adding `confidence` and `source_quote` metadata is useful signal but does not teach the model the parsing rules causing the inconsistency.

**Key takeaway:** When extraction inconsistency is about *interpretation* (what to include, how to split), demonstrate the rule with diverse few-shot examples rather than bolting on caps or post-processing.

**Sources:**
- [1] Use examples (multishot prompting) — https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting

---

### Q48 — extraction_pipeline
**Question:** After 3 months of 100% human review, extractions with model confidence >90% show 97% accuracy in aggregate. Before auto-routing high-confidence extractions, what validation step is most critical?

**Correct answer: A** — Analyze accuracy by document type and field to confirm high-confidence performance holds across all segments, not just in aggregate.

**Why it's correct:** Aggregate accuracy can hide systematic failure in a sub-segment — one document type could sit at 70% while others are 99%, yet still average to 97%. Anthropic's guidance on evaluating systems stresses building evals that reflect real usage and inspecting outputs against rubric criteria rather than trusting a single headline number [1]. Auto-routing on the aggregate alone risks pushing a weak segment's errors straight to downstream systems unreviewed.

**Why the others are wrong:**
- **B:** Threshold tuning (85/90/95%) optimizes a number that may be lying if accuracy isn't uniform across segments — do segment analysis first [1].
- **C:** A 25% pilot is good practice but is *downstream* of segment analysis; if a segment is systematically wrong, the pilot discovers it by harming real users.
- **D:** Verifying 97% meets downstream needs is a valid product question, but it assumes the 97% holds everywhere — the very thing segment analysis must establish first.

**Key takeaway:** Before trusting a confidence gate, disaggregate accuracy by segment (document type × field); aggregate metrics conceal localized failure modes.

**Sources:**
- [1] Building a multi-agent research system — evaluation of agents — https://www.anthropic.com/engineering/built-multi-agent-research-system

---

### Q49 — extraction_pipeline
**Question:** Contracts include amendments (original "30-day payment terms" vs Amendment 1 "45 days"). The model inconsistently extracts one value with no indication of which applies. Most effective fix?

**Correct answer: A** — Redesign the schema so amended fields capture multiple values, each with source location and effective date.

**Why it's correct:** The problem is structural: an amended term is genuinely a *versioned* value, and a single-value field forces the model to pick arbitrarily. Modeling it as value + source location + effective date matches the domain and removes the forced choice. This pairs with Anthropic's grounding guidance — for long documents, having the model surface the supporting text/location reduces hallucination and makes each value verifiable [1]. A well-shaped schema is the tool-use contract the model fills [2].

**Why the others are wrong:**
- **B:** "Always take the most recent" discards the original term, which downstream consumers may need (dispute resolution, audit) — a lossy hard-coded rule.
- **C:** A classifier that removes "superseded" sections can strip clauses that are still legally effective, since amendments often modify only subsets of terms.
- **D:** Pattern-matching to flag every amended contract for manual review is expensive and still gives downstream systems no structured answer.

**Key takeaway:** When the source legitimately carries multiple valid values, fix the *schema* to represent versioning (value + location + effective date) instead of forcing the model to choose one.

**Sources:**
- [1] Reduce hallucinations (direct-quote grounding) — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / tool-use best practices — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use

---

### Q50 — extraction_pipeline
**Question:** Retry-with-error-feedback (append the validation error and retry) fixes most failures in 2–3 attempts. For which failure pattern is retrying LEAST effective?

**Correct answer: D** — The model extracts "et al." for co-authors when the full author list exists only in an external document not in the input.

**Why it's correct:** Retry-with-feedback only fixes mistakes the model could have gotten right from the source — formatting and structural shape corrections. When the required information is simply *not present in the input*, no feedback can conjure it; the model can only hallucinate. Anthropic's hallucination guidance is explicit that responses should be grounded in the actual provided text, and the standard mitigation is to *allow* the model to say the information isn't available rather than invent it [1]. The missing author list is an information-availability gap, not a correctable error.

**Why the others are wrong (these *are* fixable by retry):**
- **A:** Nested-object-vs-flat-array is a structural mismatch the model can reshape once told the expected schema [2].
- **B:** A locale-formatted "1,234" vs integer is a pure formatting fix; the value is present in the document.
- **C:** ISO datetime vs `YYYY-MM-DD` is trimming the time portion — the underlying value is correct and present.

**Key takeaway:** Feedback-retry corrects *formatting and shape* errors; it cannot supply information absent from the input. Distinguish "model formatted it wrong" from "the data isn't there."

**Sources:**
- [1] Reduce hallucinations — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / output schemas — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use

---

### Q51 — extraction_pipeline
**Question:** Restaurant-menu extraction to JSON (names, descriptions, prices, dietary tags) faces inconsistent source formatting — prices "$12" vs "12.00", dietary info as icons vs text. Most reliable approach?

**Correct answer: D** — Define a strict output schema and include format-normalization rules in the prompt.

**Why it's correct:** A strict schema gives the model a typed contract to fill, and explicit normalization rules ("prices as decimal with two places," "dietary as enumerated tags") let it both extract and normalize in a single pass [1]. Tool-use/structured output with a well-specified `input_schema` is the documented way to get reliable structured JSON, and detailed instructions about expected values are part of getting best performance [1]. Few-shot examples can reinforce the canonical format on top of this [2].

**Why the others are wrong:**
- **A:** A separate call per field multiplies cost and latency and loses cross-field context (which dietary icon sits next to which item).
- **B:** Extracting raw then normalizing in post-processing code is brittle — deterministic code must enumerate every format permutation; the model normalizes more robustly at extraction time.
- **C:** Ensemble voting over multiple attempts is expensive and can still elect a wrong-format majority.

**Key takeaway:** Let the model normalize during extraction — strict schema + explicit format rules in the prompt — rather than post-processing a raw blob.

**Sources:**
- [1] Define tools / output schemas & best practices — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use
- [2] Use examples (multishot) — https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting

---

### Q52 — extraction_pipeline
**Question:** Event-metadata extraction (date, location, organizer, `attendee_count`) uses a JSON schema with all-nullable fields. The model frequently invents plausible values for fields the article never mentions (e.g., "500" attendees). Most effective fix?

**Correct answer: B** — Add prompt instructions to return null for any field not directly stated in the source.

**Why it's correct:** The fields are already nullable, so the only gap is policy: the model needs to be told to prefer null over a plausible guess. Anthropic's anti-hallucination guidance centers on grounding outputs in the actual source and explicitly allowing the model to decline / not answer when information isn't present [1]. An explicit "return null when not stated" instruction is the standard, lowest-cost fix for schema-aware hallucination.

**Why the others are wrong:**
- **A:** A second LLM verification pass is an expensive correction for a behavior you can prevent at the source.
- **C:** Making fields required is the opposite of what you want — it *forces* the model to invent values when information is missing.
- **D:** Upgrading model tier adds cost and still doesn't communicate your null-over-guess policy.

**Key takeaway:** For nullable schemas, explicitly instruct the model to emit null when the source doesn't state a value — grounding by policy beats post-hoc verification.

**Sources:**
- [1] Reduce hallucinations — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

---

### Q53 — extraction_pipeline
**Question:** Tool use with strict schemas eliminated JSON syntax errors, but 5% of extractions still have empty arrays/nulls for required `citations` and `methodology`. The info exists in the source but in varied forms (inline citations vs bibliographies; methodology sections vs details embedded in intros). Most effective fix?

**Correct answer: D** — Add few-shot examples demonstrating extraction from documents with varied structures (citations in different formats, methodology across section types).

**Why it's correct:** Strict schemas guarantee *shape*, not *recall*. The failure is the model not recognizing varied source formats — precisely what diverse few-shot examples address: "make them... Diverse: Cover edge cases" [1]. Showing the model how methodology can hide in an introduction, or how citations appear inline vs in a bibliography, directly raises recall on the 5%. Grounding via quote extraction on long documents further reinforces this [2].

**Why the others are wrong:**
- **A:** Retrying the same prompt won't teach the model to recognize intro-embedded methodology — the capability gap remains.
- **B:** Regex over prose is brittle, misses nuanced placements, and ships silently wrong data.
- **C:** Making the fields optional and routing to humans papers over the symptom; the fields are genuinely required downstream, trading the problem for reviewer load.

**Key takeaway:** Schema strictness fixes format, not recall. To capture information that appears in varied locations/formats, teach recognition with diverse few-shot examples.

**Sources:**
- [1] Use examples (multishot) — https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting
- [2] Reduce hallucinations (direct-quote grounding) — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

---

### Q54 — extraction_pipeline
**Question:** Invoice extraction: in 18% of cases the sum of line items doesn't match the extracted grand total (sometimes OCR errors, sometimes extraction mistakes). Downstream accounting rejects mismatches. Most effective approach?

**Correct answer: A** — Add a `calculated_total` (model sums line items) alongside `stated_total`; flag records for human review when they differ.

**Why it's correct:** Capturing both a derived value and the source-stated value turns the discrepancy into a first-class, machine-checkable signal — catching OCR errors and extraction mistakes uniformly, and letting you route only the mismatched ~18% to humans. This follows Anthropic's grounding philosophy (verifiable, source-anchored outputs) [1] and good schema design where the structured output exposes the signals downstream systems need [2]. It never silently rewrites financial data.

**Why the others are wrong:**
- **B:** A reconciliation model that "picks" the likely-correct number can mask OCR errors silently — accounting still gets wrong data with no flag.
- **C:** Few-shot examples of consistent invoices nudge the model, but when the *source itself* is internally inconsistent (OCR), the model must choose a wrong value anyway.
- **D:** Proportionally adjusting line items fabricates financial data for an accounting system — dangerous and unauditable.

**Key takeaway:** For numeric consistency, extract both the computed and the stated value and surface the mismatch as a reviewable signal — never auto-correct financial figures silently.

**Sources:**
- [1] Reduce hallucinations — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [2] Define tools / output schemas — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use

---

### Q55 — extraction_pipeline
**Question:** Pipeline has `extract_metadata` (JSON schema) plus enrichment tools `lookup_citations` and `verify_doi`. On "extract the metadata and tell me how cited it is," Claude sometimes calls `lookup_citations` first, which fails because it needs the DOI that `extract_metadata` produces. How to ensure extraction happens first?

**Correct answer: C** — Set `tool_choice` to `{"type":"tool","name":"extract_metadata"}` for the first turn, then process enrichment in subsequent turns once metadata is in context.

**Why it's correct:** `tool_choice: {"type":"tool","name":"..."}` deterministically forces a specific named tool on that turn — the API "prefills the assistant message to force a tool to be used" [1]. Forcing `extract_metadata` first guarantees ordering; handing control back to `auto` on later turns lets Claude run the DOI/citation enrichment with the metadata already available [1].

**Why the others are wrong:**
- **A:** `tool_choice: "any"` forces *a* tool but not the *right* one — Claude could still pick `lookup_citations` first [1].
- **B:** "Auto + reorder tools so `extract_metadata` is first" relies on an undocumented ordering preference; there is no contractual guarantee Claude prioritizes earlier-listed tools [1].
- **D:** Pinning `extract_metadata` on *every* call means the enrichment tools can never be invoked [1].

**Key takeaway:** To guarantee a specific tool runs first, force it with `tool_choice:{"type":"tool","name":...}` on turn one, then return to `auto` so dependent tools can follow.

**Sources:**
- [1] Define tools — tool_choice options (auto/any/tool/none) — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use

---

### Q56 — extraction_pipeline
**Question:** `property_type` is an enum `['house','apartment','condo','townhouse']`. 8% of extractions fail validation because listings mention "studio", "loft", "duplex", "mobile home", etc., and new types keep appearing. Most effective long-term solution?

**Correct answer: B** — Add an `"other"` enum value plus a separate `property_type_detail` string field for specifics when "other" is selected.

**Why it's correct:** This keeps the strong enum for common cases (clean downstream joins / validation) while giving a well-typed escape hatch that preserves the uncommon detail — a stable design as new types emerge. It avoids both the validation failures and lossy coercion. Enums in the `input_schema` are the documented mechanism for constrained values [1], and the `"other" + detail` pattern is the standard way to keep an enum closed without losing open-world information.

**Why the others are wrong:**
- **A:** Continuously expanding the enum is whack-a-mole — every new listing style risks another validation failure.
- **C:** Switching to a free-form string discards the validation guarantee entirely and pushes normalization downstream with no canonical vocabulary.
- **D:** Few-shot mapping "tiny house" → "house" silently drops real distinctions; it's lossy by design.

**Key takeaway:** When a closed enum meets an open-ended real world, add an `"other"` value plus a detail field — you keep validation and joins while preserving the long tail.

**Sources:**
- [1] Define tools — enum in input_schema — https://docs.claude.com/en/docs/build-with-claude/tool-use/implement-tool-use

---

### Q57 — extraction_pipeline
**Question:** E-commerce spec extraction: the `materials` field comes back inconsistently ("cotton blend" vs "Cotton/Polyester mix") and is sometimes omitted though clearly present. Most effective fix?

**Correct answer: D** — Add few-shot examples showing 2–3 complete input→output pairs with a standardized material-description format.

**Why it's correct:** The inconsistency is a *format/recall* problem: the model doesn't know the canonical output you want, and sometimes skips present info. Few-shot examples are "one of the most reliable ways to steer Claude's output format, tone, and structure" and "dramatically improve accuracy and consistency" [1]. Demonstrating the normalized form (e.g., "cotton/polyester" as an ordered list) both fixes the format and raises recall on skipped materials.

**Why the others are wrong:**
- **A:** Making the field required doesn't define a format and can force invented values when material info is genuinely absent.
- **B:** A more capable tier is costly and doesn't communicate the canonical format you want.
- **C:** Temperature 0 only makes the *wrong* format consistent — determinism isn't correctness of format.

**Key takeaway:** To standardize a free-text field's format and lift recall, show the model the exact canonical shape with a few input→output examples.

**Sources:**
- [1] Use examples (multishot) — https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting

---

### Q58 — extraction_pipeline
**Question:** Documents arrive continuously during business hours; you want the Batch API's 50% discount. SLA: results within 30 hours of arrival at 99.9% reliability. Which batching strategy?

**Correct answer: C** — Submit batches every 4 hours containing that window's documents.

**Why it's correct:** Worst-case end-to-end latency = max queue wait before a doc is batched + batch processing window. The docs state batches complete "when all messages have completed or after 24 hours, whichever comes first" and "expire if processing does not complete within 24 hours" [1]. So worst case = 4h wait + up to 24h processing = **28h**, leaving a 2-hour cushion under the 30-hour SLA to absorb batch variance and hit 99.9% [1].

**Why the others are wrong:**
- **A:** Every 6 hours → 6h wait + up to 24h = 30h exactly, with zero safety margin for variance — can't reliably hit 99.9% [1].
- **B:** A single end-of-day batch can make a morning document wait the whole day plus up to 24h — well over 30h [1].
- **D:** The real-time API meets the SLA trivially but throws away the 50% discount the question explicitly wants [1].

**Key takeaway:** Batch SLA math = (max queue wait before submission) + (24h batch ceiling). Size your batching interval so the sum sits comfortably under the SLA with margin, not exactly on it.

**Sources:**
- [1] Batch processing — 24-hour window, 50% pricing, expiry — https://docs.claude.com/en/docs/build-with-claude/batch-processing

---

### Q59 — extraction_pipeline
**Question:** 12% of extractions contain semantic errors that still pass JSON schema validation (e.g., "30 minutes" placed in an ingredient-quantity field). Reviewers can check only 20%. Which approach best allocates reviewer attention?

**Correct answer: A** — Have the model output field-level confidence scores, then calibrate review thresholds on a labeled validation set.

**Why it's correct:** Field-level confidence lets you route the lowest-confidence ~20% to humans — where the 12% semantic errors concentrate — capturing far more errors than a flat sample. Calibrating the threshold on labeled data makes the cutoff evidence-driven rather than guessed, mirroring Anthropic's emphasis on grounded, validation-set-driven evaluation [1]. Schema validation alone can't catch type-valid-but-semantically-wrong values, so a confidence signal is the right complement [2].

**Why the others are wrong:**
- **B:** Random 20% sampling catches only ~20% of the 12% (~2.4% of records) — good for measuring accuracy, poor for *coverage*.
- **C:** Prioritizing empty/“not found” fields targets a narrow error mode; the 12% problem is *wrong values in populated fields*, which this misses.
- **D:** Formatting-anomaly heuristics don't reliably predict where fields got confused with each other.

**Key takeaway:** With limited review capacity, use calibrated field-level confidence to concentrate human attention where errors cluster — random sampling measures but doesn't cover.

**Sources:**
- [1] Building a multi-agent research system — evaluation / calibration — https://www.anthropic.com/engineering/built-multi-agent-research-system
- [2] Reduce hallucinations — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

---

### Q60 — extraction_pipeline
**Question:** A daily batch of 10,000 docs completes; 300 (3%) failed with `context_length_exceeded`. The results file identifies failures by `custom_id`. Most cost-effective way to process the failures?

**Correct answer: B** — Resubmit only the 300 failed documents after chunking them into smaller pieces, then combine the partial extractions.

**Why it's correct:** `custom_id` is the documented mechanism to match each result back to its request — "always use the `custom_id` field" since "batch results may not match input order" [1] — so you can target exactly the 300 failures. The error is that input exceeds the context window, so chunking the oversized docs and merging per-chunk extractions addresses the actual cause with minimum tokens [1][2].

**Why the others are wrong:**
- **A:** Reprocessing all 10,000 to fix 300 wastes tokens regardless of caching, and prompt caching doesn't shrink an over-length input [1].
- **C:** Resubmitting all 10,000 on a larger-context tier reprocesses 9,700 successes (expensive) and may still fail on truly outsized documents.
- **D:** `max_tokens` controls *output* length; `context_length_exceeded` is about the combined *input*-plus-output budget, so raising `max_tokens` doesn't help.

**Key takeaway:** Use `custom_id` to retry only the failed records, and fix `context_length_exceeded` by chunking the input — not by enlarging output limits or reprocessing the whole batch.

**Sources:**
- [1] Batch processing — custom_id matching & result types — https://docs.claude.com/en/docs/build-with-claude/batch-processing
- [2] Models overview / context windows — https://docs.claude.com/en/docs/about-claude/models/overview

---

## Cross-cutting principles for the extraction_pipeline domain

1. **Match latency to cost** — Batch API = 50% discount but up-to-24h window; reserve the synchronous Messages API for latency-bound work (Q46, Q58). Do the SLA arithmetic: queue-wait + 24h ceiling, with margin.
2. **Schema design models the domain** — versioned values (Q49), `"other" + detail` escape hatches (Q56), and dual computed/stated fields (Q54) belong in the schema, not in prompts or post-processing.
3. **Few-shot examples fix interpretation, format, and recall** — Q47, Q53, Q57 all resolve through diverse, edge-case-covering examples.
4. **Ground against the source; prefer null to a guess** — Q50, Q52; retries can't supply absent information.
5. **Force ordering and constrain output with the API surface** — `tool_choice:{"type":"tool",...}` (Q55), strict schemas/enums (Q51, Q56), `custom_id` (Q60).
6. **Allocate scarce review with calibrated confidence and segment-level evals** — Q48, Q59; aggregate metrics and random sampling hide and under-cover real errors.

> Note on key validation: all 15 answer keys are defensible against current Anthropic guidance. No ⚠️ corrections needed.
