---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - research-pipeline
date: 2026-08-24
status: done
---

# research_pipeline — CCAF Study Guide

> Domain scope: orchestrator–worker (lead agent + specialized subagents) research pipelines. The recurring theme across these questions is that **the coordinator/lead agent is the hub**: subagents are context-isolated, communicate only through what the coordinator explicitly puts in their prompts, return condensed structured artifacts, and are delegated *goals* rather than *procedures*. Citations below map to Anthropic engineering posts and the Claude API docs (see each question's Sources block).

---

### Q1 — research_pipeline
**Question:** A multi-agent research pipeline crashed mid-run (12/28 docs processed; web search, document analysis, and synthesis all partially complete). You must resume without repeating work or losing fidelity. Which state-management approach best balances information fidelity with context efficiency?

**Correct answer: C** — Each agent persists a structured report to a known location; on resume the coordinator loads those reports and injects relevant state into each agent's prompt.

**Why it's correct:** This is the orchestrator + compact-artifact pattern. Subagents are designed to explore extensively but return only a "condensed, distilled summary" while the lead agent coordinates from a high-level plan [1]. A structured report preserves the findings (with schema) at high fidelity while keeping each subagent's context focused, and it leaves orchestration with the coordinator — exactly the separation of concerns Anthropic recommends [1][2]. Anthropic's own Research LeadResearcher persists its plan/state to Memory precisely so work survives a context reset [2].

**Why the others are wrong:**
- **A:** Per-agent self-reloaded state fragments the picture, breaks coordinator visibility into cross-agent reasoning, and reloads noise into each subagent's context — the opposite of the clean, focused context windows subagents should have [1].
- **B:** Replaying the coordinator's full conversation log is the highest-fidelity but least context-efficient choice; you'd push everything into each subagent and blow the context budget — Anthropic stresses context is finite and compaction/distillation is the lever [1].
- **D:** A vector store for resume state is over-engineered; it adds a retrieval failure mode and semantic search can miss the *exact* state a structured report preserves losslessly. RAG's static similarity retrieval is a different tool than precise state restore [2].

**Key takeaway:** For resume/crash recovery, persist compact **structured per-agent artifacts** and let the coordinator re-inject relevant state — fidelity plus context efficiency, orchestration stays centralized.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (sub-agent architectures: clean context windows, condensed 1k–2k-token summaries), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (LeadResearcher saves its plan to Memory to persist context across resets), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q2 — research_pipeline
**Question:** After web search (25 sources, 120K tokens raw), document analysis (15K tokens insights), and synthesis (3K-token draft), the coordinator must hand off to the report-generation agent so it can produce a final report with proper citations. Which context-passing strategy best balances completeness and efficiency?

**Correct answer: B** — Pass the synthesis draft plus a structured source index that maps key claims to source URLs and relevant excerpts.

**Why it's correct:** The narrative comes from the synthesis draft; the structured source index gives the report generator the exact claim→source binding it needs to cite without re-ingesting 120K tokens of raw content [1]. This mirrors Anthropic's Research system, where findings plus the documents are handed to a dedicated citation step so "all claims are properly attributed to their sources" [2], and reflects the compression principle — pass the distilled signal, not the raw corpus [1].

**Why the others are wrong:**
- **A:** Post-hoc claim-to-source matching loses the mapping the model actually used, so misattributed or hallucinated citations are the typical failure [2].
- **C:** Name-only attribution drops URLs and excerpts, so the generator can't quote or verify and tends to drift into vague citations.
- **D:** Passing full accumulated context maximizes completeness but wastes the budget — 120K+ tokens of raw search content is mostly irrelevant noise at report time [1].

**Key takeaway:** Hand downstream agents the **distilled narrative + a structured source index**, not raw content — preserve the claim→source binding explicitly.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (compression: distill the most important tokens, don't carry the corpus), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (CitationAgent processes documents + report to attribute claims), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q3 — research_pipeline
**Question:** The document analysis agent has one generic `analyze_document(document, free_text_instruction)` tool. "Extract key financial metrics" returns prose; "summarize the methodology" sometimes returns tables; 35% of results need re-requests. How to improve reliability?

**Correct answer: A** — Split the generic tool into purpose-specific tools (`extract_data_points`, `summarize_content`, `verify_claim_against_source`), each with a defined input/output contract.

**Why it's correct:** Free-text instructions put the semantics in prose, which the model interprets inconsistently; well-scoped tools with explicit, typed I/O contracts give the model a clean choice and a predictable output shape — the agent-computer interface (ACI) is as important as the prompt, and clear tool boundaries plus detailed descriptions are the single biggest lever on tool-use reliability [1][2].

**Why the others are wrong:**
- **B:** An `analysis_type` enum is better than free text but still one tool with one generic string output shape; the model can mode-mismatch and outputs aren't contract-distinct. Separate tools enforce distinct I/O.
- **C:** Coordinator pre-classification moves the ambiguity upstream without fixing the fuzzy tool contract, and adds a new error surface.
- **D:** More examples in the description help, but reliability comes first from clear tool boundaries and output schemas, not heroic prompt engineering on one overloaded tool [2].

**Key takeaway:** Prefer **several focused tools with explicit I/O contracts** over one generic free-text tool — design the ACI, don't paper over it with prompt text.

**Sources:**
- [1] Anthropic — "Building effective agents" (agent-computer interface / tool design matters as much as prompts), https://www.anthropic.com/engineering/building-effective-agents
- [2] Claude API Docs — "Define tools / Implement tool use" (extremely detailed descriptions; clear tool boundaries are the most important factor), https://docs.claude.com/en/docs/agents-and-tools/tool-use/implement-tool-use

---

### Q4 — research_pipeline
**Question:** The web search agent has gathered sources; the document analysis agent must now examine them. How does information typically flow between these two subagents?

**Correct answer: C** — The coordinator receives the web search agent's output and includes the relevant findings in the prompt when invoking the document analysis agent.

**Why it's correct:** In the orchestrator-worker pattern the lead agent is the hub: it "decomposes queries into subtasks and describes them to subagents," each of which gets an objective, output format, and task boundaries from the coordinator [1]. Subagents operate in isolated context windows and don't know each other exists; everything they receive comes through the coordinator's prompt [2].

**Why the others are wrong:**
- **A:** Event-driven message queues aren't part of the Claude subagent model; subagents don't publish/subscribe to one another [1].
- **B:** Subagents are context-isolated and can't directly invoke sibling subagents — that would tightly couple them and defeat the orchestrator pattern [1][2].
- **D:** A shared memory store can be layered in as an optimization, but it isn't the default communication path and introduces stale-read/consistency issues; the canonical flow is coordinator-mediated prompts [2].

**Key takeaway:** Subagents never talk to each other — **the coordinator forwards relevant findings into each subagent's prompt.**

**Sources:**
- [1] Anthropic — "How we built our multi-agent research system" (lead agent decomposes and describes subtasks to subagents), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Effective context engineering for AI agents" (subagents have isolated/clean context windows; main agent coordinates), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

---

### Q5 — research_pipeline
**Question:** Simple fact-check queries traverse all four subagents (40+ s, heavy tokens); complex comparative queries genuinely need the full pipeline; the query distribution is diverse and evolving. How to optimize for varying complexity?

**Correct answer: C** — Have the coordinator analyze each query and dynamically decide which subagents to invoke.

**Why it's correct:** Dynamic, LLM-driven decomposition is the defining strength of the orchestrator-worker pattern: subtasks "aren't pre-defined, but determined by the orchestrator based on the specific input" [1]. Anthropic explicitly scales effort to query complexity — a simple fact-find needs ~1 agent with a few tool calls, while complex research uses many subagents — and embeds these as runtime judgments, not fixed routes [2].

**Why the others are wrong:**
- **A:** Pattern-based routing calcifies as the distribution evolves; new intents break the patterns and silently mis-route.
- **B:** A binary fast-path fixes the worst case but wastes the full pipeline on every moderately complex query that needed only a subset.
- **D:** A trained classifier needs labels, retraining, and drift monitoring — over-engineered when the coordinator LLM can make the same call at runtime from the query itself [1][2].

**Key takeaway:** Let the **coordinator reason per-query about which subagents to spawn** — runtime orchestration adapts to an evolving distribution; static routers/classifiers calcify.

**Sources:**
- [1] Anthropic — "Building effective agents" (orchestrator-workers: subtasks determined dynamically by the orchestrator), https://www.anthropic.com/engineering/building-effective-agents
- [2] Anthropic — "How we built our multi-agent research system" (scale effort to query complexity), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q6 — research_pipeline
**Question:** Web search returns "2024: 35% adoption"; document analysis returns "2022: 18% adoption." The synthesis agent flags these as contradictory instead of recognizing growth over time. What change best enables correct temporal interpretation?

**Correct answer: A** — Require subagents to include publication / data-collection dates in their structured outputs.

**Why it's correct:** Synthesis misreads the data because it never sees the dates. Carrying each data point's timestamp in the structured output lets synthesis reason about trends instead of contradictions — an instance of giving the downstream agent the metadata it needs in a structured form rather than relying on prose or a rule [1]. Structured artifacts that travel with their provenance are what survive multi-stage handoffs [2].

**Why the others are wrong:**
- **B:** A conflict-resolution agent that discards older data destroys the trend information that's the whole point of the question.
- **C:** Restricting the web search agent to the last 6 months throws away historical context and doesn't fix the architectural gap (no metadata reaching synthesis).
- **D:** A "most recent is authoritative" instruction is an unreliable prompt-level rule that hides temporal reasoning instead of enabling it; structuring the data with dates is the systematic fix [1].

**Key takeaway:** Attach **provenance/metadata (here, dates) to every structured data point** so the synthesizer can reason rather than guess.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (structured, metadata-rich context beats prose/rules for downstream reasoning), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (subagents need explicit output formats), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q7 — research_pipeline
**Question:** Reports make factual claims without citations because source metadata was lost during the summarization steps between agents. Most effective approach to ensure attribution?

**Correct answer: A** — Have each agent output structured data that separates content summaries from source metadata (URLs, document names, page numbers).

**Why it's correct:** Keeping content and source metadata as distinct structured fields preserves the claim→source mapping end-to-end, so the report generator receives both *what was said* and *where it came from* [1]. Anthropic's Research pipeline solves attribution with a dedicated citation step that operates over the documents and report so "all claims are properly attributed to their sources" — the mapping must be preserved as first-class data, not embedded in prose [2].

**Why the others are wrong:**
- **B:** Re-querying the web search agent to re-locate sources is slow, lossy, and invites misattribution — that agent doesn't know which claim came from which source.
- **C:** Inline citations inside free text drift and get dropped during later summarization; structured metadata survives transformations [1].
- **D:** Skipping summarization blows the context budget and makes the generator's job harder; summarization is useful — losing metadata *during* it is the actual bug [1].

**Key takeaway:** Keep **source metadata in structured fields separate from prose** so it survives every summarization hop.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (structured note-taking / metadata survives compaction better than prose), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (CitationAgent attributes all claims to sources), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q8 — research_pipeline
**Question:** Reports handle uncertainty inconsistently — sometimes collapsing conflicting findings (e.g., "$50B analyst estimate, methodology varies" vs. "$35B ±7B, 95% CI peer-reviewed study") into one confident claim, sometimes over-hedging. What systematic approach best addresses this?

**Correct answer: C** — Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established from contested findings, preserving original source characterizations and methodological context.

**Why it's correct:** The fix is report *structure* that retains methodological context and separates settled from contested claims — that yields nuance without over-hedging. This preserves the source characterizations as first-class context (the high-fidelity distillation principle) rather than discarding or averaging them away [1].

**Why the others are wrong:**
- **A:** A high-confidence threshold filter throws away useful-but-uncertain evidence and merely hides conflicts instead of helping synthesis reason about them.
- **B:** Normalizing CIs and analyst estimates to a single 0–1 score and weight-averaging collapses the methodological difference that's the entire point.
- **D:** A two-source-minimum verification subagent drops legitimate single-source findings and still doesn't resolve genuinely conflicting estimates with different methodologies.

**Key takeaway:** Preserve **methodological context and separate "established" vs. "contested"** rather than collapsing or filtering uncertainty.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (high-fidelity distillation; preserve the signal that matters rather than lossy averaging), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

---

### Q9 — research_pipeline
**Question:** Subagents correctly attach citations, but the synthesis agent loses track of which sources support which conclusions when merging findings. Most effective architectural change?

**Correct answer: B** — Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings.

**Why it's correct:** Explicit claim→source mappings are a first-class structured output the synthesis agent can merge deterministically, so no attribution is dropped during summarization [1]. This is the architectural analog of Anthropic's dedicated citation handling — keep the binding as data, not prose [2].

**Why the others are wrong:**
- **A:** Post-hoc log analysis with a citation-resolution agent is fragile and expensive, and still loses bindings when synthesis merges points from multiple sources.
- **C:** Semantic-similarity reconstruction can attribute the wrong source when two sources say similar things; you want the model's original mapping, not a guess.
- **D:** Source-identifier prefixes inside prose get dropped, paraphrased, or hallucinated; structured mappings outside the prose are far more robust [1].

**Key takeaway:** Make **claim→source mappings a structured, mergeable output**, not inline prose or a post-hoc reconstruction.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (structured note-taking survives merges/compaction; prose tokens get dropped), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (explicit citation attribution as a dedicated step), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q10 — research_pipeline
**Question:** After web search and document analysis complete, the coordinator invokes the synthesis agent, which replies it can't proceed because "no research findings were provided." Most likely cause?

**Correct answer: B** — The coordinator did not include the previous agents' outputs in the synthesis agent's prompt.

**Why it's correct:** Subagent invocations are context-isolated — nothing flows between them unless the coordinator explicitly puts it in the prompt [1][2]. "No findings provided" is exactly the symptom of an empty/under-specified subagent prompt; Anthropic notes that without detailed task descriptions (including the needed inputs) subagents "leave gaps or fail to find necessary information" [2].

**Why the others are wrong:**
- **A:** A context-window overflow surfaces as truncation or an API error, not as the agent calmly reporting "no findings."
- **C:** There is no "automatic context sharing" over a shared API connection — isolation is by design [1].
- **D:** Cross-agent history fetching isn't a standard capability and isn't the right fix; the coordinator should forward findings explicitly [2].

**Key takeaway:** A subagent sees **only what the coordinator puts in its prompt** — "no findings" means the coordinator forgot to forward them.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (subagents run in isolated context windows), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- [2] Anthropic — "How we built our multi-agent research system" (subagents need explicit objective + inputs in their task description), https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q11 — research_pipeline
**Question:** New specialized subagents return different shapes: financial API (structured JSON), news monitoring (prose), patent analysis (structured lists). The synthesis agent converts everything to bullets, losing tabular clarity and narrative flow. What change most improves briefing quality?

**Correct answer: C** — Update the synthesis agent to render each content type appropriately — financial data as tables, news as prose.

**Why it's correct:** Executive briefings need mixed rendering; asking synthesis to preserve each input's native format is the right abstraction. Forcing one uniform shape is lossy in one direction or the other — better to retain the high-fidelity representation each data type warrants [1].

**Why the others are wrong:**
- **A:** Flattening structured financials into prose loses the comparability executives need.
- **B:** A single intermediate representation ends up too lossy — keep structure and news suffers, keep prose and tables suffer; the fix is type-aware rendering, not uniformity.
- **D:** Forcing prose-heavy news into a claim/evidence/source/confidence schema strips the narrative flow executives actually read.

**Key takeaway:** **Render each content type in its native form** (tables for numbers, prose for narrative) — don't homogenize.

**Sources:**
- [1] Anthropic — "Effective context engineering for AI agents" (preserve the highest-fidelity representation that fits the data, avoid lossy uniformity), https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

---

### Q12 — research_pipeline
**Question:** The coordinator gives the web search subagent exact queries, source priorities, and date filters. In production: (1) it reports "insufficient results" instead of trying alternatives, (2) quality drops for emerging topics, (3) it rarely surfaces valuable tangential sources. Most effective way to improve adaptability?

**Correct answer: D** — Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent choose its strategy.

**Why it's correct:** Delegate intent and quality bars, not procedures. Anthropic delegates an *objective*, output format, and tool/source guidance, then lets the subagent decide how to achieve it — research is inherently dynamic and path-dependent, so a one-shot procedural pipeline can't adapt [1][2]. With goals (not rigid steps) the subagent can reformulate queries, follow tangents, and recover from dead ends.

**Why the others are wrong:**
- **A:** "Research X thoroughly" goes too far the other way, dropping the guardrails (source quality, recency) that make delegation reliable [2].
- **B:** A fallback directive patches one failure mode but keeps the agent locked into procedural thinking — still brittle for emerging topics and tangents.
- **C:** A two-bucket classifier is fragile and still ships rigid instructions in the "well-defined" branch.

**Key takeaway:** Delegate **goals and quality criteria, not step-by-step procedures** — autonomy within clear guardrails beats brittle scripts.

**Sources:**
- [1] Anthropic — "How we built our multi-agent research system" (give subagents an objective + output format + tool/source guidance; research is dynamic, can't hardcode the path), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (agents direct their own process; orchestrator delegates outcomes, keeps guardrails), https://www.anthropic.com/engineering/building-effective-agents

---

### Q13 — research_pipeline
**Question:** Follow-up queries like "summarize what we learned about market trends" take 40+ s because the coordinator spawns the synthesis subagent each time, passing 80K+ tokens it already has in its own context. Most effective way to speed up these follow-ups?

**Correct answer: B** — Have the coordinator handle straightforward summarization directly from its existing context, reserving subagent spawning for complex analysis.

**Why it's correct:** If the coordinator already holds the findings, spawning a subagent to re-ingest 80K tokens is pure overhead. Multi-agent systems use roughly 15× the tokens of a chat, so spawning is justified only for tasks that need parallelization or scale beyond one context window — a simple summary is neither [1]. Use the simplest sufficient pattern [2].

**Why the others are wrong:**
- **A:** Pre-generating/caching summaries at multiple granularities is speculative token spend, and cached granularities rarely match what's asked.
- **C:** Prompt caching trims the repeated-prefix cost but the architectural waste — spawning a whole subagent for a summary — remains.
- **D:** Spawning with reduced context plus on-demand requests adds round-trips and complexity for a result the coordinator could produce directly.

**Key takeaway:** **Don't spawn a subagent to redo work the coordinator already has in context** — match the pattern's cost to the task's complexity.

**Sources:**
- [1] Anthropic — "How we built our multi-agent research system" (multi-agent uses ~15× tokens; reserve for high-value parallel/over-window tasks), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (use the simplest solution that works; add agentic complexity only when it pays off), https://www.anthropic.com/engineering/building-effective-agents

---

### Q14 — research_pipeline
**Question:** A landmark legal case citing 12 precedents takes 3+ minutes because the document analysis subagent processes them sequentially. Most effective way to reduce latency while preserving the coordinator's ability to monitor and debug?

**Correct answer: C** — Have the coordinator spawn parallel document-analysis subagents, each handling a subset of precedents, then aggregate before synthesis.

**Why it's correct:** Coordinator-managed parallelism is exactly how Anthropic's lead agent scales — it spawns subagents that "operate in parallel," each with its own context window, then synthesizes their condensed returns [1]. Fanning out from the coordinator keeps each subagent's scope tight and preserves a single hub for monitoring and aggregation [1][2].

**Why the others are wrong:**
- **A:** An external message queue complicates observability — the coordinator loses direct visibility into which tasks succeeded and what they produced.
- **B:** A recursive agent hierarchy adds layers of indirection that hurt debugging/monitoring for no real speedup beyond the first fan-out.
- **D:** Letting the subagent spawn its own nested subagents hides execution and makes the coordinator's debug view incomplete.

**Key takeaway:** Parallelize via **coordinator-spawned subagents (single hub)** — keep fan-out flat and observable; avoid nested/queued spawning.

**Sources:**
- [1] Anthropic — "How we built our multi-agent research system" (lead agent spawns parallel subagents with separate context windows, then synthesizes), https://www.anthropic.com/engineering/multi-agent-research-system
- [2] Anthropic — "Building effective agents" (orchestrator-workers; orchestrator stays the central coordinator), https://www.anthropic.com/engineering/building-effective-agents

---

### Q15 — research_pipeline
**Question:** The coordinator has correct `AgentDefinitions` for all four subagents and reasons aloud about delegating ("I'll ask the web search agent…"), but no subagent ever executes, it proceeds with incomplete info, and logs show no errors. Most likely cause?

**Correct answer: C** — The coordinator's `allowedTools` doesn't include `Task`, so it can reason about delegating but can't invoke the tool that spawns subagents.

**Why it's correct:** Subagents are spawned via the `Task` tool; if `Task` isn't in `allowedTools`, the model can narrate intent but has no callable mechanism to act on it — producing the exact "reasons about it, no execution, no errors" symptom. A tool the model can't call simply never appears as an executable `tool_use`, so nothing runs and nothing errors [1].

**Why the others are wrong:**
- **A:** A `max_tokens` truncation would show in logs and leave partial `tool_use` blocks, not a silent no-op.
- **B:** Tool/agent schemas are surfaced to the model automatically from the tool definitions — you don't re-list them in the system prompt for the model to "see" them [1].
- **D:** Context isolation is real but governs what a subagent sees *once spawned* — not whether the coordinator can spawn it at all.

**Key takeaway:** An agent can only *do* what its **allowed tools** permit — to delegate, the coordinator must have the `Task` (subagent-spawn) tool enabled.

**Sources:**
- [1] Claude API Docs — "Define tools / Implement tool use" (the API builds the tool system prompt from tool definitions; only enabled tools are callable; forced/unavailable tools change emitted blocks), https://docs.claude.com/en/docs/agents-and-tools/tool-use/implement-tool-use
