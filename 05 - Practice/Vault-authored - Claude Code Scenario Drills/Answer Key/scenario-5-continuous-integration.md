---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - domain-3
  - domain-4
  - claude-code
  - continuous-integration
date: 2026-08-25
status: done
---

# Scenario 5 — Claude Code for Continuous Integration · Answer Key

**Q11–Q20.** Questions: [../Questions.md](../Questions.md) · index: [../README.md](../README.md) · domain notes: [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]

Primary domains per the official guide: **D3 (Claude Code Configuration & Workflows)** and **D4 (Prompt Engineering & Structured Output)**.

> [!NOTE] Vault-authored, not from a bank
> Derived from the exam guide's task statements 3.6, 4.1, 4.2, 4.4 and 4.6. See [../README.md](../README.md) § Provenance.

---

## Q11 — Regex parsing prose findings keeps breaking → **B**

**B.** `--output-format json` with `--json-schema`.

**Why B wins.** These two flags exist to *"produce machine-parseable structured findings for automated posting as inline PR comments"* — the stem's exact use case. Parsing prose is the problem; enforcing structure at the source is the fix.

| Distractor | Why it fails |
|---|---|
| **A** instruct a Markdown table in the prompt | Prompt-layer formatting. Shifts probability, guarantees nothing — the same class of failure the regex is already suffering from |
| **C** Message Batches API | Batch responses are not inherently structured, and batch is **wrong for a blocking pipeline stage** anyway (up to 24h, no latency SLA) |
| **D** second Claude invocation to convert | Doubles cost and latency to do what one flag does deterministically, and the converter has its own failure rate |

**Takeaway.** Structure is enforced by schema, not requested by prompt — the D4 §4.3 principle applied to the CLI. See [[D3 - Claude Code Configuration & Workflows]] §3.6 · [[EP13 - Claude Code CI-CD Pipelines]].

---

## Q12 — Bot re-posts the same comments after each push → **C**

**C.** Include prior review findings in context and instruct Claude to report only new or still-unaddressed issues.

**Why C wins.** The exam guide names this under 3.6: *"including prior review findings in context when re-running reviews after new commits… to avoid duplicate comments."* Each CI run is a fresh session with no memory of the last one — the fix is to supply what it can't remember.

| Distractor | Why it fails |
|---|---|
| **A** review only the first push | Abandons review of every subsequent commit — the ones most likely to introduce regressions |
| **B** hash-dedupe in the pipeline | Suppresses **identical text** only. Re-phrased findings slip through, and a genuinely *still-unaddressed* issue gets silently hidden, which is worse than a duplicate |
| **D** review only the latest diff | Loses cross-commit context; an issue introduced earlier in the PR and never fixed stops being reported |

> [!IMPORTANT] Why statelessness is the root fact
> Each CI invocation is a fresh session with no memory of the last run. Every "the bot forgot X" problem in CI therefore has the same shape of answer: **put X in the context deliberately**. Q13 is the same fix applied to test generation.
> Note the guide's term *"session context isolation"* means something narrower — see Q15, where it names why a session that generated code reviews it poorly. Don't conflate the two.

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.6.

---

## Q13 — Generated tests duplicate existing coverage → **B**

**B.** Provide the existing test files in context.

**Why B wins.** *"Providing existing test files in context so test generation avoids suggesting duplicate scenarios already covered"* is the exam guide's own phrasing. The model isn't producing bad tests — it can't see what already exists.

| Distractor | Why it fails |
|---|---|
| **A** "avoid duplicates" in the prompt | Instructs the model to avoid something it has no way to perceive. Unactionable instructions don't improve output |
| **C** post-pass dedupe | Pays full generation cost, then discards. Treats the symptom and can't tell a near-duplicate from a genuine edge case |
| **D** coverage-threshold targeting | Line coverage doesn't capture *scenario* coverage. A well-covered file can still be missing a branch case, and a poorly-covered one may be trivial |

**Takeaway.** Same shape as Q12: a stateless invocation can only reason about what you hand it. See [[D3 - Claude Code Configuration & Workflows]] §3.6.

---

## Q14 — Where CI-invoked Claude Code should read team standards → **C**

**C.** `CLAUDE.md`.

**Why C wins.** The exam guide is explicit — `CLAUDE.md` is *"the mechanism for providing project context (testing standards, fixture conventions, review criteria) to CI-invoked Claude Code"*, and *"documenting testing standards, valuable test criteria, and available fixtures in `CLAUDE.md`"* is the named skill for exactly this symptom.

| Distractor | Why it fails |
|---|---|
| **A** inline in the `-p` prompt string | Works, but the standards then live in pipeline YAML — invisible to developers, unversioned alongside the code, and duplicated across every job |
| **B** `.claude/rules/` with a `paths:` glob | The strongest distractor — official sample Q6 keys almost this construction for test conventions, and path-scoped rules do fire on matching files. It loses only because the stem's criteria are **repo-wide judgments that attach to no path**; scope them by glob and they load inconsistently |
| **D** a skill the pipeline invokes | Skills are on-demand and task-specific; standards are always-relevant project context — the `CLAUDE.md` side of the split |

> [!TIP] The `--bare` footnote — real, but off-syllabus
> Current docs describe `--bare` as the recommended mode for scripted/SDK calls, and it **skips `CLAUDE.md`** along with hooks, skills, plugins, MCP servers, and auto memory. That is a genuine production trap ([[EP13 - Claude Code CI-CD Pipelines]] §3.8) — but `--bare` appears **nowhere** in the exam guide. **On the exam, `CLAUDE.md` is the answer.** In real pipelines, pair it with `--append-system-prompt-file`.

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.6 · [[EP13 - Claude Code CI-CD Pipelines]].

---

## Q15 — Same session generates then reviews → **B**

**B.** The model retains its generation reasoning and won't question its own decisions — use an **independent review instance**.

**Why B wins.** Task statements 3.6 and 4.6 both name it: a session that generated the code is *"less effective at reviewing its own changes compared to an independent review instance"*, and independent instances *"are more effective at catching subtle issues than self-review instructions or extended thinking."*

| Distractor | Why it fails |
|---|---|
| **A** better review criteria in the same session | Explicit criteria genuinely help (Q16) — but they don't remove the generation context that is the actual cause here |
| **C** `/compact` first | Compaction summarizes; it doesn't erase the model's commitment to decisions it made. And it's not the CI shape anyway |
| **D** extended thinking | The exam guide rejects this by name — more reasoning **within** the biased context doesn't fix the bias |

**Takeaway.** The fix is architectural (a second instance), not prompt-level. Note that D is explicitly rejected in the source, which makes it the highest-value distractor to have recognized. See [[D4 - Prompt Engineering & Structured Output]] §4.6 · [[EP17 - Batch API & Multi-Pass Review]].

---

## Q16 — "Be conservative" doesn't cut false positives → **B**

**B.** Specific categorical criteria: which issues to report (bugs, security) versus skip (minor style, local patterns).

**Why B wins.** The exam guide states the negative result directly: general instructions like *"be conservative"* or *"only report high-confidence findings"* **fail to improve precision** compared to specific categorical criteria. The stem confirms you've already run that experiment.

| Distractor | Why it fails |
|---|---|
| **A** self-reported confidence + threshold | Confidence-based filtering is precisely what the exam guide contrasts *against* categorical criteria — and model self-reported confidence is poorly calibrated (official sample Q3, option B) |
| **C** three runs, consensus only | Suppresses real bugs caught intermittently — the same reasoning that kills option D in official sample Q12 |
| **D** lower temperature | Makes output more deterministic, not more *correct*. It would reproduce the same false positives more consistently |

**Takeaway.** Vague-quality adjectives never fix precision; enumerated categories do. See [[D4 - Prompt Engineering & Structured Output]] §4.1 · [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]].

---

## Q17 — One bad category poisoning trust in four good ones → **C**

**C.** Temporarily disable the comment-accuracy category while you improve its prompt.

**Why C wins.** The exam guide names both the effect and the remedy: *"high false positive categories undermine confidence in accurate categories"*, and the skill is *"temporarily disabling high false-positive categories to restore developer trust while improving prompts for those categories."* Developer trust is the asset being protected.

| Distractor | Why it fails |
|---|---|
| **A** keep everything running | Optimizes for recall on one category while destroying the usefulness of all five. A review nobody reads catches nothing |
| **B** non-blocking suggestions | Still posts the noise on the PR. The comments are what erodes trust, not their blocking status |
| **D** route to the tech lead | Relocates the noise onto one person and removes the feedback signal that would tell you the rewrite worked |

**Takeaway.** Trust is a system property with its own failure mode. *Temporarily* is load-bearing — this is a staged fix, not a permanent retreat. See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q18 — Inconsistent severity labels → **B**

**B.** Explicit severity criteria with a concrete code example for each level.

**Why B wins.** *"Defining explicit severity criteria with concrete code examples for each severity level to achieve consistent classification"* is the exam guide's skill bullet verbatim in substance. *"Based on impact"* is exactly the vague instruction that produces drift.

| Distractor | Why it fails |
|---|---|
| **A** collapse to two levels | Reduces the surface for inconsistency without fixing it — the blocking/non-blocking boundary drifts for the same reason `critical`/`major` does |
| **C** second instance re-grades | Adds cost and a second inconsistent grader. Independent instances help with **review blindness** (Q15), not with undefined criteria |
| **D** compute severity mechanically | File path and change size don't carry severity: a one-character change in an auth check can be critical, a 500-line docs change trivial |

**Takeaway.** Same root cause as Q16, different symptom: undefined categories. The fix is criteria plus **examples** — this sits on the boundary between explicit criteria (4.1) and few-shot (4.2). See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q19 — Format inconsistent despite detailed instructions → **A**

**A.** Few-shot examples showing location, issue, severity, suggested fix.

**Why A wins.** The exam guide calls few-shot *"the most effective technique for achieving consistently formatted, actionable output when detailed instructions alone produce inconsistent results"* — and names that exact four-part shape as the example. The stem's *"already spells out the required format in detail"* is the precondition that selects few-shot over more instruction.

| Distractor | Why it fails |
|---|---|
| **B** repeat requirements at start and end | Position-aware ordering is a real technique for **long inputs losing middle content** (5.1), not for format compliance. Wrong tool |
| **C** one finding per request | Multiplies cost and loses cross-finding context, without demonstrating the target format even once |
| **D** move format to `CLAUDE.md` | Changes load frequency, not communicative power — the same error as Q10 option B |

**Takeaway.** Instructions detailed but output inconsistent → **demonstrate** the format. This is the D4 twin of Q10's *"show, don't tell"*. See [[D4 - Prompt Engineering & Structured Output]] §4.2 · [[EP15 - Few-Shot Prompting]].

---

## Q20 — Systematically analyzing dismissed findings → **B**

**B.** A `detected_pattern` field recording which code construct triggered the finding.

**Why B wins.** The exam guide names the field: *"adding `detected_pattern` fields to structured findings to enable analysis of false positive patterns when developers dismiss findings."* It's what makes dismissal data **groupable by cause** rather than merely countable.

| Distractor | Why it fails |
|---|---|
| **A** `confidence` | Tells you the model was *unsure*, not **what** it was unsure about. You can't target a prompt fix at a confidence band |
| **C** free-text `reasoning` | Unstructured — you'd be reading hundreds of justifications by hand. The exam guide's word is *"systematic"*, and free text doesn't aggregate |
| **D** `severity` | You almost certainly have this already, and it segments by *how bad* rather than *what triggered it* |

**Takeaway.** To fix a false-positive class you must first be able to **name** it. `detected_pattern` is the named exam artifact for that — a rare literal-recall item in an otherwise judgment-heavy domain. See [[D4 - Prompt Engineering & Structured Output]] §4.4.

---

## Pattern summary — Q11–Q20

| Principle | Questions |
|---|---|
| CI sessions are **stateless** — supply deliberately what they can't remember | 12, 13 |
| Schema/flags enforce structure; prompts only request it | 11 |
| `CLAUDE.md` is the CI context mechanism (`--bare` is real but off-syllabus) | 14 |
| Self-review is structurally weak — use an independent instance, not more thinking | 15 |
| Vague quality adjectives never improve precision; enumerated categories do | 16, 18 |
| False positives are a **trust** problem, not just an accuracy problem | 17 |
| Instructions detailed but output inconsistent → few-shot demonstration | 19 |
| Name the failure class before trying to fix it — `detected_pattern` | 20 |

Log misses in [[Weak Areas Deep Dive]] tagged `D3` / `D4`, and compare against [[Answer Patterns Index]] — the CI and Claude Code patterns above are the ones it flags as untested by any CyberSkill sitting.

*Back: [scenario-2-code-generation.md](scenario-2-code-generation.md)*
