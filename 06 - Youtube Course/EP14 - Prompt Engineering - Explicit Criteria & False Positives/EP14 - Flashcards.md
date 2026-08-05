---
tags:
  - CCA-F
  - domain-4
  - prompt-engineering
  - false-positives
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "4 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 14"
---

# 🃏 EP14 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP14 — Prompt Engineering: Explicit Criteria & False Positives** (Domain 4, task statement 4.1). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> This episode is unusually accurate: no hard conflicts with official docs were found, and it lines up closely with [[D4 - Prompt Engineering & Structured Output]] § 4.1. Cards marked **(docs)** carry material the lecture doesn't reach — the colleague test, example counts, XML structuring, and the documented hallucination-reduction techniques behind the null rule. One card flags where the lecture's illustrative percentages should not be memorised as fact.

**Related:** [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]] · [[D4 - Prompt Engineering & Structured Output]] · [[EP13 - Claude Code CI-CD Pipelines]] · [[EP15 - Few-Shot Prompting]]

---

## Domain 4 — The False Positive Problem

**Q: A reviewer flags 47 issues per PR and a real SQL injection reaches production. Was this a detection failure?**
A: **No.** The reviewer found it — at rank #23 in a 50-item list, past where the developer stopped reading. It's a **precision** failure that becomes a detection failure at the human boundary.

**Q: Why is "a finding nobody reads" the same as "a finding nobody made"?**
A: Because the reviewer's value is realised only when a human acts on it. Nominal recall can be perfect while delivered recall is zero — noise pushes real findings past the point where anyone is still reading.

**Q: Your style category is 90% false positives; your security category is accurate. Do developers still trust security findings?**
A: **No.** Trust is holistic — it doesn't partition by category. Once developers learn to dismiss the bot, they dismiss all of it.

**Q: Why is a noisy review category worse than a category that does nothing at all?**
A: A useless category adds nothing; a noisy one **subtracts** — it teaches the dismissal habit that then applies to your accurate categories too. That's what makes false positives actively dangerous rather than merely wasteful.

**Q: Why is deleting a whole review category a rational move rather than a loss of coverage?**
A: Because trust is holistic, a noisy category is a tax charged against the accurate ones. Removing it raises the *delivered* value of everything that remains, even though nominal coverage drops.

---

## Domain 4 — Why Vague Instructions Fail

**Q: You tell Claude "be thorough and conservative in your analysis." What does it actually produce, and why?**
A: Everything — style comments, naming opinions, micro-optimizations. It can't calibrate "conservative" to your team, so it acts on the only part it can apply unilaterally: thoroughness.

**Q: "Conservative relative to what?" — name the three reference points the lecture uses to show the word is undefined.**
A: A security researcher, a junior developer, or a team-specific linter configuration. Each implies a different bar, and the model has no way to know which one you mean.

**Q: State the golden rule for testing whether a prompt is clear enough. (docs)**
A: *Show the prompt to a colleague with minimal context on the task and ask them to follow it — if they'd be confused, Claude will be too.*

**Q: What is the official mental model for why precise prompts work better? (docs)**
A: Think of Claude as *"a brilliant but new employee who lacks context on your norms and workflows."* The failure isn't capability — it's missing context about your standards, which only you can supply.

**Q: Why is writing a vague prompt described as a delegation rather than a communication slip?**
A: Because it hands the model a decision that belongs to your team. Claude isn't responsible for your review standards; when the prompt doesn't state them, the model supplies its own.

**Q: A `CLAUDE.md` file exists with project rules. Does that rescue a vague prompt?**
A: **No.** `CLAUDE.md` sets background context, but it gives an overall picture of the project rather than per-scenario criteria. A vague prompt still leaves the specific report/skip decision unmade.

---

## Domain 4 — Why Confidence Thresholds Fail

**Q: Why doesn't "only flag issues you're 80% confident about" improve precision?**
A: Model confidence is calibrated to its training distribution, not your codebase or your team's definition of a real problem. It's a vague instruction wearing a number.

**Q: Summarise the defect in confidence-threshold prompting in one phrase.**
A: **You're asking the model to judge its own judgment** — the same as shipping on a developer's own assurance that their code works, which is exactly why testers exist.

**Q: What do "be conservative" and "only flag if 80% confident" have in common?**
A: Both ask the model for a judgment you never specified — one for taste, one for self-assessment. Neither supplies your team's definition of what counts.

**Q: The lecture says the model may be "85% confident" about a naming issue and "65%" about SQL injection. How much weight should those numbers carry?**
A: Treat them as illustration, not measurement — the specific percentages aren't a documented finding. The exam-supported claim is general: self-reported confidence tracks the training distribution, not your severity bar.

**Q: Would lowering a confidence threshold from 80% to 60% help a noisy category?**
A: **No.** The threshold was never the mechanism, so moving it doesn't change what the model considers reportable. The fix is criteria — or, for a persistently noisy category, disabling it.

---

## Domain 4 — Categorical Criteria

**Q: What replaces judgment instructions, and what is the one-line statement of the shift?**
A: **Categorical criteria** — explicit report and skip lists. *"You don't ask the model to decide what matters. You tell."*

**Q: What does every vague instruction conceal?**
A: A list you haven't written. "Avoid nitpicky suggestions" presumes a shared definition of nitpicky; writing out the categories is what supplies it — and that act *is* categorical criteria.

**Q: Convert "avoid nitpicky suggestions" into a categorical rule.**
A: "Skip style not enforced by our linter; skip variable naming and snake-case conventions." Named categories, checkable against the code.

**Q: Name the four components of a categorical criteria prompt.**
A: Named **report** categories with their contents; an explicit **skip** list; **labels your team already uses** for triage; and an **output format** (e.g. "output must be a JSON array of objects").

**Q: Why does a categorical prompt specify an output format at all?**
A: Because the default response is prose, and a downstream consumer — the next CI step, a merge gate — needs a parseable shape to act on.

**Q: The skip list contains "sub-5 ms optimizations." Isn't that a threshold, and didn't thresholds fail?**
A: It's a threshold **scoped inside a named category**, which makes it a criterion. What fails is a *global* confidence bar floated across all findings with no category attached.

**Q: Anthropic's docs say "tell Claude what to do, not what not to do." How does a "never report" list survive that? (docs)**
A: That guidance targets **response formatting**, where a bare prohibition leaves the shape unspecified. A skip list is a **scoping** rule paired with a report list, so the two partition the space. A negative works when it names a **category**, and fails when it names a **vibe**.

**Q: What structural technique makes a long criteria prompt more reliable? (docs)**
A: Wrap its parts in **XML tags** — report list, skip list, output format — so Claude can distinguish instructions from data and from examples.

---

## Domain 4 — Severity and Noisy Categories

**Q: You've defined severity levels in words. Why isn't that enough, and what's the fix?**
A: Words about severity are the same vagueness problem one level down. Show **code examples** of what each severity looks like rather than describing it.

**Q: Would a 1,000-line severity description outperform a short prompt with per-severity code examples?**
A: **No** — and it may confuse the model. Concreteness is the axis, not length.

**Q: How many examples should you include, and which property matters most? (docs)**
A: **3–5.** They must be relevant and structured, but above all **diverse** — enough variation that Claude doesn't pick up unintended patterns. A tier illustrated only by SQL injection teaches "critical means SQL injection," not the concept.

**Q: A category has an 80% false-positive rate and tightening its criteria hasn't helped. What do you do?**
A: **Temporarily disable the category entirely.** Rebuild trust with the categories that work, then reintroduce it later with better criteria and real examples.

**Q: In a "noisy category" exam question, what are the paired right and wrong answers?**
A: Right: **disable the category**. Wrong: **lower its threshold** — tempting because it sounds proportionate, but thresholds aren't criteria, so a lower one still produces false positives.

---

## Domain 4 — Fabrication in Structured Extraction

**Q: What does a false positive look like in a data-extraction pipeline rather than a code review?**
A: **Fabricated data** — the model filling fields that don't appear in the source document, such as a patient ID it never saw or a diagnosis code it inferred from context.

**Q: An extraction agent invents a patient ID. Is this a model failure?**
A: **No — a prompt failure.** "Extract the structured data" states the goal but not what to do when a field is missing, so the model fills the gap with its own judgment about what the record probably should contain.

**Q: Give the three-state rule for an extraction field.**
A: Present → extract **as written**. Absent → return **`null`**. Ambiguous → return the **raw text**, don't normalize to the closest match.

**Q: Why does "never fabricate" need `return null` alongside it?**
A: Because a prohibition alone still leaves the model needing something to emit for a missing field. `null` supplies the alternative behaviour — the same reason a skip list only works paired with a report list.

**Q: What is the line that captures the stakes asymmetry in extraction?**
A: *"Null is a valid answer. A fabricated ID is a lawsuit."* A style false positive costs attention; a fabricated patient ID or dosage is a different category of harm.

**Q: Which documented technique directly addresses a diagnosis code "inferred from the context"? (docs)**
A: **External knowledge restriction** — explicitly instruct Claude to use only information from the provided documents and not its general knowledge.

**Q: Which hallucination-reduction technique is `return null` the structured-output form of? (docs)**
A: **Allowing Claude to say "I don't know."** Explicit permission to admit uncertainty *"can drastically reduce false information"* — without it, the schema itself pressures the model to produce something.

**Q: Beyond null-handling, name a documented technique that makes each extracted value auditable. (docs)**
A: **Verification with citations** — require a supporting verbatim quote for each value, and retract anything that can't be supported. For long documents, extracting exact quotes first also grounds the output.

---

## Domain 4 — Exam Ordering

**Q: An exam question asks the most effective FIRST step to reduce false positives. Which option, and why not few-shot examples?**
A: **Categorical criteria.** Criteria *define* the boundary; examples only *demonstrate* it — so few-shot sharpens a defined standard rather than substituting for defining one.

**Q: When IS few-shot prompting the right answer in this task statement?**
A: When the stem asks how to **improve further** after criteria are already in place — not when it asks for the first step. The word "first" is the discriminator.

**Q: State the single root cause shared by vague adjectives, confidence thresholds, and extraction fabrication.**
A: In each case you left a decision unspecified and the model made it for you — what counts as an issue, whose confidence bar applies, what to emit for a missing field.

---

*Back to: [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]]*
