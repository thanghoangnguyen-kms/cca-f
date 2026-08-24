---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - research-pipeline
date: 2026-08-23
status: done
---

# Research Pipeline — New Mock Exam Answer Key

[← New Mock Exam index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **15** questions tagged `research_pipeline` in [Questions.md](../Questions.md). Numbers are the **sitting's original numbering**, so they interleave with the other three domains and are not contiguous — `Q5` here is `Question 5` there. All `[[#Q…]]` cross-references in this file point to other entries **within this file**.

**Answers:** **Q5** C · **Q17** B · **Q24** B · **Q25** C · **Q28** B · **Q29** D · **Q32** C · **Q34** B · **Q43** A · **Q47** B · **Q48** C · **Q52** C · **Q53** B · **Q55** C · **Q60** C

**Verification:** Q5 ✅ · Q17 ✅ · Q24 ✅ · Q25 ✅ · Q28 ✅ · Q29 ✅ · Q32 ✅ · Q34 🤔 · Q43 ✅ · Q47 ✅ · Q48 ✅ · Q52 ✅ · Q53 ✅ · Q55 📘 · Q60 📘
_✅ confirmed by the practice site's grader · 📘 matches the doc-verified key in `CyberSkill CCAF - Mock Exam/` · 🤔 reasoned judgment, not yet confirmed_

---

### Q5 — research_pipeline

**Correct: C — "Merge the reports, collapse duplicate findings, and keep one cited instance of each."**

De-duplication is the coordinator's job precisely because it is the only component that sees all three reports. Collapsing to one *cited* instance keeps the audit trail intact while removing the repetition that would otherwise read as three independent confirmations.

**Wrong answers**

- **A. "Concatenate all three reports verbatim into the final answer."** — Ships the redundancy to the reader and inflates apparent corroboration: the same fact found three times by three overlapping searches is still one fact.
- **B. "Keep the first report and discard the other two unread."** — Discards the non-overlapping findings, which is the entire reason you dispatched three sub-agents.
- **D. "Ask the user to remove the duplicates."** — Pushes the orchestrator's core synthesis responsibility onto the user.

**Takeaway:** Overlapping sub-agent coverage is expected and healthy; reconciling it is the coordinator's work, not the reader's.

---

### Q17 — research_pipeline

**Correct: B — "Dispatch eight sub-agents in parallel, each returning a short structured summary with citations, then synthesize from the summaries."**

The sources are independent, so fan-out is free latency-wise, and each sub-agent absorbs the raw page in its *own* context. The coordinator only ever sees eight compact cited summaries — fast and context-light at the same time.

**Wrong answers**

- **A. "Read all eight sources into the coordinator context, then write the synthesis in a single pass."** — Floods the coordinator with eight full pages of raw content, which is precisely the failure mode the question asks you to avoid.
- **C. "Process the sources one at a time in a single agent, appending each full page to the running prompt."** — Worst of both: serial latency *and* unbounded context growth, with earlier sources degrading as later ones pile on.
- **D. "Pick the two sources that look most promising and ignore the rest to save tokens."** — Buys efficiency by discarding six of eight sources. That's not an optimisation, it's an incomplete answer.

**Takeaway:** Parallel sub-agents + compact cited returns is the canonical orchestrator–worker research pattern. Isolation buys context; independence buys speed.

---

### Q24 — research_pipeline

**Correct: B — "The claim plus a reference to its source (URL or document id and location)."**

Auditability means a reader can trace any claim back to where it came from. Pairing each claim with a source pointer does that at negligible token cost, and it survives every downstream summarization step because the pointer travels *with* the claim.

**Wrong answers**

- **A. "Only the claim text, to keep messages short."** — Saves a handful of tokens and destroys auditability. Once the claim is detached from its source, the attribution cannot be reconstructed — see [[#Q28 — research_pipeline]] for exactly this failure in production.
- **C. "The full raw page the claim came from, inline in every message."** — Auditable but ruinous for context: the coordinator drowns in raw pages, which is the problem sub-agents exist to prevent. A pointer gives the same traceability for a fraction of the cost.
- **D. "A confidence score and nothing else."** — A number with no provenance. You can't verify a claim you can't locate, and an uncited confidence score is unfalsifiable.

**Takeaway:** Claim + source pointer is the atomic unit of an auditable research pipeline. Not the raw page, not the bare claim.

---

### Q25 — research_pipeline

**Correct: C — "Update the synthesis agent to render each content type appropriately—financial data as tables, news as prose."**

Different content has different natural form. Financial comparisons need tabular alignment; news needs narrative. Fix the *rendering* stage — the one place that knows it's producing an executive briefing — rather than flattening everything upstream.

**Wrong answers**

- **A. "Standardize all subagent outputs to prose summaries with inline citations."** — Destroys the structure in the financial and patent data at the source. Revenue, margins, and growth rates in prose are strictly harder to compare than in a table.
- **B. "Add a format conversion layer between subagents and synthesis that transforms all outputs to a common intermediate representation."** — Adds a component whose job is to erase the type distinctions the briefing needs. It relocates the flattening rather than removing it.
- **D. "Standardize all subagent outputs to JSON with fields for claim, evidence, source, and confidence."** — A good schema for *auditability* (see [[#Q24 — research_pipeline]]) but the wrong fix here: forcing news summaries into claim/evidence tuples shreds the narrative flow the briefing wants, and the synthesis agent still has to decide how to render.

**Takeaway:** Standardize *metadata* (sources, dates, confidence); preserve *content shape*. Presentation decisions belong at the presentation layer.

---

### Q28 — research_pipeline

**Correct: B — "Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings."**

Attribution is lost at the merge step, so bind claims to sources in a structure that *survives* merging. When each finding is a claim-source pair, combining findings is a set operation that carries provenance along by construction — nothing has to be reconstructed later.

**Wrong answers**

- **A. "Maintain complete transcripts of all subagent interactions and add a citation-resolution agent to analyze logs and determine attributions before report generation."** — Expensive forensic reconstruction of information you had for free upstream. Storing full transcripts is costly, and re-deriving attribution from logs is guesswork.
- **C. "Add a verification step where the report generator uses semantic similarity matching against original sources to reconstruct which claims came from which documents."** — Similarity matching *guesses* attribution. Two sources making the same point are indistinguishable, and a paraphrased claim may match the wrong document — producing confident but wrong citations, the worst outcome for an auditable report.
- **D. "Have the coordinator inject source identifier prefixes into text before each handoff, then parse these prefixes at report generation to reconstruct citations."** — String-tagging inside free text is fragile: the synthesis agent rewrites, merges, and paraphrases, and the prefixes get dropped or misattached in the process. That's the same loss you started with, now with a parser.

**Takeaway:** Provenance must be *structural*, not textual. If attribution can be lost by rewording, it will be.

---

### Q29 — research_pipeline

**Correct: D — "Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy."**

All three symptoms trace to over-specification: the sub-agent executes your queries rather than pursuing your objective, so it has no mandate to adapt when the queries miss. Delegate the *goal* plus measurable quality criteria and the sub-agent can reformulate, handle emerging topics, and follow promising tangents — because those now serve the brief instead of violating it.

**Wrong answers**

- **A. "Remove procedural details entirely, delegating with simple goals like 'research X thoroughly' and relying on the subagent's general capabilities."** — Over-corrects. "Thoroughly" is unmeasurable, so you lose the ability to steer coverage, source diversity, or recency at all. The fix is replacing procedure with *criteria*, not with vagueness.
- **B. "Add explicit fallback directives to the detailed instructions: 'If specified searches yield fewer than N results, attempt alternative query formulations before reporting failure.'"** — Patches symptom (1) only, by adding a second layer of procedure. Emerging topics and tangential sources are untouched, and you'll be adding a new directive for each new failure mode forever.
- **C. "Implement a topic classification step where the coordinator categorizes requests as 'well-defined' or 'exploratory' and uses different instruction styles for each category."** — Adds a classifier and keeps the brittle procedural style for everything it labels "well-defined" — including topics that only *look* well-defined until the searches fail.

**Takeaway:** Delegate goals and quality bars, not step-by-step procedure. Procedural delegation caps a sub-agent at your foresight.

---

### Q32 — research_pipeline

**Correct: C — "Have the coordinator analyze each query and dynamically decide which subagents to invoke based on its assessment of query requirements."**

The query distribution is "diverse and evolving," which rules out anything that has to be enumerated or trained in advance. Routing is a judgment call, and the coordinator is already reading every query — let it decide how much pipeline each one needs.

**Wrong answers**

- **A. "Implement pattern-based routing that categorizes queries by structure (single-fact vs. comparative vs. analytical) and maps each category to a predefined subagent combination."** — Static categories against an evolving distribution. Every genuinely new application lands in the wrong bucket or none, and you maintain the mapping forever.
- **B. "Create a fast-path for factual questions that bypasses subagents entirely, routing all other queries through the complete pipeline to ensure research thoroughness."** — A two-speed system for a spectrum of complexity. Everything that isn't a bare fact still pays the full 40-second pipeline, including the many mid-complexity queries that need two sub-agents rather than four.
- **D. "Train a query complexity classifier on labeled historical data to predict optimal subagent combinations, retraining periodically as query patterns evolve."** — Heavy ML machinery — labels, training, retraining cadence — to approximate a judgment the coordinator model already makes natively, and it lags the distribution by one retraining cycle by construction.

**Takeaway:** When routing depends on judgment over an open-ended input space, let the model route. Save static rules for closed, stable sets.

---

### Q34 — research_pipeline

**Correct: B — "Give the task an explicit budget and a coverage check, and stop once the questions are answered or the budget is spent."**

Two independent stopping conditions: a *semantic* one (the research questions are answered) that ends the run when the work is genuinely done, and a *resource* one (the budget) that guarantees termination even when it isn't. That combination is what makes non-convergence impossible rather than merely unlikely.

**Wrong answers**

- **A. "Let it continue until it naturally stops."** — This is the current behaviour, and the premise says it isn't converging.
- **C. "Cut the run off at a random time."** — Bounds the cost but at an arbitrary point, so you get a truncated answer with no signal about what's missing. A budget with a coverage check tells you *why* it stopped.
- **D. "Add more sub-agents so it finishes sooner."** — More agents spawn more follow-up searches. You scale the divergence rather than bounding it.

**Takeaway:** Every agentic loop needs a termination condition it cannot argue with. Pair "am I done?" with "have I spent enough?"

---

### Q43 — research_pipeline

**Correct: A — "Require subagents to include publication or data collection dates in their structured outputs."**

The 35% and 18% figures aren't contradictory — they're a time series, and the synthesis agent can only see that if the dates travel with the numbers as structured metadata. Add the date field and the "conflict" resolves itself into growth.

**Wrong answers**

- **B. "Add a conflict resolution agent that automatically discards older data when newer data exists for the same metric."** — Throws away the 2022 baseline, which is the data point that makes the trend meaningful. It also treats a temporal series as a conflict to be settled rather than a story to be told.
- **C. "Configure the web search agent to only return results from the past 6 months."** — Cripples the pipeline for every legitimately historical research question, and doesn't even fix this case: the internal 2022 report still arrives from the document analysis agent.
- **D. "Instruct the synthesis agent to always treat the most recent data as authoritative and place older findings in a separate historical appendix."** — Structurally separates the two figures, which prevents the synthesis agent from ever connecting them into a growth narrative. It also enshrines "newest wins," which is wrong when the older source is more rigorous.

**Takeaway:** Temporal metadata converts apparent contradictions into trends. Dates are part of a finding, not decoration on it.

---

### Q47 — research_pipeline

**Correct: B — "The coordinator did not include the outputs from the previous agents in the synthesis agent's prompt."**

Sub-agents don't share context. Each invocation sees only what the coordinator puts in its prompt, so if the findings aren't in there, the synthesis agent genuinely has nothing — and reports exactly that.

**Wrong answers**

- **A. "The synthesis agent's context window is not large enough to hold the combined outputs from both previous agents."** — An overflow produces a truncation or size error, not "no research findings were provided." That message means zero input, not too much.
- **C. "The subagents need to share a single API connection to enable automatic context sharing between invocations."** — No such mechanism exists. Context is passed explicitly in prompts; connections don't share state.
- **D. "The synthesis agent needs tools that can fetch results directly from the other agents' conversation histories."** — Describes an architecture that isn't how the orchestrator–worker pattern works. Workers report to the coordinator; they don't read each other's transcripts.

**Takeaway:** The coordinator is the sole communication hub. If a sub-agent lacks information, ask what the coordinator put in its prompt. Same fact as [[#Q60 — research_pipeline]].

---

### Q48 — research_pipeline

**Correct: C — "Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established findings from contested ones, preserving original source characterizations and methodological context."**

The two failure modes — false confidence and mush — are both consequences of *flattening* uncertainty. Preserving each source's own characterization ("analyst estimate, methodology varies" vs. "peer-reviewed, ±$7B, 95% CI") and separating settled from contested lets the reader weigh the evidence, which is what an honest research report does.

**Wrong answers**

- **A. "Configure subagents to only report findings meeting a high-confidence threshold, filtering uncertain information before it reaches the coordinator."** — Solves inconsistency by deleting information. Genuinely contested findings — often the most decision-relevant ones — never reach the report at all.
- **B. "Implement a confidence calibration layer that normalizes subagent uncertainty expressions to standardized probability scores (0.0-1.0), then weight-average findings by their calibrated confidence."** — Fabricates false precision: "methodology varies" doesn't have a defensible numeric score. Weight-averaging $50B and $35B produces a number no source supports and destroys the methodological distinction that actually matters.
- **D. "Add a verification subagent that cross-references findings across sources, only passing claims to synthesis that are corroborated by at least two independent sources."** — Discards single-source findings, including a rigorous peer-reviewed study with no second source. Corroboration count is a poor proxy for evidential quality.

**Takeaway:** Preserve uncertainty; don't collapse or average it. Structure the report so the reader sees which claims are settled and which are contested.

---

### Q52 — research_pipeline

**Correct: C — "Run a focused check that re-fetches the metric from the primary source and resolves the conflict before synthesizing."**

Two moderate-confidence figures for one metric means at least one is wrong, and the answer is knowable — go back to the primary source. A single targeted fetch is cheap relative to publishing a wrong number.

**Wrong answers**

- **A. "Average the two numbers and move on."** — Produces a figure neither source supports, and if one value is simply an error the average is guaranteed wrong.
- **B. "Take whichever sub-agent answered first."** — Response order carries no information about accuracy.
- **D. "Include both numbers in the final answer and let the reader decide."** — Defensible when a conflict is genuinely unresolvable ([[#Q48 — research_pipeline]]), but here it isn't — the primary source can settle it. Passing a resolvable conflict to the reader is abdication.

**Takeaway:** Resolve conflicts against the primary source when you can; surface them with full context only when you can't.

---

### Q53 — research_pipeline

**Correct: B — "Have the coordinator handle straightforward summarization requests directly using its existing context, reserving subagent spawning for complex analysis."**

The coordinator already holds the findings. Spawning a sub-agent means re-transferring 80K+ tokens it doesn't need to move, so the 40 seconds is pure overhead. Summarising in place removes the transfer entirely — and sub-agents stay available for work that genuinely needs isolation.

**Wrong answers**

- **A. "Pre-generate and cache summaries at multiple granularities whenever new findings accumulate."** — Speculative work on every finding update, most of it never read, and the cache goes stale the moment new findings land. Complexity for a problem that disappears if you just don't spawn.
- **C. "Enable prompt caching on the synthesis subagent to reduce the overhead of repeatedly transferring the same research findings."** — Reduces cost and time-to-first-token but keeps the spawn and the transfer. And "accumulated findings" change between requests, so the cache prefix keeps invalidating.
- **D. "Spawn the synthesis subagent with reduced context and have it request specific findings from the coordinator on-demand."** — Trades one big transfer for several round-trips, adding latency in a question about latency, and builds a request protocol to fetch data the coordinator could have used directly.

**Takeaway:** Delegate for *context isolation*, not out of habit. If the coordinator already has the data and the task is simple, doing it inline is the optimisation.

---

### Q55 — research_pipeline

**Correct: C — "Have the coordinator spawn parallel document analysis subagents, each handling a subset of precedents, then aggregate results before synthesis."**

The 12 precedents are independent, so parallel fan-out attacks the latency directly. Crucially, the coordinator stays the single hub — it spawned every sub-agent and receives every result, which is what preserves the monitoring and debugging the question explicitly requires.

**Wrong answers**

- **A. "Implement a message queue where precedent analysis tasks are processed asynchronously by a pool of worker agents."** — Achieves parallelism but interposes infrastructure between the coordinator and the workers. Tracing which task produced which finding now means correlating across queue logs — the observability cost the question warns about.
- **B. "Create a recursive agent hierarchy where analysis agents subdivide work among child agents until reaching single-precedent granularity."** — Deep nesting for a flat, already-known work list of 12 items. Each level adds hand-off overhead and distance from the coordinator, and the recursion buys nothing over a single fan-out.
- **D. "Enable the document analysis subagent to spawn its own specialized subagents dynamically when it encounters cases with many citations."** — Moves orchestration below the coordinator, which then can't see or debug the sub-sub-agents it never spawned. Keep fan-out at the level that owns observability.

**Takeaway:** Parallelize from the coordinator. Flat fan-out preserves the audit trail; nesting and queues erode it.

---

### Q60 — research_pipeline

**Correct: C — "The coordinator agent receives the web search agent's output and includes relevant findings in the prompt when invoking the document analysis agent."**

Hub-and-spoke. Sub-agents are context-isolated and never talk to each other; the coordinator collects each worker's output and decides what to include in the next worker's prompt. That single hub is what makes the system auditable and debuggable.

**Wrong answers**

- **A. "The agents communicate through an event-driven message queue, with the document analysis agent subscribing to web search completion events."** — Describes a distributed microservice architecture, not the orchestrator–worker pattern. No pub/sub channel exists between sub-agents.
- **B. "The web search agent directly invokes the document analysis agent, passing the discovered sources as parameters."** — Peer-to-peer invocation bypasses the coordinator, which then can't see or control the hand-off. (Same anti-pattern as option D in [[#Q55 — research_pipeline]].)
- **D. "Both agents access a shared memory store where the web search agent writes findings and the document analysis agent reads them."** — Shared mutable state between sub-agents isn't the model. Context isolation is a feature, and implicit sharing would undermine both the isolation and the audit trail.

**Takeaway:** All inter-agent information flows *through* the coordinator's prompts. If you catch yourself drawing an arrow between two workers, it's the wrong diagram.

---

## Related notes

- [New Mock Exam index](../README.md) · [Questions.md](../Questions.md)
- Other domains: [Code Exploration](code_exploration.md) · [Customer Support](customer_support.md) · [Extraction Pipeline](extraction_pipeline.md)
- [[Weak Areas Deep Dive]] — log the ones you missed here
