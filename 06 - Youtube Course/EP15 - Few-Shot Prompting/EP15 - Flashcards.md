---
tags:
  - CCA-F
  - domain-4
  - prompt-engineering
  - few-shot
  - youtube-course
  - flashcards
date: 2026-08-05
status: done
domain: "4 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 15"
---

# 🃏 EP15 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP15 — Few-Shot Prompting** (Domain 4, task statement 4.2). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry material the lecture doesn't reach: the `<example>` XML wrapper, the three example criteria, `<thinking>` tags inside examples, and the tool-description caveat. Two cards record **verified conflicts** — the example count (lecture 2–4 vs docs 3–5) and the claim that *count* causes pattern matching (docs: diversity does). Both give the exam answer explicitly.

**Related:** [[EP15 - Few-Shot Prompting]] · [[D4 - Prompt Engineering & Structured Output]] · [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]] · [[EP16 - Structured Output & JSON Schema]]

---

## Domain 4 — Why Instructions Alone Fail

**Q: A CI/CD reviewer assigns the same bug a different severity on different runs. Which stage of its work is actually broken?**
A: None of the analysis stages. It detects the bug and reasons about it correctly every time — only the **mapping from finding to label** drifts, because the prompt described that boundary without demonstrating it.

**Q: Why does an undemonstrated label boundary produce *inconsistency* rather than a consistent wrong answer?**
A: There's no bug to be consistently wrong about — there's an unspecified decision. The model resolves the ambiguity itself on each run independently, so nothing carries the same resolution forward.

**Q: EP14 said write specific, detailed prompts. EP15 says more words make it worse. What exactly does the "instruction ceiling" cap?**
A: Prose as a *delivery mechanism*, not precision. Short categorical criteria add specificity without ambiguity surface; a long narrative adds surface without specificity.

**Q: Besides ambiguity, name the second reason the lecture gives for prose definitions failing over time.**
A: They go stale — a definition only enumerates the cases known when it was written, and the codebase keeps producing new ones. Hence: as code evolves, the examples must evolve.

**Q: Your prompt says "flag severe issues" but your labels are `critical`/`high`/`medium`. Why is this worse than the word simply being undefined?**
A: It isn't undefined — it's defined by the **wrong corpus**. The model calibrates `severe` against internet training data rather than your codebase, silently importing a foreign standard.

**Q: Someone proposes fixing severity drift by adding another 300 words of severity definitions. What will happen and why?**
A: Nothing improves. More prose is the approach that already failed — the boundary is one the team recognises but can't articulate, so more description can't transmit it. It has to be demonstrated.

---

## Domain 4 — Demonstration and Example Anatomy

**Q: State the exam claim comparing few-shot examples to detailed prose.**
A: Few-shot examples are **explicitly more effective** than long prose passages, because they demonstrate the target behaviour rather than describing it.

**Q: In the workout analogy, what does the demonstration video carry that the written article cannot?**
A: The details nobody thought to write down — exact positioning relative to back and core. A description transmits only the enumerated cases; a demonstration transmits the whole configuration.

**Q: Name the three components of a well-formed few-shot example.**
A: **Input → reasoning → output.** The output tells the model *what*; the reasoning tells it *how*.

**Q: Which component of a few-shot example is optional — input, reasoning, or output?**
A: None. Reasoning in particular is mandatory on **every** example, not just the ambiguous ones.

**Q: In a severity-classification example, what belongs in the reasoning field?**
A: Why *this* case lands in *that* bucket — e.g. *"authentication check exists but is silently bypassed; any request proceeds regardless of token validity"* → `critical`.

**Q: What are Anthropic's three stated criteria for few-shot examples? (docs)**
A: **Relevant** (mirror the actual use case), **diverse** (cover edge cases and vary enough that Claude doesn't pick up unintended patterns), and **structured** (wrapped in `<example>` tags).

**Q: What concretely goes wrong if few-shot examples aren't wrapped in `<example>` / `<examples>` tags? (docs)**
A: Claude can read a demonstration as an **instruction** — an unwrapped *"classify this as critical"* becomes a standing order instead of a sample. The tags are the delimiter separating demos from instructions.

**Q: Which documented technique makes a demonstrated reasoning pattern shape the model's own reasoning, not just its labels? (docs)**
A: Putting `<thinking>` tags inside the few-shot examples — Claude generalises that reasoning style to its own extended thinking blocks.

---

## Domain 4 — Output-Only Examples and Generalization

**Q: You supply three input→label pairs with no reasoning. What behaviour does the model adopt?**
A: **Pattern matching** — it asks *"which example does this look like?"* and matches, instead of extracting and applying a decision rule.

**Q: Name the two distinct failure modes of output-only examples.**
A: (1) Near-miss inputs get **force-fitted** into the closest example whether or not the rule applies; (2) genuinely novel inputs have **nothing to match**, so the model improvises an error or off-format output.

**Q: Unpack "the answer is specific, but the reason behind that answer is generally general."**
A: A label transfers to exactly one case; its reasoning transfers to every case the rule covers. Output-only examples keep the specific and discard the general — and the general is what handles the inputs you never enumerated.

**Q: Explain the point → line → plane progression.**
A: A **point** is one input/label pair covering only itself. A **line** is a few examples suggesting one axis of extrapolation. A **plane** is a decision surface from demonstrated reasoning, covering the full input space including axes you never sampled.

**Q: Why is machine-learning overfitting an apt analogy for output-only examples, and what does it predict?**
A: Both show high accuracy on familiar inputs and brittleness under deviation. It predicts that adding **more similar** examples makes the problem worse, not better.

**Q: What is the actual deliverable of a good few-shot example set — the examples themselves, or something else?**
A: The **decision rule**. Examples are the medium; the model extracting the underlying rule and applying it to novel inputs is the goal.

**Q: In what sense do output-only examples make the model *less* capable than it is?**
A: The model can reason, but examples with no reasoning instruct it to match instead — restricting it to its pattern-matching capability when the reasoning capability was available.

---

## Domain 4 — How Many Examples

**Q: How many few-shot examples should you answer on the exam?**
A: **2–4 targeted examples.**

**Q: Official docs give a different example count from the course. What is it, and which do you use where?**
A: Docs: *"Include 3–5 examples for best results."* Course/exam: 2–4. **Answer 2–4 on the exam; write 3–4 in real code** — that range satisfies both.
Source: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices

**Q: What failure does the lecture attribute to going past four examples?**
A: Context bloat, and re-triggering the **pattern matching** the reasoning was meant to prevent — *"why five to eight fails."*

**Q: The lecture blames example *count* for unintended pattern matching. What do the official docs identify as the real driver? (docs)**
A: **Diversity.** Examples must *"vary enough that Claude doesn't pick up unintended patterns"* — so four near-identical examples pattern-match worse than six well-spread ones. The count cap is a proxy for a diversity budget.

**Q: Why does the number 5 make the count conflict awkward rather than a simple off-by-one?**
A: It sits **inside** Anthropic's recommended 3–5 range and **inside** the lecture's stated 5–8 failure range — the two sources disagree about the same number, not just about the endpoints.

---

## Domain 4 — Choosing Which Examples

**Q: You have budget for three examples. Describe the diagnostic that picks them.**
A: Run ~10 representative inputs **3× each**, find where the label flips between runs, and write examples for the drifting cases only.

**Q: Why is exampling the cases you find obviously hard the wrong strategy?**
A: Obvious cases are ones the model already handles, so an example there spends budget confirming what works — *"practicing the same things that you already know."* Only the flips mark a real boundary.

**Q: A single test run shows every finding labelled correctly. Why is that not evidence the prompt is consistent?**
A: With one run there is nothing to disagree with — a $k=1$ audit reports 100% both before and after the fix. Drift is only visible when the same inputs are sampled repeatedly.

**Q: How does drift-based selection also solve the diversity problem?**
A: Drift lives at the boundary, so cases selected because they flip are automatically spread across it. Pick examples this way and the diversity criterion satisfies itself.

**Q: In what order should a set of few-shot examples be arranged?**
A: Simple → complex: start with a straightforward scenario and let the last example be the complicated one.

---

## Domain 4 — Scenario Patterns

**Q: Distinguish categorical drift from structural drift.**
A: **Categorical** — right finding, wrong bucket (same bug labelled `critical` then `high`). **Structural** — right fields, different input shape (a contract arriving as a table one time and prose the next).

**Q: An extraction agent gets contracts as tables and as prose. What's the minimum example set?**
A: One example per document shape — one structured table, one prose paragraph — each extracting the same fields with reasoning about where the values live.

**Q: How does reasoning in an extraction example differ in content from reasoning in a classification example?**
A: Classification reasoning justifies a **label choice** (why this is `critical`). Extraction reasoning names the **input's structure and where values live** (*"no explicit table; contract value is stated in prose"*).

**Q: Two examples teach an extraction agent to read both tables and prose. What failure do they still leave open?**
A: **Fabrication** when a field is absent. Examples that only ever demonstrate successful extraction teach that extraction always succeeds — pair them with a null/empty correct output.

**Q: A support agent escalates inconsistently on borderline tickets. How many examples, and what must be true of them?**
A: **Two**, straddling the boundary: same category (both billing complaints), differing only on the variables that actually decide — one-off vs repeated failure, and whether prior support contact already failed.

**Q: Why must a boundary-straddling pair hold everything except the deciding factors constant?**
A: A pair differing on many axes sits *near* the line without locating it — the model can't tell which difference mattered. Controlling the rest isolates the deciding variable.

**Q: Besides carrying reasoning, what did the capstone support prompt require of the agent's own output?**
A: *"Always state your reasoning before your action"* — reasoning in the examples, reasoning in the responses, making each live decision auditable.

---

## Domain 4 — Distractors and Ordering

**Q: Why doesn't lowering `temperature` fix inconsistent severity labelling?**
A: Temperature controls **randomness, not judgement**. If probability mass is genuinely split between two labels, low temperature just makes the model pick the marginal favourite every time — random inconsistency becomes stable inconsistency, and the boundary is still undefined.

**Q: On current Claude models, what happens if you send `temperature` in the request? (docs)**
A: It's a **400 error** — `temperature`, `top_p`, and `top_k` are removed on Claude Opus 5, Fable 5, Opus 4.8, and Opus 4.7; Sonnet 5 rejects non-default values. They remain accepted on Opus 4.6 / Sonnet 4.6 and older.
Source: https://platform.claude.com/docs/en/about-claude/models/migration-guide

**Q: A CI/CD agent inconsistently assigns severity to the same bug class. Options: detailed prose definitions · 2–4 few-shot examples with reasoning · lower the temperature · 6–8 examples. Which, and why are the others wrong?**
A: **2–4 few-shot examples with reasoning.** Prose is the instruction ceiling that already failed; temperature is randomness not judgement; 6–8 exceeds the range and re-triggers pattern matching.

**Q: An agent has good few-shot examples but keeps calling the wrong tool. What's the correct sequence?**
A: **Expand the tool description first, add examples second.** Claude must pick the right tool before any example can apply.

**Q: Why is "description first, examples second" an ordering rather than a preference?**
A: Routing happens *before* behaviour. Examples shape what happens inside a chosen path, so if the wrong tool was called they're never reached — *"practically useless."* Fixing the second while the first is broken shows no measurable change.

**Q: When does the description-before-examples ordering not apply?**
A: Plain prompting with no tools — there's no routing step, so few-shot is straightforwardly the stronger lever.

**Q: You fix a weak tool description by pasting your few-shot examples into it. Why is that the wrong fix? (docs)**
A: Descriptions ride along in **every** request, so embedded examples cost tokens continuously and constrain the model's exploration. Make the description prescriptive about *when* to call the tool and the parameters expressive; keep demonstrations in the prompt or a skill.

---

*Back to: [[EP15 - Few-Shot Prompting]]*
