---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-28
status: done
---

# CyberSkill CCAF — Unified Bank (deduplicated)

**Back to:** [[00 - START HERE]] · [[CCA-F Study Roadmap]] · [../README.md](../README.md)

> [!IMPORTANT] A derived view, not a fourth sitting
> The three CyberSkill folders draw from one item bank, so ~⅔ of their questions coincide. This folder resolves those 180 question-slots into the **80 distinct items** behind them, each appearing exactly once, merged with the best stem, the best-attested answer, and the strongest explanation available across the three.
> 
> **The three source folders are unchanged and remain the source of truth.** Nothing here replaces them — [../README.md](../README.md) explains why they stay separate, and that reasoning still holds. Use this folder when you want *coverage without repetition*; use the sittings when you want a timed 60-question run.

## Files

| File | What |
|---|---|
| [Questions.md](Questions.md) | All 80, unmarked. `U1`–`U60` are full multiple choice; `U61`–`U80` are open-response |
| [Answer Key/unified-research_pipeline.md](Answer%20Key/unified-research_pipeline.md) | 20 answers |
| [Answer Key/unified-code_exploration.md](Answer%20Key/unified-code_exploration.md) | 20 answers |
| [Answer Key/unified-customer_support.md](Answer%20Key/unified-customer_support.md) | 20 answers |
| [Answer Key/unified-extraction_pipeline.md](Answer%20Key/unified-extraction_pipeline.md) | 20 answers |

> [!NOTE] Why these filenames are prefixed
> The three source folders all name their keys `research_pipeline.md`, `code_exploration.md`, `customer_support.md`, `extraction_pipeline.md` — a three-way collision the vault works around by linking them with relative Markdown instead of `[[wikilinks]]`. This folder prefixes its four with `unified-` so it adds no fourth collision, and so its keys **can** be safely wikilinked: [[unified-research_pipeline]].

---

## What the merge found

Building the item-level crosswalk required matching all 180 stems pairwise. Four things fell out of it that were not previously recorded anywhere in the vault.

### 1. The bank behind the three sittings is 80 items — 20 per domain, exactly

| Domain | Distinct items |
|---|---:|
| `research_pipeline` | 20 |
| `code_exploration` | 20 |
| `customer_support` | 20 |
| `extraction_pipeline` | 20 |
| **Total** | **80** |

A perfectly even 20/20/20/20 split is a strong signal the underlying bank is exactly 80 items, evenly stocked. It is **not proof** — three draws cannot rule out an item no sitting happened to surface — but nothing in three sittings hints at an 81st.

### 2. `New Mock ↔ Mock Exam` overlap is 42/60, not 40/60

> [!WARNING] Correction applied vault-wide on 2026-08-28
> The overlap matrix in [../README.md](../README.md) recorded **40 / 60**. Item-level matching finds **42**. The two extra pairs are `N-Q48` ≡ `O-Q8` (the $50B/$35B uncertainty item) and `N-Q2` ≡ `O-Q32` (the `escalate_to_human` trigger item) — both genuine matches the earlier count missed, because the Mock Exam key paraphrases those two stems unusually tersely and they read as different questions at a glance.
>
> Every note carrying the old figure has been updated: [../README.md](../README.md), [../Answer Patterns Index.md](../Answer%20Patterns%20Index.md), [../CyberSkill CCAF - New Mock Exam/README.md](../CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) and its `Questions.md`, [[CCA-F Study Roadmap]], `AGENTS.md` and `CLAUDE.md`. **`.github/copilot-instructions.md` still says 40/60** — that file is managed separately and must not be edited from here.
> 
> The other two pairings reproduce exactly: `N↔T` **48/60** ✅ and `O↔T` **42/60** ✅. So does every downstream claim — 12 Timed items absent from New Mock, 10 of them present in Mock Exam, and `T-Q4` / `T-Q10` as the only two items unique to one sitting.

### 3. Zero answer conflicts across 132 overlapping pairs

Every cluster with two or more sittings gives 132 cross-sitting answer comparisons. **All 132 agree** — and all 132 land on the *same letter*, which means the bank reshuffles question order between sittings but **not** option order within a question. Three independently captured keys, no contradiction anywhere. Likewise all 180 domain labels agree: not one item was filed under different domains by different sittings.

### 4. One item in the whole bank rests on nothing but reasoning

`U45` (`N-Q45`) is the only item drawn by exactly one sitting *and* never confirmed by a grader. If you disagree with one answer in this folder, that is the one where you are most likely to be right.

---

## How strongly is each answer attested?

| | Meaning | Items |
|---|---|---:|
| 🥇 | **Grader-authoritative** — the site's own `correct_key`, read off the 2026-08-24 review page | 60 |
| 🥈 | **Grader-confirmed** — marked correct by the site's grader on the 2026-08-23 sitting | 9 |
| 📘 | **Doc-verified** — no grader ever saw it; reasoning cites official docs | 8 |
| 🤔 | **Reasoned only** — no grader, and no doc that actually covers the point | 3 |
| 🏛 | **Blueprint-named** *(additional mark)* — the pattern is named in the [[Official Exam Blueprint]] itself. Outranks every row above | 11 |

**69 of 80 (86%) carry a grader's verdict**, and 11 carry the blueprint's.

> [!NOTE] Revised 2026-09-02 after a full re-verification pass
> All 80 answers were independently re-derived against live official docs. **No keyed letter changed.** Three things did:
> - **U76 and U77 moved 📘 → 🤔.** Their cited page (the customer-support use-case guide) was read in full and contains nothing on partial tool failure, graceful degradation, or honouring a request for a human. The actions are still right; the citation was decorative.
> - **Eleven items gained 🏛** — `U3` `U4` `U15` `U16` `U21` `U35` `U41` `U46` `U51` `U56` `U64`. The blueprint names their pattern almost verbatim (scenario 1 line 242, scenario 3 line 244, §5.5 line 185), which is stronger evidence than a third-party grader.
> - **`U47` and `U60` gained 📘** alongside their grader marks: the subagents page confirms the exact mechanism both turn on — a subagent receives only the `Agent` tool's prompt string, never the parent's history or tool results.
>
> **Three rationale defects were corrected** — `U27` (post-hoc citation verification is a *documented* technique, so it loses on cost, not soundness), `U62` (`allowedTools` omission is not the silent-failure path, and the usual `tools`-vs-`allowedTools` objection doesn't apply to this stem), and `U66` (`--session-id` assigns an ID; it is not a resume path). `U69`'s existing "don't generalise to always split tools" warning was re-checked and **confirmed correct** — leave it as the standing caution it is. See each entry.

And by fidelity:

| | Meaning | Items |
|---|---|---:|
| 🅰 | **Full MCQ** — all four options quoted; drillable as multiple choice | 60 |
| ✍️ | **Open-response** — no option list exists in any source; stem + correct answer only | 20 |

---

## The crosswalk

Every distinct item, with the sittings it appeared in. `—` means that sitting never drew it.

| U# | Domain | Key | Fid. | Auth. | New Mock | Mock Exam | Timed 08-24 |
|---|---|:--:|:--:|:--:|:--:|:--:|:--:|
| **U1** | `code_exploration` | C | 🅰 | 🥇 | `N-Q1` | `O-Q20` | `T-Q40` |
| **U2** | `customer_support` | A | 🅰 | 🥇 | `N-Q2` | `O-Q32` | `T-Q38` |
| **U3** | `extraction_pipeline` | B | 🅰 | 🥇🏛 | `N-Q3` | — | `T-Q17` |
| **U4** | `extraction_pipeline` | B | 🅰 | 🥈🏛 | `N-Q4` | `O-Q60` | — |
| **U5** | `research_pipeline` | C | 🅰 | 🥇 | `N-Q5` | — | `T-Q48` |
| **U6** | `customer_support` | C | 🅰 | 🥇 | `N-Q6` | `O-Q39` | `T-Q18` |
| **U7** | `code_exploration` | B | 🅰 | 🥇 | `N-Q7` | — | `T-Q57` |
| **U8** | `customer_support` | B | 🅰 | 🥇 | `N-Q8` | `O-Q43` | `T-Q60` |
| **U9** | `code_exploration` | B | 🅰 | 🥇 | `N-Q9` | — | `T-Q29` |
| **U10** | `code_exploration` | B | 🅰 | 🥇 | `N-Q10` | — | `T-Q43` |
| **U11** | `code_exploration` | B | 🅰 | 🥇 | `N-Q11` | `O-Q24` | `T-Q11` |
| **U12** | `code_exploration` | D | 🅰 | 🥈 | `N-Q12` | `O-Q27` | — |
| **U13** | `extraction_pipeline` | C | 🅰 | 🥇 | `N-Q13` | `O-Q55` | `T-Q6` |
| **U14** | `code_exploration` | B | 🅰 | 🥇 | `N-Q14` | — | `T-Q28` |
| **U15** | `customer_support` | A | 🅰 | 🥇🏛 | `N-Q15` | `O-Q38` | `T-Q20` |
| **U16** | `customer_support` | A | 🅰 | 📘🏛 | `N-Q16` | `O-Q44` | — |
| **U17** | `research_pipeline` | B | 🅰 | 🥇 | `N-Q17` | — | `T-Q44` |
| **U18** | `extraction_pipeline` | C | 🅰 | 🥇 | `N-Q18` | `O-Q58` | `T-Q27` |
| **U19** | `extraction_pipeline` | D | 🅰 | 🥇 | `N-Q19` | `O-Q46` | `T-Q7` |
| **U20** | `code_exploration` | C | 🅰 | 🥇 | `N-Q20` | `O-Q30` | `T-Q55` |
| **U21** | `extraction_pipeline` | B | 🅰 | 🥇🏛 | `N-Q21` | `O-Q56` | `T-Q59` |
| **U22** | `extraction_pipeline` | B | 🅰 | 🥇 | `N-Q22` | — | `T-Q12` |
| **U23** | `customer_support` | B | 🅰 | 🥇 | `N-Q23` | — | `T-Q3` |
| **U24** | `research_pipeline` | B | 🅰 | 🥇 | `N-Q24` | — | `T-Q53` |
| **U25** | `research_pipeline` | C | 🅰 | 🥇 | `N-Q25` | `O-Q11` | `T-Q54` |
| **U26** | `customer_support` | B | 🅰 | 🥇 | `N-Q26` | `O-Q31` | `T-Q34` |
| **U27** | `extraction_pipeline` | B | 🅰 | 🥇 | `N-Q27` | `O-Q52` | `T-Q25` |
| **U28** | `research_pipeline` | B | 🅰 | 🥇 | `N-Q28` | `O-Q9` | `T-Q15` |
| **U29** | `research_pipeline` | D | 🅰 | 🥇 | `N-Q29` | `O-Q12` | `T-Q50` |
| **U30** | `customer_support` | C | 🅰 | 🥇 | `N-Q30` | `O-Q37` | `T-Q26` |
| **U31** | `code_exploration` | D | 🅰 | 🥇 | `N-Q31` | `O-Q22` | `T-Q37` |
| **U32** | `research_pipeline` | C | 🅰 | 🥈 | `N-Q32` | `O-Q5` | — |
| **U33** | `extraction_pipeline` | D | 🅰 | 🥇 | `N-Q33` | `O-Q51` | `T-Q2` |
| **U34** | `research_pipeline` | B | 🅰 | 🥇 | `N-Q34` | — | `T-Q35` |
| **U35** | `extraction_pipeline` | D | 🅰 | 🥇🏛 | `N-Q35` | `O-Q53` | `T-Q14` |
| **U36** | `extraction_pipeline` | B | 🅰 | 🥈 | `N-Q36` | — | — |
| **U37** | `code_exploration` | D | 🅰 | 🥈 | `N-Q37` | `O-Q16` | — |
| **U38** | `code_exploration` | B | 🅰 | 🥇 | `N-Q38` | `O-Q23` | `T-Q52` |
| **U39** | `code_exploration` | D | 🅰 | 🥇 | `N-Q39` | `O-Q29` | `T-Q9` |
| **U40** | `customer_support` | B | 🅰 | 🥇 | `N-Q40` | — | `T-Q1` |
| **U41** | `customer_support` | C | 🅰 | 🥈🏛 | `N-Q41` | `O-Q34` | — |
| **U42** | `code_exploration` | C | 🅰 | 🥇 | `N-Q42` | `O-Q21` | `T-Q39` |
| **U43** | `research_pipeline` | A | 🅰 | 🥇 | `N-Q43` | `O-Q6` | `T-Q47` |
| **U44** | `code_exploration` | A | 🅰 | 🥇 | `N-Q44` | `O-Q18` | `T-Q58` |
| **U45** | `customer_support` | B | 🅰 | 🤔 | `N-Q45` | — | — |
| **U46** | `extraction_pipeline` | B | 🅰 | 🥇🏛 | `N-Q46` | — | `T-Q24` |
| **U47** | `research_pipeline` | B | 🅰 | 🥈 | `N-Q47` | `O-Q10` | — |
| **U48** | `research_pipeline` | C | 🅰 | 🥇 | `N-Q48` | `O-Q8` | `T-Q21` |
| **U49** | `extraction_pipeline` | D | 🅰 | 🥇 | `N-Q49` | `O-Q50` | `T-Q5` |
| **U50** | `code_exploration` | D | 🅰 | 🥇 | `N-Q50` | `O-Q26` | `T-Q45` |
| **U51** | `extraction_pipeline` | A | 🅰 | 🥇🏛 | `N-Q51` | `O-Q59` | `T-Q42` |
| **U52** | `research_pipeline` | C | 🅰 | 🥇 | `N-Q52` | — | `T-Q30` |
| **U53** | `research_pipeline` | B | 🅰 | 🥈 | `N-Q53` | `O-Q13` | — |
| **U54** | `customer_support` | A | 🅰 | 🥇 | `N-Q54` | `O-Q45` | `T-Q33` |
| **U55** | `research_pipeline` | C | 🅰 | 📘 | `N-Q55` | `O-Q14` | — |
| **U56** | `extraction_pipeline` | A | 🅰 | 🥈🏛 | `N-Q56` | `O-Q48` | — |
| **U57** | `extraction_pipeline` | D | 🅰 | 🥇 | `N-Q57` | `O-Q57` | `T-Q31` |
| **U58** | `customer_support` | B | 🅰 | 🥇 | `N-Q58` | — | `T-Q19` |
| **U59** | `customer_support` | B | 🅰 | 🥇 | `N-Q59` | — | `T-Q36` |
| **U60** | `research_pipeline` | C | 🅰 | 🥇 | `N-Q60` | `O-Q4` | `T-Q32` |
| **U61** | `code_exploration` | B | ✍️ | 🥇 | — | — | `T-Q4` |
| **U62** | `research_pipeline` | C | ✍️ | 🥇 | — | `O-Q15` | `T-Q8` |
| **U63** | `extraction_pipeline` | B | ✍️ | 🥇 | — | — | `T-Q10` |
| **U64** | `extraction_pipeline` | A | ✍️ | 🥇🏛 | — | `O-Q54` | `T-Q13` |
| **U65** | `customer_support` | B | ✍️ | 🥇 | — | `O-Q33` | `T-Q16` |
| **U66** | `code_exploration` | D | ✍️ | 🥇 | — | `O-Q19` | `T-Q22` |
| **U67** | `customer_support` | A | ✍️ | 🥇 | — | `O-Q40` | `T-Q23` |
| **U68** | `customer_support` | C | ✍️ | 🥇 | — | `O-Q42` | `T-Q41` |
| **U69** | `research_pipeline` | A | ✍️ | 🥇 | — | `O-Q3` | `T-Q46` |
| **U70** | `research_pipeline` | A | ✍️ | 🥇 | — | `O-Q7` | `T-Q49` |
| **U71** | `research_pipeline` | C | ✍️ | 🥇 | — | `O-Q1` | `T-Q51` |
| **U72** | `code_exploration` | B | ✍️ | 🥇 | — | `O-Q25` | `T-Q56` |
| **U73** | `research_pipeline` | B | ✍️ | 📘 | — | `O-Q2` | — |
| **U74** | `code_exploration` | A | ✍️ | 📘 | — | `O-Q17` | — |
| **U75** | `code_exploration` | B | ✍️ | 📘 | — | `O-Q28` | — |
| **U76** | `customer_support` | C | ✍️ | 🤔 | — | `O-Q35` | — |
| **U77** | `customer_support` | A | ✍️ | 🤔 | — | `O-Q36` | — |
| **U78** | `customer_support` | B | ✍️ | 📘 | — | `O-Q41` | — |
| **U79** | `extraction_pipeline` | A | ✍️ | 📘 | — | `O-Q47` | — |
| **U80** | `extraction_pipeline` | A | ✍️ | 📘 | — | `O-Q49` | — |

**Cluster shapes:** all three sittings 32 · New+Timed 16 · New+Mock 10 · Mock+Timed 10 · New only 2 · Mock only 8 · Timed only 2.

### Reverse lookup — from a sitting to this folder

**New Mock Exam** (`N-Q#` → `U#`)

> 1→**1** · 2→**2** · 3→**3** · 4→**4** · 5→**5** · 6→**6** · 7→**7** · 8→**8** · 9→**9** · 10→**10** · 11→**11** · 12→**12** · 13→**13** · 14→**14** · 15→**15** · 16→**16** · 17→**17** · 18→**18** · 19→**19** · 20→**20** · 21→**21** · 22→**22** · 23→**23** · 24→**24** · 25→**25** · 26→**26** · 27→**27** · 28→**28** · 29→**29** · 30→**30** · 31→**31** · 32→**32** · 33→**33** · 34→**34** · 35→**35** · 36→**36** · 37→**37** · 38→**38** · 39→**39** · 40→**40** · 41→**41** · 42→**42** · 43→**43** · 44→**44** · 45→**45** · 46→**46** · 47→**47** · 48→**48** · 49→**49** · 50→**50** · 51→**51** · 52→**52** · 53→**53** · 54→**54** · 55→**55** · 56→**56** · 57→**57** · 58→**58** · 59→**59** · 60→**60**

**Mock Exam** (`O-Q#` → `U#`)

> 1→**71** · 2→**73** · 3→**69** · 4→**60** · 5→**32** · 6→**43** · 7→**70** · 8→**48** · 9→**28** · 10→**47** · 11→**25** · 12→**29** · 13→**53** · 14→**55** · 15→**62** · 16→**37** · 17→**74** · 18→**44** · 19→**66** · 20→**1** · 21→**42** · 22→**31** · 23→**38** · 24→**11** · 25→**72** · 26→**50** · 27→**12** · 28→**75** · 29→**39** · 30→**20** · 31→**26** · 32→**2** · 33→**65** · 34→**41** · 35→**76** · 36→**77** · 37→**30** · 38→**15** · 39→**6** · 40→**67** · 41→**78** · 42→**68** · 43→**8** · 44→**16** · 45→**54** · 46→**19** · 47→**79** · 48→**56** · 49→**80** · 50→**49** · 51→**33** · 52→**27** · 53→**35** · 54→**64** · 55→**13** · 56→**21** · 57→**57** · 58→**18** · 59→**51** · 60→**4**

**Timed Mock 2026-08-24** (`T-Q#` → `U#`)

> 1→**40** · 2→**33** · 3→**23** · 4→**61** · 5→**49** · 6→**13** · 7→**19** · 8→**62** · 9→**39** · 10→**63** · 11→**11** · 12→**22** · 13→**64** · 14→**35** · 15→**28** · 16→**65** · 17→**3** · 18→**6** · 19→**58** · 20→**15** · 21→**48** · 22→**66** · 23→**67** · 24→**46** · 25→**27** · 26→**30** · 27→**18** · 28→**14** · 29→**9** · 30→**52** · 31→**57** · 32→**60** · 33→**54** · 34→**26** · 35→**34** · 36→**59** · 37→**31** · 38→**2** · 39→**42** · 40→**1** · 41→**68** · 42→**51** · 43→**10** · 44→**17** · 45→**50** · 46→**69** · 47→**43** · 48→**5** · 49→**70** · 50→**29** · 51→**71** · 52→**38** · 53→**24** · 54→**25** · 55→**20** · 56→**72** · 57→**7** · 58→**44** · 59→**21** · 60→**8**

---

## How to use it

1. **Coverage pass.** Work [Questions.md](Questions.md) Part 1 (`U1`–`U60`) as a timed multiple-choice run — that is every option-bearing item the bank has, with no repeats.
2. **Then Part 2** (`U61`–`U80`) as open response. Twenty items you cannot meet any other way, and stating the principle out loud is the harder, better drill.
3. **Grade** against the four domain keys. Each entry shows every sitting the item came from, so a miss tells you whether you have now missed it once or three times.
4. **Log misses** in [[Weak Areas Deep Dive]], then drill the whole pattern via [[Answer Patterns Index]].
5. **Where a key here disagrees with the [official sample questions](../Exam%20Guide%20-%20Sample%20Questions/Answer%20Key.md), the official key wins.** No exceptions — this folder is third-party material.

> [!TIP] What this folder is good for, and what it isn't
> ✅ **Coverage without repetition** — 80 items instead of 180 slots, and the per-item provenance tells you how well-attested each answer is.
> ❌ **Not a substitute for a timed sitting.** The three source folders each preserve a real 60-question run with its own internally consistent numbering. Sit those for exam simulation; use this to make sure you have met every item at least once.

---

## Provenance and method

Built by matching all 180 stems pairwise (TF-IDF cosine over content words, domain-weighted), then resolving matches into clusters. The result is unusually clean: matched pairs scored **≥ 0.455**, the best non-match scored **0.275** — nothing in between. The mapping was then checked three ways, all of which passed:

- **Bijection** — no sitting maps two of its questions onto one question of another. 
- **Transitivity** — 32 clusters contain all three sittings, giving 32 independent triangles. All 32 close consistently; zero contradictions. 
- **Independent agreement** — 180 domain labels agree across every cluster, and 132 cross-sitting answer comparisons agree on both text and letter.

**Merge rules.** Stem: New Mock verbatim → else Timed Mock verbatim → else the Mock Exam paraphrase. Options: New Mock only (no other source quotes them). Answer: Timed Mock's `correct_key` → else New Mock's grader-confirmed letter → else the Mock Exam key. Explanation: New Mock's worked entry (it rebuts every distractor), plus the Mock Exam's **Sources** block where one exists, plus the Timed Mock's warning callouts. Every explanation is transplanted from an already-verified key — none was rewritten for this folder.

Two small repairs were applied. `U48`'s stem had lost its `$` symbols in the original text extraction (`35B` → `$35B`), restored here from the Mock Exam sibling and matching the precedent set in [../CyberSkill CCAF - Timed Mock 2026-08-24/Questions.md](../CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Questions.md). And cross-references inside transplanted explanations originally pointed at their own sitting's numbering; all 42 were remapped to `U#`.

**Answer-letter distribution:** A 16 · B 33 · C 19 · D 12 — across 80 items.

**Back to:** [../README.md](../README.md) · [[00 - START HERE]] · [[Answer Patterns Index]] · [[Weak Areas Deep Dive]]