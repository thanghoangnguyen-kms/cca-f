---
tags:
  - CCA-F
  - domain-5
  - human-review
  - provenance
  - reliability
  - structured-output
  - flashcards
  - youtube-course
date: 2026-08-05
status: done
domain: "5 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 20"
---

# 🃏 EP20 Flashcards — When AI Needs a Human

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP20 - When AI Needs a Human]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D5 - Context Management & Reliability]] · [[EP03 - Subagent Context Passing & Session Management]] · [[EP16 - Structured Output & JSON Schema]] · [[EP19 - Subagent Error Propagation & Context Management]] · [[EP19 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 5 — The Aggregate Accuracy Trap

**Q: A document extraction system tested at 97% accuracy and shipped. Months later the finance team reports the quarterly numbers don't add up. The 97% was measured honestly. What went wrong?**
A: Nothing was wrong with the *measurement* — 97% was the **wrong statistic**. An aggregate is volume-weighted, so it describes performance on your *typical* document, not on the rare, high-stakes ones. Handwritten receipts were running at ~60% and barely moved the average.

**Q: Why is a high aggregate accuracy metric structurally worst at reporting on exactly the categories that matter most?**
A: Because volume and stakes are **anti-correlated**. The properties that make a document unusual — handwritten, non-standard, exception-path — make it both rare *and* consequential. A rare category contributes to the average in proportion to its share, so the metric is quietest where the risk is loudest.

**Q: Your extraction errors are all clustered in one document category rather than scattered across all of them. Why does that change how you respond?**
A: Clustered errors are **systematic failure**, not noise — a blind spot the system will hit every single time that document type arrives, and one that compounds in the database. Scattered errors are expected at any accuracy below 100%; a cluster is a design defect.

**Q: A pipeline runs at 96% overall. Legal contracts are 5% of volume at 60% accuracy; you fix them to 95%. How much does the headline number move, and what does that prove about aggregate metrics?**
A: **1.75 percentage points** (96% → 97.75%) — plausibly inside measurement noise, despite eliminating ~88% of the errors in the worst category. Aggregates can neither **detect** the problem nor **credit** the fix, which is why per-category reporting is the only usable instrument.

**Q: A system processes 10,000 documents/day at 96% accuracy; legal contracts are 5% of volume at 60%. What share of the day's total errors come from legal contracts?**
A: **Half.** Legal contracts produce $500 \times 0.40 = 200$ errors; total errors are $10{,}000 \times 0.04 = 400$. 5% of the volume generates 50% of the errors, and the aggregate reports none of that.

---

## Domain 5 — Stratified Sampling

**Q: Name the four steps of stratified sampling, in order.**
A: **1. Categorize** into logical buckets (strata) · **2. Sample per stratum** — a representative sample from each · **3. Review and measure specifically** — accuracy *by category*, not one average · **4. Prioritize action** on the findings immediately.

**Q: Handwritten forms are 5% of your volume. Why do they get their own dedicated sample batch instead of 5% of the sample?**
A: Because 5% of the sample gives too few documents to produce a reliable accuracy estimate for that stratum — and the category was failing undetected **precisely because it was not sampled**. A dedicated batch deliberately oversamples the rare stratum to buy detection power exactly where the aggregate has none.

**Q: What does stratified sampling give you that an aggregate cannot, and why is it affordable?**
A: **One accuracy number per category** instead of one misleading number overall — at a fraction of the cost of reviewing everything. It is the maximum diagnostic signal for the minimum review effort.

**Q: An exam stem describes persistent errors in one document type hidden behind a healthy overall accuracy number. What is the architectural answer?**
A: **Stratified random sampling** — *"measures error rates without reviewing everything."* This is the phrasing the exam uses, and it is the correct answer for identifying failing document types efficiently.

**Q: Why is "errors are statistically expected at 96% accuracy" always a wrong answer when the errors are concentrated in one category?**
A: It confuses **noise with systematic failure**. Statistical expectation explains errors distributed across categories; it cannot explain a cluster inside one document type, which indicates a reproducible blind spot rather than random variance.

---

## Domain 5 — Field-Level Confidence

**Q: Why is document-level confidence the same mistake as aggregate accuracy, one level down?**
A: Both are **aggregates, and aggregates hide minorities**. A document score blends eight fields, so one unreadable field is diluted by seven clean ones — exactly as one failing category is diluted by the bulk of the volume.

**Q: Which two pieces of metadata travel with each extracted value, and what question does each answer for a reviewer?**
A: **`confidence`** — how sure the model is. **`source_region`** — where in the document to look to check it (e.g. *"top right header"*, *"bottom table row 12"*). Together they answer *how sure* and *where do I verify*.

**Q: An invoice has 8 extracted fields and one unreadable handwritten address. Under field-level confidence, what does the human actually review?**
A: **That one field only.** Not the invoice number, not the total — just the flagged `vendor_address`. Collapsing the review unit from a document to a field is what makes the human-in-the-loop queue affordable to staff.

**Q: A confidence score of 0.63 arrives from the model. What has to happen before you can act on it, and what is it without that?**
A: It must be **calibrated against a labeled validation set** — real examples with known ground truth — to establish what accuracy 0.63 actually corresponds to. Uncalibrated, a model's raw self-reported confidence is just a guess.

**Q: Why can't a 0.7 confidence on an invoice number be compared with a 0.7 on a handwritten address?**
A: Because calibration is **per field type**. The same reported number maps to different real accuracy depending on the field, so thresholds must be set per field type from the labeled set — never one global cutoff.

**Q: Does the Claude Messages API return logprobs, token probabilities, or a confidence score for its output?**
A: **No.** The response carries `id`, `type`, `role`, `content`, `model`, `stop_reason`, `stop_details`, `stop_sequence`, `usage`, and `container` — nothing probabilistic. A confidence score is a field **the model writes into your own JSON schema**, which is exactly why it is self-reported and why calibration is mandatory.

**Q: You want your JSON schema to guarantee `confidence` is between 0.0 and 1.0. Can structured outputs enforce that?**
A: **No.** `required` and `additionalProperties: false` are supported, but **`minimum`, `maximum`, and `multipleOf` are not** — the SDKs strip them from the schema they send and validate the response against your original schema client-side. Required field ✅, enforced range ❌.

---

## Domain 5 — Routing and Human Escalation

**Q: Name the two triggers that send an extracted record to the human review queue.**
A: **(1) Low confidence** — a field's calibrated confidence falls below its threshold. **(2) Contradictory fields** — extracted values disagree internally (a total that doesn't match the sum of line items, a future-dated invoice).

**Q: Every field in a record is above its confidence threshold, and the extraction is still wrong. Which trigger catches it, and why can confidence never catch it?**
A: **Contradictory fields.** Confidence is a **per-field** property, so the model can be confidently wrong about each value individually. Consistency is a **cross-field** property no per-field score can express — neither check implies the other.

**Q: Name the two anti-patterns for handling a low-confidence field, and the single defect they share.**
A: **Discard silently** (drop the field) and **force through** (pass it unflagged). Both **destroy the signal that something needed attention** — one loses the value, the other loses the warning. The correct disposition is to **escalate**, which loses neither.

**Q: A subagent silently drops low-confidence fields before handing off. What specifically breaks downstream?**
A: Downstream systems **cannot distinguish a dropped field from a genuinely absent one** — they get silent gaps and work with incomplete information believing it is complete. The next subagent never receives data it had no way to know existed.

**Q: Why is "force through" dangerous even though no data is lost?**
A: Because the low-confidence value arrives looking **exactly as authoritative** as a 0.97 field. There is no audit trail and no review, so hallucinated or misread data propagates through every downstream consumer indistinguishable from verified data.

**Q: D5 §5.2 lists self-reported confidence as an *unreliable* escalation trigger, but §5.5 routes on low field confidence. How do you reconcile them?**
A: They measure different things. **§5.2** = *uncalibrated* confidence as a proxy for **case complexity** in a conversational agent ❌. **§5.5** = *calibrated* field confidence as a measure of **extraction accuracy**, thresholded from a labeled validation set ✅. The tell is the word **"calibrated"** and what the score is being used to predict.

---

## Domain 5 — Provenance and Claim-Source Mappings

**Q: Why is summarization intrinsically a source-destroying operation?**
A: Condensing text removes the qualifying clause that carries the attribution. *"According to the McKinsey 2024 report, adoption rose 47%"* becomes *"adoption rose 47%"* — the number survives, the provenance does not. Agents *"have a dangerous habit: they lose their sources."*

**Q: Beyond losing a citation, what capability does a pipeline lose when claim-source mappings fail to survive?**
A: **Human review.** The claim becomes unverifiable — the reviewer asks *"where did this 47% come from?"* and there is no answer. Losing provenance silently disables the entire escalation workflow, because a human cannot check what cannot be traced.

**Q: Which episode built claim-source mappings, and what does this episode add on top?**
A: **EP03**, in the context of passing context between agents — that episode was about *sending* the mappings correctly. This episode is about **whether they survive** the whole pipeline to the human reviewer and the downstream validation system.

**Q: In a search → analysis → synthesis → report pipeline, which agent is the highest-risk point for provenance loss, and why does its function make it so?**
A: The **synthesis agent** — the only hop whose job is to **merge claims from multiple sources**. Search and analysis handle one document at a time, so preserving provenance is copying metadata forward; merging naturally tempts the agent to collapse several sourced claims into one unified statement, which is where source tags get dropped or crossed.

**Q: Where must source tags be enforced for the synthesis agent, and why does the exam name two mechanisms rather than one?**
A: In its **instance prompt** *and* its **output schema**. The prompt *requests* preservation; the schema makes an output **without** source tags structurally invalid. The prompt alone is a request — only the schema is enforcement.

**Q: What must the search agent pass forward with every claim, and why is it called the first place attribution gets dropped?**
A: **URL, publication date, and domain credibility.** It is first in the chain — if provenance never leaves the search agent, no downstream hop can reconstruct it, and every later agent inherits an unverifiable claim.

---

## Domain 5 — Conflicting Sources and Temporal Data

**Q: Two credible reports give the 2024 market as \$12B and \$18B. Why is averaging to \$15B worse than arbitrarily picking one of them?**
A: Both source values are **traceable to a citation**; \$15B appears in **no source** — it is fabricated data wearing the authority of two. It also destroys the evidence of disagreement, which often reflects a methodology difference or a temporal gap a human could have resolved.

**Q: What is the correct disposition when two credible sources disagree on a value?**
A: **Annotate both findings with their respective sources and pick neither** — surface the conflict to the coordinator, which escalates to a human if it cannot resolve it. Never take the highest, the lowest, or the average.

**Q: Two "conflicting" figures turn out to have been measured a year apart. What field would have revealed that, and what does that tell you about conflict resolution?**
A: The **date fields** (`publication_date` / `collection_date`). Many apparent conflicts are **temporal, not real** — which is why dates are a prerequisite for resolving conflicts, not a separate concern.

**Q: Name the two mandatory date fields in structured outputs, and what each one answers.**
A: **`publication_date`** — when the data was originally published or measured (how fresh the underlying research is). **`collection_date`** — when your agent actually retrieved it (how stale your copy is).

**Q: Why aren't publication date and collection date redundant?**
A: They diverge in **both directions** and answer different questions. A 2024 paper fetched two years ago may since have been revised or retracted; a freshly-collected document may report a 2019 measurement. Each is blind to what the other catches.

**Q: A pipeline emits contradictory records — some say 12 days, some say 3. Prompt tuning and retry logic haven't helped. What is the fix?**
A: **A structural schema change: require `publication_date` and `collection_date` on every record.** The values were never in conflict — they are measurements from different years. The lecture names both attempted fixes as explicitly wrong: *"not prompt engineering or retry logic."*

**Q: Why must date fields be required rather than optional in the schema?**
A: Optional means **absent whenever the model is unsure** — precisely the case where you need them. Making them required is also what lets a downstream engineer filter on time at all and recover the source of truth.

---

## Domain 5 — Putting It Together

**Q: Name the five layers of the scenario-6 structured-extraction architecture.**
A: **1.** Extraction with field-level confidence and `source_region` · **2.** Stratified sampling with accuracy reported by category · **3.** Threshold-based routing to human review · **4.** Mandatory temporal metadata · **5.** Claim-source mappings preserved across every hop.

**Q: What single principle unifies all five layers of the extraction architecture?**
A: **Never let the system be more certain in its output than it was in its evidence.** Each layer refuses that laundering at a different point — field confidence keeps doubt attached to the value, stratified sampling to the category, routing turns it into a decision, provenance and dates keep it checkable after the pipeline is done.

**Q: You have a strict JSON schema requiring `confidence` and `source_region` per field, and a teammate proposes enabling the API's Citations feature for attribution. What happens?**
A: **The request returns a 400.** Citations and structured outputs are **incompatible** — citations interleave citation blocks with text output, which cannot coexist with strict JSON-schema constraints. Build claim-source mappings as fields in your own schema instead.

**Q: When is the API's native Citations feature the right tool, and when is it unavailable to you?**
A: Right for a **cited prose answer** over documents — it returns `cited_text`, `document_index`, `document_title`, and a `char_location` or `page_location`. Unavailable the moment you commit to **structured output** (`output_config.format`), which it cannot be combined with.

**Q: What is the through-line between EP19's error propagation and EP20's human review?**
A: Both make failure **legible** — EP19 makes a subagent's failure legible to the **coordinator** (structured error payloads); EP20 makes the system's uncertainty legible to the **human** (confidence, routing, provenance). Silent suppression in EP19 and discard-silently here are the same defect at different layers: a system that cannot distinguish *"we have no data"* from *"we had bad data and threw it away."*

---

*Back to: [[EP20 - When AI Needs a Human]]*
