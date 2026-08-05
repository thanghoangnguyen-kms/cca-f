---
tags:
  - CCA-F
  - domain-4
  - batch-processing
  - code-review
  - multi-instance
  - youtube-course
  - flashcards
date: 2026-08-05
status: done
domain: "4 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 17"
---

# 🃏 EP17 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP17 — Batch API & Multi-Pass Review** (Domain 4, task statements 4.5 and 4.6). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> **One card corrects the lecture's central claim:** it says batch is "single-turn only, no multi-turn tool calling." Official docs list tool use and multi-turn conversations as fully batchable, and [[D4 - Prompt Engineering & Structured Output]] § 4.5 already records this. Learn the corrected version — the lecture's *conclusion* (don't batch an agentic loop) is right, its *reason* is wrong.
> Cards marked **(docs)** carry material the lecture never reaches: out-of-order results, the four result types and billing, batch limits, `expired`, selective resubmission, sample-set refinement, SLA arithmetic, attention dilution, and the `confidence` field.

**Related:** [[EP17 - Batch API & Multi-Pass Review]] · [[D4 - Prompt Engineering & Structured Output]] · [[EP13 - Claude Code CI-CD Pipelines]] · [[EP16 - Structured Output & JSON Schema]] · [[EP18 - Why AI Agents Forget (Context Engineering)]]

---

## Domain 4 — The Cost/Latency Trade

**Q: What are the Message Batches API's three defining characteristics?**
A: **50% of standard pricing**, a processing window of up to **24 hours** (results when all complete or at 24h, whichever comes first), and **no latency SLA** — no guaranteed delivery time inside that window.

**Q: The batch discount isn't a cheaper or weaker model. What are you actually trading away?**
A: **Scheduling flexibility.** Same model, same output quality — Anthropic processes the work when spare capacity exists. Immediate delivery is the premium feature; surrendering control over *when* is what earns the 50%.

**Q: Before asking "how much does this cost," what question decides whether a workload is overpaying?**
A: **"Is anyone actually waiting for this result?"** Latency you don't need is latency you're paying a premium for. A nightly test-generation run nobody reads until morning is paying full price for speed with no consumer.

**Q: "Up to 24 hours" — is that the expected completion time for a batch? (docs)**
A: **No — it's the ceiling and the expiry boundary.** Most batches complete in **under 1 hour**. Use 24h for SLA math and capacity design; expect under an hour operationally, so a batch still running at hour six is a signal.

**Q: Under heavy demand, what changes about batch behaviour beyond it being slower? (docs)**
A: More requests **expire** at the 24-hour mark. The ceiling isn't only a latency bound — under contention it becomes a **partial-failure** bound, so code that assumes N requests yield N results will eventually break.

---

## Domain 4 — What Batch Can and Cannot Do

**Q: The lecture says batch supports "single-turn only, no multi-turn tool calling." Is that correct?**
A: **No.** Docs list **tool use (including all server tools)** and **multi-turn conversations** as batchable — *"almost any request you can make to the Messages API."* [[D4 - Prompt Engineering & Structured Output]] § 4.5 records the same. Learn the corrected version.

**Q: Which Messages API parameters are actually rejected inside a batch request? (docs)**
A: `stream: true`, `speed` (fast mode), `store` / `previous_thread_event_id` (threads), `cache_hint` / `context_hint`, `max_tokens: 0`, and `research_preview_2026_02`. Tool use is **not** among them.

**Q: If batch supports tool use, why can't you run an agentic loop with client-side tools as a batch?**
A: Because batch is **fire-and-forget**: you submit, and one request yields exactly one result with no way to inject anything mid-flight. A client-executed tool needs *you* to return a `tool_result` in a **new** request. The loop needs a participant; batch has none.

**Q: What's the difference between a server-side and a client-executed tool with respect to batching?**
A: A **server-side** tool (web search, code execution) runs inside the server's own loop during the request — fully batchable. A **client-executed** tool requires a round trip through your code, which batch's submit-and-forget model can't provide.

**Q: Is streaming supported in batch, and what's the stated reason? (docs)**
A: **No.** *"Batch results come back as a single file, not a stream."* The Python SDK encodes it in the type name — batch params are `MessageCreateParamsNonStreaming`.

**Q: Name one capability the Batch API has that the synchronous Messages API lacks. (docs)**
A: **Extended output** — available on the Message Batches API only. Batch isn't strictly a weaker subset of sync; the relationship isn't purely subtractive.

**Q: Restate the lecture's second decision question so it's correct.**
A: Not *"does this need multi-turn tool calling?"* (batch supports that) but **"does completing this require me to respond mid-flight?"** — chained pipeline steps consuming a prior step's output, or client-side tool round trips.

---

## Domain 4 — The Decision Rule

**Q: State both questions of the batch decision rule and how they resolve.**
A: (1) **Is anyone waiting for the result right now?** (2) **Does this require me to respond mid-flight?** Either "yes" → standard API. Both "no" → batch.

**Q: Reviews block PR merge. A developer proposes batch to cut cost. What's the primary concern?**
A: **No guaranteed delivery time on a blocking gate.** The batch may return in 2 minutes or 23 hours 59 minutes; the CI run times out long before. Cost savings never redeem a broken merge gate.

**Q: Why can't a chained CI/CD pipeline use batch even when nobody is personally waiting?**
A: Because one step's output feeds the next step's input — the *pipeline* is the thing blocked. A GitHub Actions run can't sit idle for up to 24 hours waiting on an intermediate result.

**Q: Give three workloads that are canonical batch fits.**
A: Overnight code-review reports, full-codebase security audits, and nightly test generation. Also bulk document work like processing 500 invoices on a Sunday. The shape: each unit **independent**, nobody **blocked**.

**Q: A stem says "extracts sentiment from 2,000 feedback forms weekly in real time," then mentions a nightly batch, independent forms, and no tools. Batch or standard?**
A: **Batch.** "Real time" is a decoy describing how forms are *collected*, not when they must be *processed*. The decisive signals are *nightly*, *independent*, and *no tools* — non-blocking and single-turn.

**Q: What general reading habit does the "real time" decoy teach for batch questions?**
A: Read **what the phrase attaches to**, not merely whether it appears. Latency words can describe collection, ingestion, or display rather than the processing step the question is actually about.

---

## Domain 4 — custom_id and Correlation

**Q: Why is `custom_id` mandatory rather than merely convenient? (docs)**
A: Because **batch results can be returned in any order** and may not match submission order. There is no position to match on, so zipping a results array against a requests array is broken — and *intermittently* broken, which is worse.

**Q: At what granularity does `custom_id` apply?**
A: **One per request**, unique within the batch. The batch itself already has a server-assigned ID (`msgbatch_…`) for polling; `custom_id` exists at request level so you can demultiplex the results file.

**Q: What is `custom_id`'s required format? (docs)**
A: 1–64 characters, alphanumerics plus hyphens and underscores — `^[a-zA-Z0-9_-]{1,64}$`. Required on every request in the batch.

**Q: The lecture says don't use random UUIDs as `custom_id` because results "might get lost." Is that reasoning right?**
A: **No.** A UUID correlates perfectly if you persist the mapping — correctness isn't the issue. The real argument is **operability**: `pr-4127-auth-ts-v3` is debuggable at 3am; `f47ac10b-58cc…` needs a database round trip to interpret.

**Q: What makes a good `custom_id` in a code-review batch?**
A: Something unique **and** meaningful — PR number plus file path plus version, e.g. `pr-4127-src-auth-service-ts-v3`. Uniqueness is required; meaningfulness is what makes the results file readable.

**Q: An exam question asks how to correlate batch requests with their responses. What's the answer?**
A: **`custom_id`** — always. It's the only correlation mechanism, since order isn't guaranteed.

---

## Domain 4 — Result Types, Limits, and Recovery

**Q: A batch reports that processing has ended. Does that mean every request succeeded? (docs)**
A: **No.** Each request ends as `succeeded`, `errored`, `canceled`, or `expired`. "Processing ended" only means no request is still pending — `request_counts` gives the tally across all four.

**Q: Name the four batch result types and which are billed. (docs)**
A: `succeeded` — **billed**. `errored`, `canceled`, `expired` — **not billed**. A batch that half-expires costs only what actually ran.

**Q: What does the `expired` result type mean? (docs)**
A: The **24-hour window elapsed** before that request reached the model. It's the failure mode unique to batch, and it becomes more common under high demand or high request volume.

**Q: What are the size limits on a single batch? (docs)**
A: **100,000 requests or 256 MB**, whichever is reached first.

**Q: How long do batch results remain downloadable? (docs)**
A: **29 days** after batch creation. After that you can still view the batch, but results are no longer retrievable — so persist anything you need long-term.

**Q: Which `max_tokens` value is rejected inside a batch, and why? (docs)**
A: **`max_tokens: 0`** (the cache pre-warming trick). An ephemeral cache entry written during batch processing would likely expire before any follow-up request runs, so every batched request needs `max_tokens` ≥ 1.

**Q: Does prompt caching work with batch? (docs)**
A: Yes, but **best-effort only** — because requests are processed concurrently and in any order, cache hits aren't guaranteed. Don't build cost projections that assume them.

**Q: 6,000 of 20,000 batch requests failed. What do you resubmit?**
A: **Only the failed requests**, identified by `custom_id` — never the whole batch. If a request failed on a context limit, chunk that document before resubmitting.

**Q: How does out-of-order delivery connect to failure recovery?**
A: `custom_id` is what makes selective resubmission possible at all. Without a correlation key you couldn't tell *which* requests failed, so your only recovery would be resubmitting everything — paying twice for the 70% that worked.

---

## Domain 4 — Designing for One Attempt

**Q: You can't refine a prompt mid-batch. What's the design consequence? (docs)**
A: **Refine synchronously on a small sample first**, then submit the large batch. You get exactly one attempt per request, so first-pass success rate is the variable to optimize before you commit volume.

**Q: Why is a 50% discount on a batch you have to rerun worse than paying full price once?**
A: Because $0.5 + 0.5 = 1.0$ of the original cost for the redone portion, on top of the first run — a rerun batch is a **25% premium** over getting it right once synchronously, and it costs a second 24-hour window too.

**Q: Compliance requires results within 30 hours and you want batch pricing. How often must you submit? (docs)**
A: Every **6 hours** — $\text{SLA} - 24\text{h} = 30 - 24$. Worst case is an item arriving just after a cutoff, so the cadence interval *is* the maximum pre-processing wait.

**Q: Why is sizing a batch cadence against the ~1-hour typical completion time a trap?**
A: Because it passes on a quiet day and breaches under load. A 30-hour SLA with a daily cadence plus a full 24-hour run delivers at 48 hours — and that slow case coincides with elevated expiry risk.

**Q: What's the general rule for batch submission cadence?**
A: **Cadence = SLA − 24 hours**, subtracting the guaranteed ceiling, never the expected completion time. Only the 24-hour figure is committed to.

---

## Domain 4 — The Self-Review Trap

**Q: Why does the instance that wrote the code make a poor reviewer of it?**
A: It **retains its original conversation history and reasoning context**, where that reasoning reads as justification. It doesn't just remember its decisions — it remembers why they were right.

**Q: What class of defect does self-review structurally miss?**
A: **Unquestioned assumptions.** The lecture's example: the code accepts a negative birth date because the author's model of the problem never included that input — and that model is exactly what persists in session context.

**Q: Name the two non-solutions and the one solution to self-review bias.**
A: ❌ "Review your own work carefully" instructions. ❌ **Extended thinking.** ✅ An **independent review instance** in a fresh session with no prior reasoning context.

**Q: Why is extended thinking the subtler wrong answer for self-review bias?**
A: Because more reasoning over the same premises produces a **more thoroughly argued blind spot**, not a corrected one. Depth of reasoning isn't the missing ingredient — independence of context is.

**Q: What does the developer/QA analogy explain about review architecture?**
A: A QA catches what the author can't because they arrive without the author's model of the problem — they'll ask whether a birth date can be negative. Separate roles exist precisely because independence, not extra care, is what surfaces assumptions.

---

## Domain 4 — Independent Review Instances

**Q: Instance A wrote the code. What exactly crosses to reviewing instance B?**
A: **The artifact — the file.** Not A's conversation, justifications, or framing of the problem. B's value *is* its ignorance of how the code came to be.

**Q: Why does reducing the reviewer's ignorance reduce the review's value?**
A: Because the ignorance is the mechanism. Every piece of the generator's reasoning you import re-supplies the framing that produced the blind spot, moving the reviewer back toward self-review.

**Q: You seed a fresh reviewer with a summary to save cost. Which summaries are safe? (docs)**
A: **Safe:** the spec, requirements, ticket, acceptance criteria, and the artifact — all things that exist independently of the generator's session. **Unsafe:** the generator's narrative or self-assessment of its own work.

**Q: Why does a generator-written summary defeat independent review?**
A: It smuggles the generator's bias in through a side channel. A summary asserting *"handles all date edge cases correctly"* pre-answers the exact question the independent reviewer existed to ask.

**Q: What's the cheapest correct brief for an independent reviewer?**
A: The one a human QA gets: here's what it should do (spec/acceptance criteria), here's what was built (the artifact), go. You avoid paying the reviewer to rediscover requirements without paying it to inherit an opinion.

---

## Domain 4 — Multi-Pass Review Architecture

**Q: In multi-pass review, which pass gives depth and which gives breadth?**
A: **Per-file = depth** (deep into one unit); **cross-file integration = breadth** (spanning many). The lecture waffles on this out loud — its own section title and sample answer both give this mapping.

**Q: One instance reviews a 40-file PR and returns shallow feedback. Why won't "be more thorough" fix it? (docs)**
A: Because it's **attention dilution**, not disobedience. A context holding 40 files has finite attention to distribute, yielding shallow and contradictory findings. Splitting creates room that no instruction can.

**Q: What does pass 1 of a multi-pass review consist of?**
A: **Parallel independent instances, one per file**, each with a fresh context, focusing deeply on style, bugs, and security within that file.

**Q: What does pass 2 do that pass 1 structurally cannot?**
A: It reviews the **collated findings across files** for contracts between them — data flow, interface consistency, dependency issues. Those bugs live in no single file, so no per-file reviewer can see them.

**Q: Give the lecture's concrete example of a bug only the cross-file pass catches.**
A: Auth and cart services each pass their own unit tests, but only an integration view catches that an **unauthenticated user can add products to the cart**. The defect is in the contract between the services.

**Q: Why is the architect's PR-review habit a good analogy for multi-pass?**
A: An architect first checks whether individual files are correct, then whether the feature integrates as a whole. Same two passes, same order — depth per unit, then breadth across units.

**Q: What should each finding carry to enable calibrated review routing? (docs)**
A: A **`confidence`** score (plus brief `reasoning`), so **low-confidence findings route to a human first**. It also gives pass 2 something to rank and dedupe on, since pass 1's N instances share no severity bar.

**Q: Why does a multi-pass design especially need per-finding confidence scores? (docs)**
A: Because N independent per-file instances produce findings with no common bar, and volume alone isn't actionable. Confidence gives the integration pass a ranking signal and gives humans a triage lever better than "read all 200."

---

## Domain 4 — Putting It Together

**Q: In the two-pipeline architecture, which pipeline uses which API and why?**
A: **PR pipeline → standard** (the merge gate blocks, and steps consume prior steps' output). **Nightly pipeline → batch** (test generation, security audit, docs — nobody waits, 50% savings).

**Q: What stays constant across both the PR and nightly pipelines?**
A: **Every review instance is independent — no self-review anywhere.** Independence doesn't depend on which API you chose.

**Q: Why are "batch vs standard" and "independent instances" orthogonal decisions?**
A: Batch/standard is a **latency and cost** decision; independent instances is a **correctness** decision. A nightly batch audit still needs fresh reviewer sessions, and a blocking gate still can't self-review.

**Q: The PR pipeline "includes prior review findings in the new prompts." Why does that force standard messaging?**
A: Because a step consumes a previous step's output — the chained-dependency case. Deduplicating against earlier findings requires having them in hand, which an unbounded-within-24h batch can't guarantee mid-run.

**Q: Which episode covers the CI/CD integration this one builds on?**
A: **[[EP13 - Claude Code CI-CD Pipelines]]** — the host guesses "episode 11 or 12" on air, which is wrong.

**Q: What do the batch trade and the review trade have in common as failure modes?**
A: Both are **architectural, not instructional**. You can't instruct a blocking pipeline into tolerating a 24-hour window, and you can't instruct a biased context into objectivity. You restructure, or the problem stands.

---

*Back to: [[EP17 - Batch API & Multi-Pass Review]]*
