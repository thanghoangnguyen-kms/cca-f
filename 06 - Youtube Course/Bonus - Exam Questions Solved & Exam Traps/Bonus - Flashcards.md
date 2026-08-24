---
tags:
  - CCA-F
  - exam-strategy
  - exam-traps
  - practice-questions
  - distractors
  - flashcards
  - youtube-course
date: 2026-08-24
source: "Peace Of Code — Claude Certified Architect Bonus Episode"
status: done
---

# 🃏 Bonus Flashcards — Exam Questions Solved & Exam Traps

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[Bonus - Exam Questions Solved & Exam Traps]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> This is the **strategy** deck: most cards test *how to pick an option*, not what a term means. If you can name the trap but not spot it in a stem, the card has not landed yet.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]] · [[EP20 - When AI Needs a Human]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Cross-Domain — The Four Distractor Traps

**Q: Name the four distractor traps the exam reuses.**
A: **(1) Prompt Bandaid** — prompt improvement offered where a guarantee is required. **(2) Over-Engineered First Step** — the complete solution offered to a "most effective first step" question. **(3) Premature Infrastructure** — new classifier/router/microservice/subagent before the cheap optimization. **(4) Sentiment as Signal** — self-reported confidence used as a control-flow input.

**Q: State the one-line filter for breaking a tie between two plausible options, and the three stem keywords that trigger it.**
A: *"Does this guarantee the outcome, or just make it more likely?"* Triggered by **must**, **never**, or **guarantee**. When present, the guaranteeing option wins — even if the probabilistic one is better written.

**Q: The four traps collapse into only two underlying filters. What are they, and which traps pair up?**
A: **Guarantee vs probability** catches Trap 1 (Prompt Bandaid) and Trap 4 (Sentiment as Signal) — both offer a probabilistic mechanism where an invariant is required. **Is a cheaper cause-matched fix available** catches Trap 2 and Trap 3 — both reach past an available cheap fix. Learning the guarantee filter disposes of two traps at once.

**Q: An option proposes a well-crafted few-shot example plus explicit system-prompt wording, against a stem that says a rule "must never be violated." Why is it eliminated without assessing its quality?**
A: Prompt instructions are **probabilistic** — they raise the likelihood of a behaviour but cannot exclude its complement. "Never" demands an invariant, and no amount of wording craft converts a probabilistic mechanism into an enforced one. The elimination is a category judgement, not a quality judgement.

**Q: Why is the shorthand "guarantee → use a hook" too narrow, and what is the correct formulation?**
A: The invariant is **deterministic code that runs regardless of what the model decides** — a hook is only the most common instance. Two guarantee-shaped answers in this same episode are *not* hooks: **tool scoping** (remove the tool so no reasoning path reaches it) and a **nullable schema field plus human-review route**. Ask *which option removes the model's discretion*, not *which option is a hook*.

**Q: Traps 2 and 3 both punish reaching past a cheap fix. What distinguishes their tells?**
A: **Trap 2 is about scope** — the stem says "most effective first step," so the *complete* solution violates the stated constraint. **Trap 3 is about sequence** — new infrastructure precedes existing optimization, so the new component inherits the unfixed cause. A router built on one-sentence tool descriptions routes just as badly.

**Q: An option offers the cheapest possible fix in a "most effective first step" question. Is cheapness sufficient to select it?**
A: **No.** Cheapness is a tie-breaker *among cause-matched options only*. A cheap fix aimed at the wrong root cause is still wrong. Diagnose the cause from the stem first, then prefer the cheapest option addressing it.

**Q: Why does averaging two independent confidence scores fail as a fix?**
A: Confidence is **uncalibrated against correctness** — a model can report 99% on a hallucination. Averaging two miscalibrated signals produces a smoother miscalibrated signal, not a calibrated one. The defect is categorical, which is why *every* variant fails together: raising, lowering, averaging, or awaiting a better model.

**Q: What class of fix always replaces a confidence threshold, and what makes it different in kind?**
A: **Observable criteria** — conditions checkable from outside the model (customer explicitly requests a human; case falls in a known policy gap; no progress after N defined attempts). They are externally verifiable, so they do not inherit the model's miscalibration.

**Q: What is a "fabricated specific," and which three appear as distractors in this episode?**
A: A confidently-named flag, env var, or parameter that **does not exist** — a recurring distractor shape. In this episode: **`CLAUDE_HEADLESS`**, **`--batch`**, and the batch API "higher priority flag." All three are verified inventions.

---

## Cross-Domain — Diagnosis Method and Exam Tactics

**Q: The exam gives you four options that are all technically true statements about Claude. What are you actually searching for?**
A: The one option whose **mechanism matches the failure the stem describes**. Being true, on-domain, and good practice is not sufficient — in this episode "add more detailed tool descriptions" is the correct answer to one question and the distractor in its twin.

**Q: In what order should you process an exam question, per the playbook?**
A: **Symptom → root cause → fix.** Read the setup twice, name the symptom, locate the root cause *in the stem* (it is usually stated outright), and only then evaluate options. Diagnosing after reading the options invites anchoring on a plausible-sounding one.

**Q: What are the three stem clauses from this episode that name the root cause outright?**
A: *"All four tool definitions use single sentence descriptions"* → description quality. *"...has access to all system tools"* → tool scope. *"...with no predefined service boundaries"* → plan mode. The exam frequently hands you the diagnosis if you read for it.

**Q: Why is "flag it and move on" a scoring strategy rather than an admission of defeat?**
A: Because the pass mark is roughly **720 of 1000**, not 1000. Surrendering a few genuinely hard items costs little; letting one consume the time that would have banked several easy items costs a lot. *(Pass mark is an exam-logistics figure from the lecture — confirm against the official exam guide.)*

**Q: Domain 1 is what share of the exam, and which three domains sum to 67%?**
A: Domain 1 alone is **27%** — the largest single block. Domains **1, 3, and 4** total **67%**. Domain 2 is 18%, leaving Domain 5 at roughly 15%. *(Lecture figures — see the caveat in the study guide.)*

**Q: Revising only Domains 1, 3, and 4 gives 67% coverage. Why does that plan fail even executed perfectly?**
A: 100% on that trio and 0% elsewhere yields exactly **67%**, which is **below the 72% (720/1000) pass mark** — a 5-point shortfall. Since perfection is unattainable, real coverage of Domains 2 and 5 must exceed the $5/33 \approx 15.2\%$ floor by a wide margin. Weighting sets revision *order*, never omission.

**Q: The official sample questions leave two of the six exam scenarios untested. Which two, and why does that matter?**
A: **Scenario 4 (developer productivity)** and **scenario 6 (structured data extraction)**. The twelve official samples cover customer support, code generation, multi-agent research, and CI/CD only — so practising the samples alone leaves two scenarios completely unrehearsed, and both are in play on the real exam.

---

## Domain 1 — Gates, Decomposition, and Parallelism

**Q: An agent occasionally calls `process_refund` before `get_customer` returns a verified ID, and the stem asks how to *guarantee* the ordering. What is the fix, and why is `tool_choice: "any"` wrong?**
A: A **`PreToolUse` prerequisite gate in code** that blocks `process_refund` unless a verified `get_customer` result exists. `tool_choice: "any"` forces *a* tool call every turn — but the agent is already calling tools; the defect is their **order**, which `tool_choice` does not constrain.

**Q: You need to guarantee an agent never writes to production before a dry run succeeds. Why is a `PostToolUse` hook that logs violations the sharpest distractor?**
A: Because it fires **after** the write. It produces an audit trail of violations rather than preventing them, so it satisfies detection but not the guarantee the stem demands. Only a `PreToolUse` gate blocks the irreversible action before it happens.

**Q: A synthesized research report omits two topics, even though the source documents the subagents retrieved discuss both at length. What is the root cause, and why is that clause decisive?**
A: **The initial decomposition was too narrow, before any research began** — the topics were never *assigned*. The clause is decisive because it confirms the information was retrieved, placing the failure upstream of synthesis. A synthesis token limit would show as truncation or thin coverage of *researched* topics, not clean absence.

**Q: Why can no downstream fix — bigger context windows, multi-pass synthesis, peer messaging — repair a coverage gap that predates retrieval?**
A: Because the information was never requested. If no subagent was tasked with a topic, there is nothing in the pipeline to summarize, expand, or forward. Downstream capacity operates on what was gathered; it cannot manufacture an unasked question.

**Q: In a multi-agent system, why is "let the subagents message each other directly to fill the gaps" always wrong?**
A: All inter-subagent communication must route **through the coordinator** — direct peer messaging is an explicit anti-pattern. The coordinator owns task assignment and context distribution; peer channels bypass it, so it loses visibility into what each subagent knows and cannot correct the decomposition.

**Q: A coordinator calls `Task`, waits for the full result, calls `Task` again, and waits. What is wrong, and what does true parallel spawning require?**
A: This is **sequential, not parallel** — one full turn per subagent. True parallel spawning emits **multiple `Task` tool calls within a single coordinator response**. Routing them across separate turns is sequential by definition.

**Q: A teammate proposes a routing classifier to decide the order of four independent subagent research tasks. Assess.**
A: **Premature Infrastructure.** The subtopics are independent, so ordering is irrelevant — the classifier optimizes something that does not matter while leaving the real defect (sequential rather than single-response spawning) untouched. Fix the response shape; add no component.

**Q: What was the subagent-spawning tool renamed to, in which version, and which name should you give on the exam?**
A: Renamed **`Task` → `Agent`** in Claude Code **v2.1.63**. `Task` remains a valid alias and stays the **exam-safe answer**.

---

## Domain 2 — Descriptions vs Tool Scope

**Q: A support agent with four MCP tools misroutes, and the stem notes all four definitions use single-sentence descriptions. What is the most effective first step, and what four things should the fix add?**
A: **Expand the tool descriptions** to include **input formats**, **triggering conditions**, **prerequisites**, and **when-not-to-use boundaries**. The stem names the cause; adding a routing classifier on top of it is over-engineering.

**Q: A synthesis agent that should only combine gathered findings keeps triggering fresh `web_search` calls mid-task. Why is "add more detailed tool descriptions" the distractor here?**
A: Because the model **is not confused about what `web_search` does** — it understands the tool and calls it for a plausible reason (deciding a claim is worth re-verifying). Better descriptions cannot fix correct understanding. The stem's real clue is that the agent *has access to all system tools*, so the fix is **scoping access down**.

**Q: Give the single question that separates "expand tool descriptions" from "scope tool access."**
A: **Does the model misunderstand the tool, or correctly understand a tool it should not have?** Misunderstands → describe better. Understands correctly but shouldn't have it → remove it from the tool set. No amount of description fixes an access problem.

**Q: Why won't prompt guardrails reliably stop a synthesis agent from calling a tool it shouldn't use?**
A: Guardrails only shift probability — the agent retains the capability and a plausible reason to reach for it, so it will occasionally call the tool anyway. Removing the tool from its available set makes the call **impossible** rather than unlikely.

**Q: An agent handling refund disputes has `delete_account` available and has twice called it while "cleaning up." The team proposes a prompt rule forbidding it. Assess and give the correct fix.**
A: The proposal is a **Prompt Bandaid** — a destructive action needs an invariant, not a probability shift. The agent correctly understands a tool it should never have had, so the fix is **tool scoping**: remove `delete_account` from this agent entirely. If some flow genuinely needs it, gate it behind a `PreToolUse` check as well.

---

## Domain 3 — Placement, Mode, Rules, and Headless

**Q: A team wants a `/review-pr` command every developer gets on clone with no per-person setup. Where does it go, and where would user-level commands live?**
A: **`.claude/commands/` at the project root** — it ships with the repository, so cloning is the install step. User-level commands live in **`~/.claude/commands/`**, which would require manually instructing every developer.

**Q: Migrating a 40,000-line monolith into three microservices with no predefined service boundaries — plan mode or direct execution, and why is "direct execution but require permission" still wrong?**
A: **Plan mode.** Direct execution edits files as it goes; requiring permission per edit still edits, and stopping midway leaves you without knowing what plan produced the changes already made. Official guidance: planning is most useful when you are uncertain of the approach, the change touches multiple files, or you are unfamiliar with the code — all three hold here.

**Q: One repo needs strict infra rules only under the Terraform path and different testing rules only under the test path. What is the exact mechanism — directory and frontmatter field?**
A: Separate rule files in **`.claude/rules/`** with YAML frontmatter carrying a **`paths`** field — that exact key, not `globs` or `path`. Each rule set then loads conditionally by file pattern.

**Q: Why can't `@` imports in `CLAUDE.md` express "apply these rules only when touching Terraform"?**
A: Because `@path/to/file` imports are **unconditional** — they expand and load at session launch regardless of what you are working on. Conditional loading by file pattern requires `.claude/rules/` with a `paths` field.

**Q: A path-scoped rules question mentions "without cluttering `CLAUDE.md`." Why is that phrase bait?**
A: It steers you toward `CLAUDE.md` organization options (imports, nested files, one root file) when the actual requirement is **conditionally enforcing rules by path**. `CLAUDE.md` supplies session context; it does not scope rule enforcement. Solve for the enforcement mechanism, not the tidiness.

**Q: Claude Code in GitHub Actions hangs until the job times out on the first run. What is the fix, and why does raising the CI timeout not help?**
A: Run non-interactively with **`-p`** (or `--print`). Interactive Claude Code waits for a human response and CI has no human, so the process waits forever — raising the timeout only extends how long you wait for something that will never arrive.

**Q: Do `CLAUDE_HEADLESS=true` and `--batch` exist in Claude Code?**
A: **Neither exists.** Both are fabricated distractors — verified absent from the official environment-variable reference and CLI reference. This is the *fabricated specific* pattern: a plausible name, invented.

---

## Domain 4 — Batch API and Multi-Pass Review

**Q: A team plans to move a pre-merge CI check that blocks every PR onto the Message Batches API for the 50% saving. What is wrong, and why is it unfixable by configuration?**
A: The batch API has **no latency guarantee** — results may take up to **24 hours**. A blocking gate on an unbounded-latency dependency is an architectural defect, so no parameter repairs it; there is no priority flag (that option is a fabricated specific). Batch suits overnight and non-blocking work only.

**Q: The batch API stem truthfully states a 50% cost saving. What does that teach about reading distractors?**
A: That a **true, attractive fact can be the bait**. The saving is real and the plan is still wrong, because the defect is where batch sits in the pipeline, not what it costs. Verify that the option addresses the described *failure*, not that its claims are accurate.

**Q: A code-review agent reviews a 14-file PR in one pass; some files get skimmed and cross-file issues are missed. What is the fix?**
A: **Split into focused per-file review passes, followed by a separate cross-file integration pass** that specifically checks cross-references — breadth first, then depth. One pass over many files dilutes the relevant signal in bulk context.

**Q: Two distinct mechanisms make single-pass review over many files fail. Name both.**
A: **(1) Signal dilution** — genuine findings are lost in a large volume of mostly irrelevant context. **(2) Trust collapse** — once developers see false positives they discount the entire report, including the true findings. A fix must address both, which is why "review twice and average" fails: it inherits each.

**Q: Why does adding "be thorough" to a review agent's system prompt fail, and why does raising `max_tokens` fail?**
A: "Be thorough" is a **Prompt Bandaid** with no operational definition — it may simply produce *more* false positives. Raising `max_tokens` adds volume to output that is already too diluted, worsening the signal-to-noise ratio rather than improving coverage.

---

## Domain 5 — Observable Criteria and Structured Errors

**Q: An agent escalates below 70% self-reported confidence. Easy cases escalate needlessly *and* complex disputes don't escalate. What does failing in both directions tell you?**
A: That the **signal is broken, not the threshold**. A miscalibrated score produces false positives and false negatives simultaneously, so no threshold placement fixes it. Replace the confidence score with observable escalation criteria.

**Q: Name the three observable escalation criteria that replace a confidence threshold.**
A: **(1)** The customer **explicitly requests a human**. **(2)** The case falls into a **known policy gap or exception**. **(3)** The agent **cannot make progress after a defined number of attempts**. Each is checkable without asking the model how confident it feels.

**Q: Why is "remove escalation entirely and let the agent always resolve it itself" unacceptable, even though bounded self-resolution is good practice?**
A: Because an agent with no exit path **hallucinates a resolution** when it cannot genuinely solve the case, and you are obliged to accept it. Self-resolution must be bounded — attempt, then escalate on defined conditions.

**Q: A document subagent returns the same empty result whether retrieval failed or the document genuinely contained nothing. What breaks, and what is the fix?**
A: The coordinator cannot distinguish the two, so it reports "no information found" for what was actually a retryable network fault. Fix: return **structured error context** — failure category (transient vs genuine empty), what was attempted, and whether a retry makes sense — so the coordinator can retry, try an alternative, or correctly report a real empty result.

**Q: A subagent silently retries three times before returning an empty result to the coordinator. Why is this still the wrong answer?**
A: Retrying is reasonable; **returning a bare empty result afterwards** is not. The coordinator ends up exactly as blind as before and will still misreport a transient failure as "nothing found." The retry is not the fix — making the failure *legible* is.

**Q: Why is "terminate the entire research task immediately on any subagent failure" an anti-pattern?**
A: A single subagent failure should not collapse the whole pipeline. Errors should be **propagated with structure and retried where sensible**, letting the coordinator decide scope — terminate, retry, use an alternative source, or proceed with partial results.

**Q: In the librarian analogy, what distinguishes a valid empty result from a harmful one?**
A: "We don't have that book" is **valid and actionable** — nothing exists, so you move on. Saying the same sentence while the book *is* on the shelf but unlocatable causes a silent loss. "It's here, I can't locate it, come back in half an hour" preserves your ability to act — that is structured error context.

---

## Cross-Domain — Extraction and Codebase Navigation

**Q: An extraction model returns valid JSON that passes schema validation, but the due date is fabricated. Why does raising the retry count from 3 to 10 not help?**
A: Retries repair **structural** faults — malformed JSON, schema violations. Nothing structural is wrong: the JSON is valid and the schema passes. The value is simply absent from the source, so each retry re-fabricates. More attempts buy more hallucinations.

**Q: What is the fix for a model fabricating a field that does not appear in the source document?**
A: **Make the field nullable** so the model can return `null` instead of inventing a value, and **route those documents to human review**. A schema that cannot express *absent* leaves fabrication as the only legal output — the model has no way to be honest.

**Q: Claude Code is asked to find authentication logic in an unfamiliar 200,000-line repo and starts `Read`-ing every `.py` file. Why does "read the whole repo in one large batch instead" fail for the same reason?**
A: Both **exhaust the context window before any useful analysis begins**. The premise that more context yields a more complete answer is wrong once you exceed the window — the reads crowd out the reasoning they were meant to support.

**Q: What does `Grep` search, what does `Glob` match, and why is "use `Glob` to search file contents" a factually wrong option?**
A: **`Grep` searches file contents** (ripgrep regex); **`Glob` matches file paths** by name pattern. The option inverts `Glob`'s function. Correct approach: `Grep` for the keyword, optionally `Glob` to constrain paths, narrowing to a handful of files before the first `Read`.

**Q: A review bot's false positives destroyed developer trust, and "be conservative, only flag high-confidence issues" barely helped. Why does the winning answer name categories instead of adjusting a threshold?**
A: Because "be conservative" is a **vague magnitude** the model cannot apply consistently — Trap 1 plus Trap 4. **Explicit categorical criteria** (which issue types to report versus skip) convert it into a discrete checkable set, and **temporarily disabling the specific high-false-positive category** removes the noise source directly.

**Q: What is the recurring structural shape shared by the prerequisite gate, observable escalation criteria, structured error context, the nullable field, and named report categories?**
A: Each **replaces a fuzzy or ambiguous signal with a discriminating one**. A confidence number conflates "easy" with "confidently wrong"; a bare empty result conflates "nothing exists" with "lookup failed"; a non-nullable field conflates "absent" with "invented." The fix is never a better threshold — it is making the distinction representable and the honest answer expressible.

---

*Back to: [[Bonus - Exam Questions Solved & Exam Traps]]*
