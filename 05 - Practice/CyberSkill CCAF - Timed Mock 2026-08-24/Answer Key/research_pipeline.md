---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - research-pipeline
date: 2026-08-24
status: done
---

# Timed Mock 2026-08-24 — `research_pipeline` Answer Key

**15 answers** — Q8, 15, 21, 30, 32, 35, 44, 46, 47, 48, 49, 50, 51, 53, 54. Scored **10/15 (67%)**.

Question numbers are this sitting's own and interleave with the other domains; they match [../Questions.md](../Questions.md). Back to [../README.md](../README.md). Every entry now reproduces its stem verbatim from [../Questions.md](../Questions.md) above the answer, so you can read this file without switching.

> [!NOTE] Every answer here is grader-authoritative
> These are the site's own `correct_key` values as rendered on the review page. Where an item was answered correctly, the correct answer *is* the recorded selection. Distractors were not recoverable — see [../README.md](../README.md) § Fidelity.

---

## ❌ Missed — work these first

All five misses in this domain are the **same mistake**. Read them as one lesson, not five.

### Q21 — Inconsistent handling of uncertainty in reports

> Production reviews reveal inconsistent handling of uncertainty in final reports. Sometimes conflicting subagent findings are synthesized into a single confident statement (losing nuance), while other times reports over-hedge with excessive qualifications (becoming unhelpful). When the web search agent returns "industry analysts estimate $50B market size (methodology varies)" and the document analysis agent returns "peer-reviewed study estimates $35B (±$7B, 95% CI)," the coordinator either picks one arbitrarily or produces vague statements like "the market may be $35B–$50B depending on factors."
>
> What systematic approach best addresses this?

- **Correct: C.** Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established findings from contested ones, preserving original source characterizations and methodological context.
- **You answered B.** "Implement a confidence calibration layer that normalizes subagent uncertainty expressions to standardized probability scores (0.0-1.0), then weight-average findings by their calibrated confidence."

**Why C wins.** The two inputs are not commensurable. "Analysts estimate $50B (methodology varies)" and "peer-reviewed, $35B ±$7B, 95% CI" differ in *kind* — one is an informal aggregate, the other a statistical interval. Collapsing both to a scalar in [0,1] throws away the very thing a reader needs to judge them, and weight-averaging produces a number that no source supports and no method justifies. C keeps the distinction visible and hands the reader the methodological context, which is what "handling uncertainty" actually means.

> [!WARNING] The trap: numeric machinery reads as rigor
> "Calibration layer", "standardized probability scores", "weight-average" all sound more engineered than "structure the report into sections". They are more engineered — and wrong, because the engineering destroys information. Ask what the pipeline is *for*: an auditable research report, not a point estimate.
>
> **Status of this reasoning:** sound, but *not* documented Anthropic doctrine — no official page addresses scalar normalization of heterogeneous confidence. Official guidance does support preserving source grounding and letting the model express uncertainty. Treat the argument as reasoning you can defend, not a rule you can cite.

---

### Q47 — 2024 vs 2022 figures flagged as contradictory

> When researching "renewable energy adoption," the web search agent returns recent statistics (2024: 35% adoption) while the document analysis agent extracts data from internal reports (2022: 18% adoption). The synthesis agent incorrectly flags these as contradictory sources rather than recognizing the data shows growth over time.
>
> What change would best enable the synthesis agent to correctly interpret such temporal differences?

- **Correct: A.** Require subagents to include publication or data collection dates in their structured outputs.
- **You answered D.** "Instruct the synthesis agent to always treat the most recent data as authoritative and place older findings in a separate historical appendix."

**Why A wins.** The synthesis agent isn't misjudging the data — it **cannot see the dates**, so 35% and 18% look like a straight contradiction. Fix the missing field, and the apparent conflict resolves itself into a growth trend. D papers over the symptom while making the output worse: exiling the 2022 figure to an appendix destroys the trend, which was the actual finding.

> [!IMPORTANT] When an agent reasons wrongly, first ask what it wasn't told
> A synthesis error that traces to absent metadata is fixed **upstream, in the subagent's output contract** — not by adding a synthesis rule that compensates for the gap. This is the single highest-yield pattern in this domain.

---

### Q49 — Citations lost during summarization

> The synthesis agent receives summarized findings from the web search and document analysis agents, then passes a consolidated summary to the report generator. During testing, you discover the generated reports make factual claims without proper citations—the report generator cannot attribute statements to their original sources because that metadata was lost during the summarization steps.
>
> What's the most effective approach to ensure proper source attribution in the final reports?

- **Correct: A.** Have each agent output structured data separating content summaries from source metadata (URLs, document names, page numbers).
- **You answered C.** "Instruct the synthesis agent to embed source references inline within its summary text using a consistent citation format."

**Why A wins.** Metadata that lives *inside prose* is destroyed by the next summarization step — which is precisely the failure the stem describes. Separating content from source metadata into distinct structured fields means provenance survives every downstream transformation, because it is never something a summarizer can paraphrase away.

> [!WARNING] You got the identical question right earlier in the same sitting
> **Q15** — same failure, citations lost in synthesis — and you correctly chose *structured claim-source mappings*. Thirty-four questions later you chose inline prose citations for the same problem. The rule you already know: **provenance is a field, never a sentence.**

---

### Q50 — Over-specified subagent instructions

> The coordinator provides detailed step-by-step instructions to the web search subagent, specifying exact search queries, source priorities, and date filters. Production monitoring reveals three issues: (1) the subagent reports "insufficient results" rather than trying alternative approaches when pre-specified searches fail, (2) research quality drops for emerging topics that don't match expected patterns, and (3) the subagent rarely surfaces valuable tangential sources.
>
> What's the most effective way to improve subagent adaptability?

- **Correct: D.** Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy.
- **You answered C.** "Implement a topic classification step where the coordinator categorizes requests as 'well-defined' or 'exploratory' and uses different instruction styles for each category."

**Why D wins.** All three symptoms — giving up instead of adapting, degrading on unfamiliar topics, missing tangential sources — are consequences of one cause: the subagent was handed a **procedure** instead of a **goal**, so it has no basis for improvising when the procedure fails. D removes the cause. C adds a classifier on top and keeps procedural instructions for half the traffic, so half the failures remain — and it introduces a new component that can itself misclassify.

**Delegate goals and quality criteria, never procedures** — core orchestrator-worker doctrine, and it recurs constantly. A subagent given a goal can adapt; one given a script can only report failure.

---

### Q54 — Heterogeneous subagent outputs flattened to bullets

> A user is expanding the research system beyond its single web search agent by adding specialized data sources. They add a financial API agent that returns structured JSON with revenue, margins, and growth rates; a news monitoring agent that returns prose summaries of recent developments; and a patent analysis agent that returns structured lists of technology areas. The synthesis agent combines these into executive briefings. Currently, it converts everything to bullet points, causing financial comparisons to lose tabular clarity and news summaries to lose narrative flow.
>
> What change would most improve briefing quality?

- **Correct: C.** Update the synthesis agent to render each content type appropriately—financial data as tables, news as prose.
- **You answered B.** "Add a format conversion layer between subagents and synthesis that transforms all outputs to a common intermediate representation."

**Why C wins.** The stated problem is that a uniform representation is **already** destroying tabular clarity and narrative flow. B's answer to "uniformity is losing information" is a second, earlier layer of uniformity — it entrenches the bug and adds a component. C fixes it where the loss occurs: the renderer should be type-aware, because the content types genuinely differ.

> [!WARNING] Q21 and Q54 are the same trap in different clothes
> Both offer a **normalization layer** that converts heterogeneous inputs into one shape. Both are wrong, for the same reason: **the differences carry information**. Normalize only when the variation is noise (formatting, units, casing). Never when it is signal (methodology, confidence kind, content type).

---

## ✅ Answered correctly

The option shown is the keyed correct answer.

### Q8 — Coordinator reasons about delegating but never spawns

> The coordinator agent has `AgentDefinition`s configured for all four specialized subagents, each with appropriate descriptions, prompts, and tool restrictions. During testing, you notice the coordinator correctly reasons about when to delegate—it generates messages like "I'll ask the web search agent to find sources on this topic"—but no subagent execution ever occurs. The coordinator then proceeds as if the delegation happened and continues with incomplete information. Logs show no errors.
>
> What is the most likely cause?

**C.** The coordinator's `allowedTools` configuration doesn't include "`Task`", so while it can reason about delegation, it cannot invoke the tool required to spawn subagents.
*Takeaway: the ability to spawn a subagent is a **tool permission**, not a config property of `AgentDefinition`. Reasoning about delegation with no error in the logs = the spawn tool was never grantable.*

> [!WARNING] This option's terminology is wrong, and its failure mode is the *other* one — verified against official docs
> **Field name.** The keyed answer says `allowedTools`. The `AgentDefinition` inner field is **`tools`**; `allowedTools` is CLI/SDK permission vocabulary, not an `AgentDefinition` field. The vault records this in [[D1 - Agentic Architecture & Orchestration]] and `AGENTS.md` § Known corrections.
>
> **Which omission is silent.** This matters more than the name. Omitting `Agent` from the top-level `allowedTools` does **not** fail silently — the call routes to your `canUseTool` callback, or is denied in `dontAsk` mode. Either way it surfaces. The genuinely silent failure is omission from **`AgentDefinition.tools`**: a tool left out isn't in the subagent's session at all, so Claude simply works without it, with no prompt and no error. The stem's *"logs show no errors"* fits that mechanism, not the `allowedTools` one. [[Answer Patterns Index]] § Tier 3 already draws this distinction correctly.
>
> **The rename.** `Task` → `Agent` in Claude Code v2.1.63, and the alias is narrower than "still valid" suggests: the current tools reference lists only `Agent`; `Task` survives in the SDK's `system:init` tools list and in `result.permission_denials[].tool_name`. `Task` remains the exam-safe answer. Beware a name collision — `TaskCreate`/`TaskGet`/`TaskList`/`TaskUpdate` in the tools reference are **task-list** tools, unrelated to spawning.
>
> Source: <https://code.claude.com/docs/en/agent-sdk/subagents>

### Q15 — Synthesis loses which source supports which claim

> In production, final reports frequently contain claims without proper source attribution. Investigation shows that while the web search and document analysis agents correctly attach citations to their outputs, the synthesis agent loses track of which sources support which conclusions when combining findings.
>
> What's the most effective architectural change?

**B.** Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings from multiple sources.
*Takeaway: provenance travels as structure. Compare **Q49**, where you answered the same question differently.*

### Q30 — Two subagents, conflicting figures, moderate confidence

> Two sub-agents return conflicting figures for the same metric, each with moderate confidence. Before the coordinator writes the final answer, the best move is to:

**C.** Run a focused check that re-fetches the metric from the primary source and resolves the conflict before synthesizing.
*Takeaway: resolve conflicts by going back to the primary source, not by averaging or picking. Compare **Q21**: resolve where you can, expose the disagreement where you can't.*

### Q32 — How information flows between two subagents

> The web search agent has gathered several relevant sources for a research topic. The document analysis agent now needs to examine these sources.
>
> How does information typically flow between these two specialized subagents?

**C.** The coordinator agent receives the web search agent's output and includes relevant findings in the prompt when invoking the document analysis agent.
*Takeaway: subagents are context-isolated, so findings reach a sibling only by the coordinator putting them in that sibling's prompt.*

> [!WARNING] "Subagents can never talk directly" is overstated — and the vault says so elsewhere too
> A subagent granted the **`SendMessage`** tool starts with a list of the other named agents in the session and can message them directly. So hub-and-spoke is a **design principle and the exam answer**, not an SDK constraint — exactly the same shape as the nesting correction `AGENTS.md` already records. C is still right here: nothing in the stem grants `SendMessage`, and routing through the coordinator is what keeps context isolated and auditable.
> Source: <https://code.claude.com/docs/en/agent-sdk/subagents>

### Q35 — Research run not converging

> A research agent keeps spawning follow-up searches and the run is not converging. The most reliable way to prevent an endless loop is to:

**B.** Give the task an explicit budget and a coverage check, and stop once the questions are answered or the budget is spent.
*Takeaway: termination needs both a **budget** (hard stop) and a **coverage check** (goal met). One alone either loops or stops early.*

### Q44 — Eight independent sources, one synthesis

> A research agent must gather facts from eight independent web sources and produce one synthesis. None of the sources depend on each other. Which dispatch pattern stays fast without flooding the coordinator context?

**B.** Dispatch eight sub-agents in parallel, each returning a short structured summary with citations, then synthesize from the summaries.
*Takeaway: independent work fans out in parallel; each worker returns a **compact structured** result so the coordinator's context survives the fan-in.*

### Q46 — Generic `analyze_document` tool, 35% re-request rate

> The document analysis agent has a single `analyze_document` tool that takes a document and a free-text instruction parameter. During evaluation, requests like "extract the key financial metrics" often return narrative summaries, while "summarize the methodology" sometimes returns raw data tables. The synthesis agent reports that 35% of analysis results require re-requests with clarified instructions.
>
> What's the most effective way to improve reliability?

**A.** Split the generic tool into purpose-specific tools—`extract_data_points`, `summarize_content`, `verify_claim_against_source`—each with defined input/output contracts.
*Takeaway: a free-text instruction parameter is an unspecified contract. Ambiguous output → **fix the I/O contract**.*

> [!WARNING] Don't generalise this to "always split tools" — current docs push the other way
> Official tool-design guidance now says to **consolidate** related operations into fewer, more capable tools, grouping them under an `action` parameter, because fewer tools reduce selection ambiguity. A is still right here because the defect is the **unspecified free-text contract**, not the tool count — three tools with defined I/O beat one tool with an open instruction field. But the portable rule is *specify the contract*, not *increase the tool count*. A single `analyze_document(document, action: enum, …)` with typed outputs per action would also have fixed this.
> Source: <https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools>

### Q48 — Three subagents, duplicate findings

> Three sub-agents searched overlapping territory and several findings repeat across their reports. Before synthesis, the coordinator should:

**C.** Merge the reports, collapse duplicate findings, and keep one cited instance of each.
*Takeaway: dedupe before synthesis, and keep the citation on the surviving copy.*

### Q51 — Pipeline crashed at 12 of 28 documents

> Your multi-agent research pipeline crashed after processing 12 of 28 documents. The web search agent had identified relevant sources, the document analysis agent had partially completed extraction, and the synthesizer had begun pattern identification. You need to resume processing without repeating work or losing fidelity of prior findings.
>
> What state management approach best balances information fidelity with context efficiency when restoring agent state?

**C.** Have each agent persist a structured report to a known location. On resume, the coordinator loads the reports and injects relevant state into agent prompts.
*Takeaway: durable structured artifacts at known paths are the resumable unit — not transcripts, not in-memory state.*

### Q53 — What each finding must travel with

> You are designing how sub-agents report findings so the final research output can be audited later. Each finding should travel with:

**B.** The claim plus a reference to its source (URL or document id and location).
*Takeaway: claim + locator. Auditability needs the location, not just the document.*

---

## Domain pattern summary

| Rule | Items |
|---|---|
| **Fix the output contract upstream, not the synthesizer downstream** | Q47, Q49, Q15 |
| **Never normalize away differences that carry information** | Q21, Q54 |
| Provenance is a structured field, never inline prose | Q15, Q49, Q53 |
| Delegate goals + quality criteria, never procedures | Q50 |
| Coordinator is the sole hub; subagents are context-isolated | Q32 |
| Spawning requires the spawn tool to be permitted | Q8 |
| Parallel fan-out + compact structured fan-in | Q44 |
| Conflicts → re-fetch the primary source; expose what can't be resolved | Q30, Q21 |
| Dedupe before synthesis, keep one cited instance | Q48 |
| Termination = budget **and** coverage check | Q35 |
| Narrow tools with defined I/O beat one free-text tool | Q46 |
| Resume from durable structured artifacts | Q51 |

**Related:** [[Weak Areas Deep Dive]] · [[Answer Patterns Index]] · [[D1 - Agentic Architecture & Orchestration]] · [[D5 - Context Management & Reliability]]
