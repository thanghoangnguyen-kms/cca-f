---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - research-pipeline
date: 2026-08-28
status: done
---

# Research Pipeline — Unified Bank Answer Key

[← Unified Bank index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **20** `research_pipeline` questions in the deduplicated bank — `U5` · `U17` · `U24` · `U25` · `U28` · `U29` · `U32` · `U34` · `U43` · `U47` · `U48` · `U52` · `U53` · `U55` · `U60` · `U62` · `U69` · `U70` · `U71` · `U73`. Each entry reproduces its stem, names every sitting the item appeared in, and states how strongly its answer is verified. Numbers are **`U#`, this folder's own** — never carry them to another set.

**Answers:** **U5** C · **U17** B · **U24** B · **U25** C · **U28** B · **U29** D · **U32** C · **U34** B · **U43** A · **U47** B · **U48** C · **U52** C · **U53** B · **U55** C · **U60** C · **U62** C · **U69** A · **U70** A · **U71** C · **U73** B

**Authority:** U5 🥇 · U17 🥇 · U24 🥇 · U25 🥇 · U28 🥇 · U29 🥇 · U32 🥈 · U34 🥇 · U43 🥇 · U47 🥈 · U48 🥇 · U52 🥇 · U53 🥈 · U55 📘 · U60 🥇 · U62 🥇 · U69 🥇 · U70 🥇 · U71 🥇 · U73 📘
_🥇 the site's own `correct_key` · 🥈 confirmed by the site's grader · 📘 doc-verified, no grader · 🤔 reasoned only_

---

### U5 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q5` · `T-Q48` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Three sub-agents searched overlapping territory and several findings repeat across their reports. Before synthesis, the coordinator should:

**Correct: C — "Merge the reports, collapse duplicate findings, and keep one cited instance of each."**

De-duplication is the coordinator's job precisely because it is the only component that sees all three reports. Collapsing to one *cited* instance keeps the audit trail intact while removing the repetition that would otherwise read as three independent confirmations.

**Wrong answers**

- **A. "Concatenate all three reports verbatim into the final answer."** — Ships the redundancy to the reader and inflates apparent corroboration: the same fact found three times by three overlapping searches is still one fact.
- **B. "Keep the first report and discard the other two unread."** — Discards the non-overlapping findings, which is the entire reason you dispatched three sub-agents.
- **D. "Ask the user to remove the duplicates."** — Pushes the orchestrator's core synthesis responsibility onto the user.

**Takeaway:** Overlapping sub-agent coverage is expected and healthy; reconciling it is the coordinator's work, not the reader's.

---

### U17 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q17` · `T-Q44` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A research agent must gather facts from eight independent web sources and produce one synthesis. None of the sources depend on each other. Which dispatch pattern stays fast without flooding the coordinator context?

**Correct: B — "Dispatch eight sub-agents in parallel, each returning a short structured summary with citations, then synthesize from the summaries."**

The sources are independent, so fan-out is free latency-wise, and each sub-agent absorbs the raw page in its *own* context. The coordinator only ever sees eight compact cited summaries — fast and context-light at the same time.

**Wrong answers**

- **A. "Read all eight sources into the coordinator context, then write the synthesis in a single pass."** — Floods the coordinator with eight full pages of raw content, which is precisely the failure mode the question asks you to avoid.
- **C. "Process the sources one at a time in a single agent, appending each full page to the running prompt."** — Worst of both: serial latency *and* unbounded context growth, with earlier sources degrading as later ones pile on.
- **D. "Pick the two sources that look most promising and ignore the rest to save tokens."** — Buys efficiency by discarding six of eight sources. That's not an optimisation, it's an incomplete answer.

**Takeaway:** Parallel sub-agents + compact cited returns is the canonical orchestrator–worker research pattern. Isolation buys context; independence buys speed.

---

### U24 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q24` · `T-Q53` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> You are designing how sub-agents report findings so the final research output can be audited later. Each finding should travel with:

**Correct: B — "The claim plus a reference to its source (URL or document id and location)."**

Auditability means a reader can trace any claim back to where it came from. Pairing each claim with a source pointer does that at negligible token cost, and it survives every downstream summarization step because the pointer travels *with* the claim.

**Wrong answers**

- **A. "Only the claim text, to keep messages short."** — Saves a handful of tokens and destroys auditability. Once the claim is detached from its source, the attribution cannot be reconstructed — see [[#U28 — research_pipeline]] for exactly this failure in production.
- **C. "The full raw page the claim came from, inline in every message."** — Auditable but ruinous for context: the coordinator drowns in raw pages, which is the problem sub-agents exist to prevent. A pointer gives the same traceability for a fraction of the cost.
- **D. "A confidence score and nothing else."** — A number with no provenance. You can't verify a claim you can't locate, and an uncited confidence score is unfalsifiable.

**Takeaway:** Claim + source pointer is the atomic unit of an auditable research pipeline. Not the raw page, not the bare claim.

---

### U25 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q25` · `O-Q11` · `T-Q54` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A user is expanding the research system beyond its single web search agent by adding specialized data sources. They add a financial API agent that returns structured JSON with revenue, margins, and growth rates; a news monitoring agent that returns prose summaries of recent developments; and a patent analysis agent that returns structured lists of technology areas. The synthesis agent combines these into executive briefings. Currently, it converts everything to bullet points, causing financial comparisons to lose tabular clarity and news summaries to lose narrative flow. What change would most improve briefing quality?

**Correct: C — "Update the synthesis agent to render each content type appropriately—financial data as tables, news as prose."**

Different content has different natural form. Financial comparisons need tabular alignment; news needs narrative. Fix the *rendering* stage — the one place that knows it's producing an executive briefing — rather than flattening everything upstream.

**Wrong answers**

- **A. "Standardize all subagent outputs to prose summaries with inline citations."** — Destroys the structure in the financial and patent data at the source. Revenue, margins, and growth rates in prose are strictly harder to compare than in a table.
- **B. "Add a format conversion layer between subagents and synthesis that transforms all outputs to a common intermediate representation."** — Adds a component whose job is to erase the type distinctions the briefing needs. It relocates the flattening rather than removing it.
- **D. "Standardize all subagent outputs to JSON with fields for claim, evidence, source, and confidence."** — A good schema for *auditability* (see [[#U24 — research_pipeline]]) but the wrong fix here: forcing news summaries into claim/evidence tuples shreds the narrative flow the briefing wants, and the synthesis agent still has to decide how to render.

**Takeaway:** Standardize *metadata* (sources, dates, confidence); preserve *content shape*. Presentation decisions belong at the presentation layer.

**Sources** _(carried over from `O-Q11` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Effective context engineering for AI agents" (preserve the highest-fidelity representation that fits the data, avoid lossy uniformity), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

> [!WARNING] U48 and U25 are the same trap in different clothes
> Both offer a **normalization layer** that converts heterogeneous inputs into one shape. Both are wrong, for the same reason: **the differences carry information**. Normalize only when the variation is noise (formatting, units, casing). Never when it is signal (methodology, confidence kind, content type).

---

### U28 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q28` · `O-Q9` · `T-Q15` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> In production, final reports frequently contain claims without proper source attribution. Investigation shows that while the web search and document analysis agents correctly attach citations to their outputs, the synthesis agent loses track of which sources support which conclusions when combining findings. What's the most effective architectural change?

**Correct: B — "Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings from multiple sources."**

Attribution is lost at the merge step, so bind claims to sources in a structure that *survives* merging. When each finding is a claim-source pair, combining findings is a set operation that carries provenance along by construction — nothing has to be reconstructed later.

**Wrong answers**

- **A. "Maintain complete transcripts of all subagent interactions and add a citation-resolution agent to analyze logs and determine attributions before report generation."** — Expensive forensic reconstruction of information you had for free upstream. Storing full transcripts is costly, and re-deriving attribution from logs is guesswork.
- **C. "Add a verification step where the report generator uses semantic similarity matching against original sources to reconstruct which claims came from which documents."** — Similarity matching *guesses* attribution. Two sources making the same point are indistinguishable, and a paraphrased claim may match the wrong document — producing confident but wrong citations, the worst outcome for an auditable report.
- **D. "Have the coordinator inject source identifier prefixes into text before each handoff, then parse these prefixes at report generation to reconstruct citations."** — String-tagging inside free text is fragile: the synthesis agent rewrites, merges, and paraphrases, and the prefixes get dropped or misattached in the process. That's the same loss you started with, now with a parser.

**Takeaway:** Provenance must be *structural*, not textual. If attribution can be lost by rewording, it will be.

**Sources** _(carried over from `O-Q9` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Effective context engineering for AI agents" (structured note-taking survives merges/compaction; prose tokens get dropped), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (explicit citation attribution as a dedicated step), https://www.anthropic.com/engineering/multi-agent-research-system

---

### U29 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q29` · `O-Q12` · `T-Q50` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The coordinator provides detailed step-by-step instructions to the web search subagent, specifying exact search queries, source priorities, and date filters. Production monitoring reveals three issues: (1) the subagent reports "insufficient results" rather than trying alternative approaches when pre-specified searches fail, (2) research quality drops for emerging topics that don't match expected patterns, and (3) the subagent rarely surfaces valuable tangential sources. What's the most effective way to improve subagent adaptability?

**Correct: D — "Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy."**

All three symptoms trace to over-specification: the sub-agent executes your queries rather than pursuing your objective, so it has no mandate to adapt when the queries miss. Delegate the *goal* plus measurable quality criteria and the sub-agent can reformulate, handle emerging topics, and follow promising tangents — because those now serve the brief instead of violating it.

**Wrong answers**

- **A. "Remove procedural details entirely, delegating with simple goals like 'research X thoroughly' and relying on the subagent's general capabilities."** — Over-corrects. "Thoroughly" is unmeasurable, so you lose the ability to steer coverage, source diversity, or recency at all. The fix is replacing procedure with *criteria*, not with vagueness.
- **B. "Add explicit fallback directives to the detailed instructions: 'If specified searches yield fewer than N results, attempt alternative query formulations before reporting failure.'"** — Patches symptom (1) only, by adding a second layer of procedure. Emerging topics and tangential sources are untouched, and you'll be adding a new directive for each new failure mode forever.
- **C. "Implement a topic classification step where the coordinator categorizes requests as 'well-defined' or 'exploratory' and uses different instruction styles for each category."** — Adds a classifier and keeps the brittle procedural style for everything it labels "well-defined" — including topics that only *look* well-defined until the searches fail.

**Takeaway:** Delegate goals and quality bars, not step-by-step procedure. Procedural delegation caps a sub-agent at your foresight.

**Sources** _(carried over from `O-Q12` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "How we built our multi-agent research system" (give subagents an objective + output format + tool/source guidance; research is dynamic, can't hardcode the path), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (agents direct their own process; orchestrator delegates outcomes, keeps guardrails), https://www.anthropic.com/engineering/building-effective-agents

---

### U32 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q32` · `O-Q5` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting

> In production, you observe that simple fact-checking queries (e.g., "What year was the Paris Climate Agreement signed?") traverse all four subagents sequentially, consuming 40+ seconds and significant tokens per query. Complex comparative research benefits from the full pipeline. Your query distribution is diverse and evolving as users discover new applications. What's the most effective approach to optimize for varying query complexity?

**Correct: C — "Have the coordinator analyze each query and dynamically decide which subagents to invoke based on its assessment of query requirements."**

The query distribution is "diverse and evolving," which rules out anything that has to be enumerated or trained in advance. Routing is a judgment call, and the coordinator is already reading every query — let it decide how much pipeline each one needs.

**Wrong answers**

- **A. "Implement pattern-based routing that categorizes queries by structure (single-fact vs. comparative vs. analytical) and maps each category to a predefined subagent combination."** — Static categories against an evolving distribution. Every genuinely new application lands in the wrong bucket or none, and you maintain the mapping forever.
- **B. "Create a fast-path for factual questions that bypasses subagents entirely, routing all other queries through the complete pipeline to ensure research thoroughness."** — A two-speed system for a spectrum of complexity. Everything that isn't a bare fact still pays the full 40-second pipeline, including the many mid-complexity queries that need two sub-agents rather than four.
- **D. "Train a query complexity classifier on labeled historical data to predict optimal subagent combinations, retraining periodically as query patterns evolve."** — Heavy ML machinery — labels, training, retraining cadence — to approximate a judgment the coordinator model already makes natively, and it lags the distribution by one retraining cycle by construction.

**Takeaway:** When routing depends on judgment over an open-ended input space, let the model route. Save static rules for closed, stable sets.

**Sources** _(carried over from `O-Q5` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Building effective agents" (orchestrator-workers: subtasks determined dynamically by the orchestrator), https://www.anthropic.com/engineering/building-effective-agents
- [2] Anthropic — "How we built our multi-agent research system" (scale effort to query complexity), https://www.anthropic.com/engineering/multi-agent-research-system

---

### U34 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q34` · `T-Q35` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A research agent keeps spawning follow-up searches and the run is not converging. The most reliable way to prevent an endless loop is to:

**Correct: B — "Give the task an explicit budget and a coverage check, and stop once the questions are answered or the budget is spent."**

Two independent stopping conditions: a *semantic* one (the research questions are answered) that ends the run when the work is genuinely done, and a *resource* one (the budget) that guarantees termination even when it isn't. That combination is what makes non-convergence impossible rather than merely unlikely.

**Wrong answers**

- **A. "Let it continue until it naturally stops."** — This is the current behaviour, and the premise says it isn't converging.
- **C. "Cut the run off at a random time."** — Bounds the cost but at an arbitrary point, so you get a truncated answer with no signal about what's missing. A budget with a coverage check tells you *why* it stopped.
- **D. "Add more sub-agents so it finishes sooner."** — More agents spawn more follow-up searches. You scale the divergence rather than bounding it.

**Takeaway:** Every agentic loop needs a termination condition it cannot argue with. Pair "am I done?" with "have I spent enough?"

---

### U43 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q43` · `O-Q6` · `T-Q47` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> When researching "renewable energy adoption," the web search agent returns recent statistics (2024: 35% adoption) while the document analysis agent extracts data from internal reports (2022: 18% adoption). The synthesis agent incorrectly flags these as contradictory sources rather than recognizing the data shows growth over time. What change would best enable the synthesis agent to correctly interpret such temporal differences?

**Correct: A — "Require subagents to include publication or data collection dates in their structured outputs."**

The 35% and 18% figures aren't contradictory — they're a time series, and the synthesis agent can only see that if the dates travel with the numbers as structured metadata. Add the date field and the "conflict" resolves itself into growth.

**Wrong answers**

- **B. "Add a conflict resolution agent that automatically discards older data when newer data exists for the same metric."** — Throws away the 2022 baseline, which is the data point that makes the trend meaningful. It also treats a temporal series as a conflict to be settled rather than a story to be told.
- **C. "Configure the web search agent to only return results from the past 6 months."** — Cripples the pipeline for every legitimately historical research question, and doesn't even fix this case: the internal 2022 report still arrives from the document analysis agent.
- **D. "Instruct the synthesis agent to always treat the most recent data as authoritative and place older findings in a separate historical appendix."** — Structurally separates the two figures, which prevents the synthesis agent from ever connecting them into a growth narrative. It also enshrines "newest wins," which is wrong when the older source is more rigorous.

**Takeaway:** Temporal metadata converts apparent contradictions into trends. Dates are part of a finding, not decoration on it.

**Sources** _(carried over from `O-Q6` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Effective context engineering for AI agents" (structured, metadata-rich context beats prose/rules for downstream reasoning), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (subagents need explicit output formats), https://www.anthropic.com/engineering/multi-agent-research-system

---

### U47 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q47` · `O-Q10` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting

> After the web search agent and document analysis agent complete their tasks, the coordinator invokes the synthesis agent. However, the synthesis agent responds that it cannot complete the task because no research findings were provided. What is the most likely cause of this issue?

**Correct: B — "The coordinator did not include the outputs from the previous agents in the synthesis agent's prompt."**

Sub-agents don't share context. Each invocation sees only what the coordinator puts in its prompt, so if the findings aren't in there, the synthesis agent genuinely has nothing — and reports exactly that.

**Wrong answers**

- **A. "The synthesis agent's context window is not large enough to hold the combined outputs from both previous agents."** — An overflow produces a truncation or size error, not "no research findings were provided." That message means zero input, not too much.
- **C. "The subagents need to share a single API connection to enable automatic context sharing between invocations."** — No such mechanism exists. Context is passed explicitly in prompts; connections don't share state.
- **D. "The synthesis agent needs tools that can fetch results directly from the other agents' conversation histories."** — Describes an architecture that isn't how the orchestrator–worker pattern works. Workers report to the coordinator; they don't read each other's transcripts.

**Takeaway:** The coordinator is the sole communication hub. If a sub-agent lacks information, ask what the coordinator put in its prompt. Same fact as [[#U60 — research_pipeline]].

**Sources** _(carried over from `O-Q10` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Effective context engineering for AI agents" (subagents run in isolated context windows), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (subagents need explicit objective + inputs in their task description), https://www.anthropic.com/engineering/multi-agent-research-system

---

### U48 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q48` · `O-Q8` · `T-Q21` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Production reviews reveal inconsistent handling of uncertainty in final reports. Sometimes conflicting subagent findings are synthesized into a single confident statement (losing nuance), while other times reports over-hedge with excessive qualifications (becoming unhelpful). When the web search agent returns "industry analysts estimate $50B market size (methodology varies)" and the document analysis agent returns "peer-reviewed study estimates $35B(±$7B, 95% CI)," the coordinator either picks one arbitrarily or produces vague statements like "the market may be $35B−$50B depending on factors." What systematic approach best addresses this?

**Correct: C — "Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established findings from contested ones, preserving original source characterizations and methodological context."**

The two failure modes — false confidence and mush — are both consequences of *flattening* uncertainty. Preserving each source's own characterization ("analyst estimate, methodology varies" vs. "peer-reviewed, ±$7B, 95% CI") and separating settled from contested lets the reader weigh the evidence, which is what an honest research report does.

**Wrong answers**

- **A. "Configure subagents to only report findings meeting a high-confidence threshold, filtering uncertain information before it reaches the coordinator."** — Solves inconsistency by deleting information. Genuinely contested findings — often the most decision-relevant ones — never reach the report at all.
- **B. "Implement a confidence calibration layer that normalizes subagent uncertainty expressions to standardized probability scores (0.0-1.0), then weight-average findings by their calibrated confidence."** — Fabricates false precision: "methodology varies" doesn't have a defensible numeric score. Weight-averaging $50B and $35B produces a number no source supports and destroys the methodological distinction that actually matters.
- **D. "Add a verification subagent that cross-references findings across sources, only passing claims to synthesis that are corroborated by at least two independent sources."** — Discards single-source findings, including a rigorous peer-reviewed study with no second source. Corroboration count is a poor proxy for evidential quality.

**Takeaway:** Preserve uncertainty; don't collapse or average it. Structure the report so the reader sees which claims are settled and which are contested.

**Sources** _(carried over from `O-Q8` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "Effective context engineering for AI agents" (high-fidelity distillation; preserve the signal that matters rather than lossy averaging), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

> [!WARNING] The trap: numeric machinery reads as rigor
> "Calibration layer", "standardized probability scores", "weight-average" all sound more engineered than "structure the report into sections". They are more engineered — and wrong, because the engineering destroys information. Ask what the pipeline is *for*: an auditable research report, not a point estimate.
>
> **Status of this reasoning:** sound, but *not* documented Anthropic doctrine — no official page addresses scalar normalization of heterogeneous confidence. Official guidance does support preserving source grounding and letting the model express uncertainty. Treat the argument as reasoning you can defend, not a rule you can cite.

---

### U52 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q52` · `T-Q30` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Two sub-agents return conflicting figures for the same metric, each with moderate confidence. Before the coordinator writes the final answer, the best move is to:

**Correct: C — "Run a focused check that re-fetches the metric from the primary source and resolves the conflict before synthesizing."**

Two moderate-confidence figures for one metric means at least one is wrong, and the answer is knowable — go back to the primary source. A single targeted fetch is cheap relative to publishing a wrong number.

**Wrong answers**

- **A. "Average the two numbers and move on."** — Produces a figure neither source supports, and if one value is simply an error the average is guaranteed wrong.
- **B. "Take whichever sub-agent answered first."** — Response order carries no information about accuracy.
- **D. "Include both numbers in the final answer and let the reader decide."** — Defensible when a conflict is genuinely unresolvable ([[#U48 — research_pipeline]]), but here it isn't — the primary source can settle it. Passing a resolvable conflict to the reader is abdication.

**Takeaway:** Resolve conflicts against the primary source when you can; surface them with full context only when you can't.

---

### U53 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q53` · `O-Q13` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting

> Production monitoring shows that follow-up queries like "summarize what we learned about market trends" consistently take 40+ seconds. Investigation reveals the coordinator spawns the synthesis subagent for each summarization request, passing 80K+ tokens of accumulated findings. The coordinator already has these findings in its context from orchestrating the research. What's the most effective way to improve response time for these follow-up summaries?

**Correct: B — "Have the coordinator handle straightforward summarization requests directly using its existing context, reserving subagent spawning for complex analysis."**

The coordinator already holds the findings. Spawning a sub-agent means re-transferring 80K+ tokens it doesn't need to move, so the 40 seconds is pure overhead. Summarising in place removes the transfer entirely — and sub-agents stay available for work that genuinely needs isolation.

**Wrong answers**

- **A. "Pre-generate and cache summaries at multiple granularities whenever new findings accumulate."** — Speculative work on every finding update, most of it never read, and the cache goes stale the moment new findings land. Complexity for a problem that disappears if you just don't spawn.
- **C. "Enable prompt caching on the synthesis subagent to reduce the overhead of repeatedly transferring the same research findings."** — Reduces cost and time-to-first-token but keeps the spawn and the transfer. And "accumulated findings" change between requests, so the cache prefix keeps invalidating.
- **D. "Spawn the synthesis subagent with reduced context and have it request specific findings from the coordinator on-demand."** — Trades one big transfer for several round-trips, adding latency in a question about latency, and builds a request protocol to fetch data the coordinator could have used directly.

**Takeaway:** Delegate for *context isolation*, not out of habit. If the coordinator already has the data and the task is simple, doing it inline is the optimisation.

**Sources** _(carried over from `O-Q13` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "How we built our multi-agent research system" (multi-agent uses ~15× tokens; reserve for high-value parallel/over-window tasks), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (use the simplest solution that works; add agentic complexity only when it pays off), https://www.anthropic.com/engineering/building-effective-agents

---

### U55 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q55` · `O-Q14` — **2 sittings, all agreeing**  
**Authority:** 📘 doc-verified — no grader ever saw it; two sittings agree and the reasoning is doc-cited

> When analyzing complex legal cases that cite multiple precedents, the document analysis subagent processes each sequentially. A landmark case citing 12 precedents takes over 3 minutes to analyze completely. What's the most effective way to reduce this latency while preserving the coordinator's ability to monitor and debug the system?

**Correct: C — "Have the coordinator spawn parallel document analysis subagents, each handling a subset of precedents, then aggregate results before synthesis."**

The 12 precedents are independent, so parallel fan-out attacks the latency directly. Crucially, the coordinator stays the single hub — it spawned every sub-agent and receives every result, which is what preserves the monitoring and debugging the question explicitly requires.

**Wrong answers**

- **A. "Implement a message queue where precedent analysis tasks are processed asynchronously by a pool of worker agents."** — Achieves parallelism but interposes infrastructure between the coordinator and the workers. Tracing which task produced which finding now means correlating across queue logs — the observability cost the question warns about.
- **B. "Create a recursive agent hierarchy where analysis agents subdivide work among child agents until reaching single-precedent granularity."** — Deep nesting for a flat, already-known work list of 12 items. Each level adds hand-off overhead and distance from the coordinator, and the recursion buys nothing over a single fan-out.
- **D. "Enable the document analysis subagent to spawn its own specialized subagents dynamically when it encounters cases with many citations."** — Moves orchestration below the coordinator, which then can't see or debug the sub-sub-agents it never spawned. Keep fan-out at the level that owns observability.

**Takeaway:** Parallelize from the coordinator. Flat fan-out preserves the audit trail; nesting and queues erode it.

**Sources** _(carried over from `O-Q14` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "How we built our multi-agent research system" (lead agent spawns parallel subagents with separate context windows, then synthesizes), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (orchestrator-workers; orchestrator stays the central coordinator), https://www.anthropic.com/engineering/building-effective-agents

---

### U60 — research_pipeline

🅰 full MCQ · **Seen as:** `N-Q60` · `O-Q4` · `T-Q32` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The web search agent has gathered several relevant sources for a research topic. The document analysis agent now needs to examine these sources. How does information typically flow between these two specialized subagents?

**Correct: C — "The coordinator agent receives the web search agent's output and includes relevant findings in the prompt when invoking the document analysis agent."**

Hub-and-spoke. Sub-agents are context-isolated and never talk to each other; the coordinator collects each worker's output and decides what to include in the next worker's prompt. That single hub is what makes the system auditable and debuggable.

**Wrong answers**

- **A. "The agents communicate through an event-driven message queue, with the document analysis agent subscribing to web search completion events."** — Describes a distributed microservice architecture, not the orchestrator–worker pattern. No pub/sub channel exists between sub-agents.
- **B. "The web search agent directly invokes the document analysis agent, passing the discovered sources as parameters."** — Peer-to-peer invocation bypasses the coordinator, which then can't see or control the hand-off. (Same anti-pattern as option D in [[#U55 — research_pipeline]].)
- **D. "Both agents access a shared memory store where the web search agent writes findings and the document analysis agent reads them."** — Shared mutable state between sub-agents isn't the model. Context isolation is a feature, and implicit sharing would undermine both the isolation and the audit trail.

**Takeaway:** All inter-agent information flows *through* the coordinator's prompts. If you catch yourself drawing an arrow between two workers, it's the wrong diagram.

**Sources** _(carried over from `O-Q4` — the Mock Exam key cites docs on every entry; the New Mock key cites none)_

- [1] Anthropic — "How we built our multi-agent research system" (lead agent decomposes and describes subtasks to subagents), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Effective context engineering for AI agents" (subagents have isolated/clean context windows; main agent coordinates), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

> [!WARNING] "Subagents can never talk directly" is overstated — and the vault says so elsewhere too
> A subagent granted the **`SendMessage`** tool starts with a list of the other named agents in the session and can message them directly. So hub-and-spoke is a **design principle and the exam answer**, not an SDK constraint — exactly the same shape as the nesting correction `AGENTS.md` already records. C is still right here: nothing in the stem grants `SendMessage`, and routing through the coordinator is what keeps context isolated and auditable.
> Source: <https://code.claude.com/docs/en/agent-sdk/subagents>

---

### U62 — research_pipeline

✍️ open-response · **Seen as:** `O-Q15` · `T-Q8` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The coordinator agent has `AgentDefinition`s configured for all four specialized subagents, each with appropriate descriptions, prompts, and tool restrictions. During testing, you notice the coordinator correctly reasons about when to delegate—it generates messages like "I'll ask the web search agent to find sources on this topic"—but no subagent execution ever occurs. The coordinator then proceeds as if the delegation happened and continues with incomplete information. Logs show no errors. What is the most likely cause?

**Correct: C — "The coordinator's `allowedTools` configuration doesn't include "`Task`", so while it can reason about delegation, it cannot invoke the tool required to spawn subagents."**

**Why it's correct:** Subagents are spawned via the `Task` tool; if `Task` isn't in `allowedTools`, the model can narrate intent but has no callable mechanism to act on it — producing the exact "reasons about it, no execution, no errors" symptom. A tool the model can't call simply never appears as an executable `tool_use`, so nothing runs and nothing errors [1].

**Why the others are wrong:**
- **A:** A `max_tokens` truncation would show in logs and leave partial `tool_use` blocks, not a silent no-op.
- **B:** Tool/agent schemas are surfaced to the model automatically from the tool definitions — you don't re-list them in the system prompt for the model to "see" them [1].
- **D:** Context isolation is real but governs what a subagent sees *once spawned* — not whether the coordinator can spawn it at all.

**Key takeaway:** An agent can only *do* what its **allowed tools** permit — to delegate, the coordinator must have the `Task` (subagent-spawn) tool enabled.

**Sources**

- [1] Claude API Docs — "Define tools / Implement tool use" (the API builds the tool system prompt from tool definitions; only enabled tools are callable; forced/unavailable tools change emitted blocks), https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

> [!WARNING] This option's terminology is wrong, and its failure mode is the *other* one — verified against official docs
> **Field name.** The keyed answer says `allowedTools`. The `AgentDefinition` inner field is **`tools`**; `allowedTools` is CLI/SDK permission vocabulary, not an `AgentDefinition` field. The vault records this in [[D1 - Agentic Architecture & Orchestration]] and `AGENTS.md` § Known corrections.
>
> **Which omission is silent.** This matters more than the name. Omitting `Agent` from the top-level `allowedTools` does **not** fail silently — the call routes to your `canUseTool` callback, or is denied in `dontAsk` mode. Either way it surfaces. The genuinely silent failure is omission from **`AgentDefinition.tools`**: a tool left out isn't in the subagent's session at all, so Claude simply works without it, with no prompt and no error. The stem's *"logs show no errors"* fits that mechanism, not the `allowedTools` one. [[Answer Patterns Index]] § Tier 3 already draws this distinction correctly.
>
> **The rename.** `Task` → `Agent` in Claude Code v2.1.63, and the alias is narrower than "still valid" suggests: the current tools reference lists only `Agent`; `Task` survives in the SDK's `system:init` tools list and in `result.permission_denials[].tool_name`. `Task` remains the exam-safe answer. Beware a name collision — `TaskCreate`/`TaskGet`/`TaskList`/`TaskUpdate` in the tools reference are **task-list** tools, unrelated to spawning.
>
> Source: <https://code.claude.com/docs/en/agent-sdk/subagents>

---

### U69 — research_pipeline

✍️ open-response · **Seen as:** `O-Q3` · `T-Q46` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The document analysis agent has a single `analyze_document` tool that takes a document and a free-text instruction parameter. During evaluation, requests like "extract the key financial metrics" often return narrative summaries, while "summarize the methodology" sometimes returns raw data tables. The synthesis agent reports that 35% of analysis results require re-requests with clarified instructions. What's the most effective way to improve reliability?

**Correct: A — "Split the generic tool into purpose-specific tools—`extract_data_points`, `summarize_content`, `verify_claim_against_source`—each with defined input/output contracts."**

**Why it's correct:** Free-text instructions put the semantics in prose, which the model interprets inconsistently; well-scoped tools with explicit, typed I/O contracts give the model a clean choice and a predictable output shape — the agent-computer interface (ACI) is as important as the prompt, and clear tool boundaries plus detailed descriptions are the single biggest lever on tool-use reliability [1][2].

**Why the others are wrong:**
- **B:** An `analysis_type` enum is better than free text but still one tool with one generic string output shape; the model can mode-mismatch and outputs aren't contract-distinct. Separate tools enforce distinct I/O.
- **C:** Coordinator pre-classification moves the ambiguity upstream without fixing the fuzzy tool contract, and adds a new error surface.
- **D:** More examples in the description help, but reliability comes first from clear tool boundaries and output schemas, not heroic prompt engineering on one overloaded tool [2].

**Key takeaway:** Prefer **several focused tools with explicit I/O contracts** over one generic free-text tool — design the ACI, don't paper over it with prompt text.

**Sources**

- [1] Anthropic — "Building effective agents" (agent-computer interface / tool design matters as much as prompts), https://www.anthropic.com/engineering/building-effective-agents
- [2] Claude API Docs — "Define tools / Implement tool use" (extremely detailed descriptions; clear tool boundaries are the most important factor), https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools

> [!WARNING] Don't generalise this to "always split tools" — current docs push the other way
> Official tool-design guidance now says to **consolidate** related operations into fewer, more capable tools, grouping them under an `action` parameter, because fewer tools reduce selection ambiguity. A is still right here because the defect is the **unspecified free-text contract**, not the tool count — three tools with defined I/O beat one tool with an open instruction field. But the portable rule is *specify the contract*, not *increase the tool count*. A single `analyze_document(document, action: enum, …)` with typed outputs per action would also have fixed this.
> Source: <https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools>

---

### U70 — research_pipeline

✍️ open-response · **Seen as:** `O-Q7` · `T-Q49` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The synthesis agent receives summarized findings from the web search and document analysis agents, then passes a consolidated summary to the report generator. During testing, you discover the generated reports make factual claims without proper citations—the report generator cannot attribute statements to their original sources because that metadata was lost during the summarization steps. What's the most effective approach to ensure proper source attribution in the final reports?

**Correct: A — "Have each agent output structured data separating content summaries from source metadata (URLs, document names, page numbers)."**

**Why it's correct:** Keeping content and source metadata as distinct structured fields preserves the claim→source mapping end-to-end, so the report generator receives both *what was said* and *where it came from* [1]. Anthropic's Research pipeline solves attribution with a dedicated citation step that operates over the documents and report so "all claims are properly attributed to their sources" — the mapping must be preserved as first-class data, not embedded in prose [2].

**Why the others are wrong:**
- **B:** Re-querying the web search agent to re-locate sources is slow, lossy, and invites misattribution — that agent doesn't know which claim came from which source.
- **C:** Inline citations inside free text drift and get dropped during later summarization; structured metadata survives transformations [1].
- **D:** Skipping summarization blows the context budget and makes the generator's job harder; summarization is useful — losing metadata *during* it is the actual bug [1].

**Key takeaway:** Keep **source metadata in structured fields separate from prose** so it survives every summarization hop.

**Sources**

- [1] Anthropic — "Effective context engineering for AI agents" (structured note-taking / metadata survives compaction better than prose), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (CitationAgent attributes all claims to sources), https://www.anthropic.com/engineering/multi-agent-research-system

> [!WARNING] You got the identical question right earlier in the same sitting
> **U28** — same failure, citations lost in synthesis — and you correctly chose *structured claim-source mappings*. Thirty-four questions later you chose inline prose citations for the same problem. The rule you already know: **provenance is a field, never a sentence.**

---

### U71 — research_pipeline

✍️ open-response · **Seen as:** `O-Q1` · `T-Q51` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your multi-agent research pipeline crashed after processing 12 of 28 documents. The web search agent had identified relevant sources, the document analysis agent had partially completed extraction, and the synthesizer had begun pattern identification. You need to resume processing without repeating work or losing fidelity of prior findings. What state management approach best balances information fidelity with context efficiency when restoring agent state?

**Correct: C — "Have each agent persist a structured report to a known location. On resume, the coordinator loads the reports and injects relevant state into agent prompts."**

**Why it's correct:** This is the orchestrator + compact-artifact pattern. Subagents are designed to explore extensively but return only a "condensed, distilled summary" while the lead agent coordinates from a high-level plan [1]. A structured report preserves the findings (with schema) at high fidelity while keeping each subagent's context focused, and it leaves orchestration with the coordinator — exactly the separation of concerns Anthropic recommends [1][2]. Anthropic's own Research LeadResearcher persists its plan/state to Memory precisely so work survives a context reset [2].

**Why the others are wrong:**
- **A:** Per-agent self-reloaded state fragments the picture, breaks coordinator visibility into cross-agent reasoning, and reloads noise into each subagent's context — the opposite of the clean, focused context windows subagents should have [1].
- **B:** Replaying the coordinator's full conversation log is the highest-fidelity but least context-efficient choice; you'd push everything into each subagent and blow the context budget — Anthropic stresses context is finite and compaction/distillation is the lever [1].
- **D:** A vector store for resume state is over-engineered; it adds a retrieval failure mode and semantic search can miss the *exact* state a structured report preserves losslessly. RAG's static similarity retrieval is a different tool than precise state restore [2].

**Key takeaway:** For resume/crash recovery, persist compact **structured per-agent artifacts** and let the coordinator re-inject relevant state — fidelity plus context efficiency, orchestration stays centralized.

**Sources**

- [1] Anthropic — "Effective context engineering for AI agents" (sub-agent architectures: clean context windows, condensed 1k–2k-token summaries), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (LeadResearcher saves its plan to Memory to persist context across resets), https://www.anthropic.com/engineering/multi-agent-research-system

---

### U73 — research_pipeline

✍️ open-response · **Seen as:** `O-Q2` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> After web search (25 sources, 120K tokens raw), document analysis (15K tokens insights), and synthesis (3K-token draft), the coordinator must hand off to the report-generation agent so it can produce a final report with proper citations. Which context-passing strategy best balances completeness and efficiency?

**Correct: B — "Pass the synthesis draft plus a structured source index that maps key claims to source URLs and relevant excerpts."**

**Why it's correct:** The narrative comes from the synthesis draft; the structured source index gives the report generator the exact claim→source binding it needs to cite without re-ingesting 120K tokens of raw content [1]. This mirrors Anthropic's Research system, where findings plus the documents are handed to a dedicated citation step so "all claims are properly attributed to their sources" [2], and reflects the compression principle — pass the distilled signal, not the raw corpus [1].

**Why the others are wrong:**
- **A:** Post-hoc claim-to-source matching loses the mapping the model actually used, so misattributed or hallucinated citations are the typical failure [2].
- **C:** Name-only attribution drops URLs and excerpts, so the generator can't quote or verify and tends to drift into vague citations.
- **D:** Passing full accumulated context maximizes completeness but wastes the budget — 120K+ tokens of raw search content is mostly irrelevant noise at report time [1].

**Key takeaway:** Hand downstream agents the **distilled narrative + a structured source index**, not raw content — preserve the claim→source binding explicitly.

**Sources**

- [1] Anthropic — "Effective context engineering for AI agents" (compression: distill the most important tokens, don't carry the corpus), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (CitationAgent processes documents + report to attribute claims), https://www.anthropic.com/engineering/multi-agent-research-system

---

**Back to:** [README.md](../README.md) · [Questions.md](../Questions.md)