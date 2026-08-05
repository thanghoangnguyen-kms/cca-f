---
tags:
  - CCA-F
  - domain-2
  - built-in-tools
  - codebase-exploration
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "2 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 09"
---

# 🃏 EP09 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP09 — Claude Built-in Tools** (Domain 2, task statement 2.5, with Domain 5 § 5.4 overlap). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry a correction or expansion verified against official Anthropic documentation that the lecture did not cover. Where the exam answer and real Claude Code behaviour differ, both are given and labelled.

**Related:** [[EP09 - Claude Built-in Tools]] · [[D2 - Tool Design & MCP Integration]] · [[D5 - Context Management & Reliability]] · [[EP08 - Flashcards]]

---

## Domain 2 — The Six Tools

**Q: Name the six built-in tools in task statement 2.5's scope, and the one-word job of each.**
A: `Grep` (search contents) · `Glob` (match paths) · `Read` (load) · `Write` (create/replace) · `Edit` (patch) · `Bash` (execute).

**Q: Claude Code's tools reference lists roughly forty built-in tools. Why does the exam say six? (docs)**
A: The six are the file/search/execution core that task statement 2.5 scopes. The full set also includes `Agent`, `Skill`, `WebFetch`, `WebSearch`, `LSP`, `NotebookEdit` and more. **Exam answer: six.** Don't assert a total count in real work without checking the reference.

**Q: Using `Write` to change three lines of a 500-line file succeeds. Name the two costs anyway.**
A: You pay tokens to regenerate 497 lines you never intended to change, and every one of those lines passes back through the model where it can be subtly altered.

**Q: A tool-selection question hinges on how large the change is. What is wrong with that reasoning?**
A: Size is the wrong axis. `Write` vs `Edit` is decided by whether the file already exists and whether a unique anchor is available — a one-line change to a nonexistent file still needs `Write`.

---

## Domain 2 — Grep and Glob

**Q: `Grep` returns a list of filenames. Why is it still wrong to call it a file-finding tool?**
A: Filenames are its **output**, never its input. It matches on file *contents* and reports where it found them — it can't select files by name at all.

**Q: What does `Glob` never do, and what follows from that limitation?**
A: It never opens a file. So it can tell you what exists and where, but nothing about what any file contains — it matches labels, not documents.

**Q: Both `Grep` and `Glob` return lists of files. What single test discriminates between them?**
A: Ask what you are matching **on**. Content inside the file → `Grep`. Name, extension, or path → `Glob`. What comes back is identical either way.

**Q: "Find every file that imports React" vs "find all TypeScript files in /src" — same tool or different, and why?**
A: Different. The first matches on `import React`, a line of source → `Grep`. The second matches on the `.ts` extension, part of the name → `Glob`. The word "file" in both is noise.

**Q: "Find all TODO comments" — which tool, and what makes it unambiguous?**
A: `Grep`. A comment is text written inside a file, so the matching criterion is content.

**Q: A `Glob` for `**/*.js` in a Node repo returns hundreds of `node_modules/` hits, but a `Grep` over the same tree returns none. Why? (docs)**
A: `Grep` respects `.gitignore` and skips ignored files. `Glob` does **not** by default — it returns gitignored files alongside tracked ones. Set `CLAUDE_CODE_GLOB_NO_IGNORE=false` to change it.

**Q: A `Glob` returns exactly 100 files. Why should that number make you suspicious? (docs)**
A: `Glob` caps results at 100 and flags the truncation — exactly 100 usually means there are more. Narrow the pattern rather than treating the list as complete.

**Q: In what order does `Glob` return results, and why is that useful? (docs)**
A: By modification time, not alphabetically. Recently-touched files surface first, which is usually what you want when investigating a recent change.

**Q: The lecture calls `Grep` "the normal Linux grep command." What is the one practically important way that's wrong? (docs)**
A: It's built on **ripgrep** and uses ripgrep's regex dialect, not POSIX. Metacharacters escape differently — finding `interface{}` in Go needs the pattern `interface\{\}`.

**Q: `Grep`'s default output mode returns file paths only, not matching lines. Why is that the right default? (docs)**
A: The next step is almost always a `Read` of one file, so paths are the cheapest result that still identifies the target. Returning full matching lines would spend context on files about to be discarded.

**Q: Name `Grep`'s three output modes. (docs)**
A: `files_with_matches` (default — paths only), `content` (matching lines with file and line number), `count` (matches per file).

---

## Domain 2 — Bash vs the Dedicated Tools

**Q: `Bash` can run `grep`. Give three concrete reasons the dedicated `Grep` tool is preferred. (docs)**
A: (1) `Grep` needs no permission prompt inside the working directory; `Bash` does. (2) `Grep` returns structured output in defined modes; shell output is unbounded text truncated at 30,000 characters. (3) ripgrep semantics are identical everywhere; shell `grep` varies by platform and by the user's aliases.

**Q: Which built-in tools run without a permission prompt inside the working directory, and which prompt? (docs)**
A: No prompt: `Read`, `Grep`, `Glob`. Prompt: `Write`, `Edit`, `Bash` — though `Bash` runs a built-in set of read-only commands without asking.

**Q: An answer option reads "use the Bash tool to grep the codebase." How should you treat it?**
A: As a distractor. It works in practice but is the wrong answer in a tool-selection question — a dedicated tool exists for that job.

**Q: What is `Bash` genuinely the right tool for?**
A: Running tests, installing packages, git operations, and invoking CLIs — anything the other five tools don't cover. Not searching, not reading, not editing.

**Q: An agent runs `export API_TOKEN=abc` via `Bash`, then a second command reads `$API_TOKEN` and gets nothing. Why? (docs)**
A: Each `Bash` call runs in a separate process and environment variables don't persist between them. Confusingly, a `cd` *does* carry over as long as it stays inside the project directory.

**Q: What happens when a `Bash` command exceeds its timeout? (docs)**
A: It's moved to the **background**, not killed — Claude keeps working while it runs to completion. Default timeout is two minutes, raisable to ten via the `timeout` parameter.

---

## Domain 2 — Read and Write

**Q: What must already be true before `Read` is the correct tool?**
A: You already know which file you want — normally because a `Grep` or `Glob` just identified it. Reading a merely-suspected file is bulk reading in miniature.

**Q: A `Read` comes back with a `PARTIAL view` notice. What happened, and what does it block? (docs)**
A: The whole-file read would exceed the token limit, so only the first page returned, with instructions to continue via `offset` / `limit`. It also does **not** satisfy the read-before-edit requirement, so a following `Edit` still needs a proper read.

**Q: An agent calls `Read` on a directory path to list its contents. What happens? (docs)**
A: It fails — `Read` reads files only. Listing a directory means running `ls` through `Bash`.

**Q: Give the two situations that make `Write` correct rather than `Edit`.**
A: (1) The file doesn't exist yet — `Write` is the only tool that creates files. (2) The changes are extensive enough that regenerating the whole file is cleaner than patching it.

**Q: A `Write` to an existing file fails even though the path and content are valid. What is the most likely cause? (docs)**
A: The file hasn't been read in the current conversation. Overwriting an existing path requires a prior `Read`; brand-new files are exempt.

**Q: "Write is destructive — old contents are lost." In what sense is that overstated? (docs)**
A: True of the file on disk, but Claude Code snapshots a file before changing it, so a `Write` or `Edit` is reversible with `Esc` `Esc` / `/rewind`, independently of git.

---

## Domain 2 — Edit and the Fallback

**Q: What are the two distinct ways an `Edit` can fail on its `old_string`?**
A: Zero matches (the anchor text isn't in the file — often a whitespace or indentation difference), or more than one match (the tool can't know which occurrence you meant).

**Q: `Edit` refuses when the anchor matches seven times. Why is refusing correct rather than a limitation?**
A: There is no way to infer which occurrence was intended, and silently picking one would corrupt the file in a hard-to-notice way. Refusing surfaces the ambiguity to whoever can resolve it.

**Q: Name the three checks that must all pass before an `Edit` applies. (docs)**
A: **Read-before-edit** (the file was read this conversation), **match** (`old_string` appears exactly as written, byte for byte), **uniqueness** (it appears exactly once, unless `replace_all` is set).

**Q: State the exam-safe recovery when an `Edit` fails on a non-unique anchor.**
A: `Read` the full file → modify it in memory → `Write` the whole updated file back. No uniqueness requirement, so it always works.

**Q: What is the trade-off of the read-then-write fallback versus a successful `Edit`?**
A: You spend tokens proportional to the whole file rather than the fragment, and every line passes back through the model — in exchange for a path that cannot fail on ambiguity.

**Q: Why is the fallback ordered read-then-write rather than any other sequence?**
A: Two reasons. Enforced: overwriting an existing file requires a prior `Read` in the same conversation, so a `Write` first would fail outright. Practical: having the whole file in context is what lets the agent correctly pick among the occurrences that defeated `Edit`.

**Q: Besides the read-then-write fallback, what two remedies do the official docs give for a non-unique anchor? (docs)**
A: Supply a **longer `old_string`** with enough surrounding context to pin one occurrence (the lecture's pro tip), or set **`replace_all: true`** to change every occurrence at once. `replace_all` is absent from the lecture — exam answer stays read-then-write.

**Q: You must change one of four identical `timeout = 30` lines. Why is `replace_all` wrong here?**
A: It would change all four. `replace_all` fits "rename this everywhere in the file", not "change this one instance" — for that, widen the anchor to include the enclosing class or function line.

**Q: Which `Bash` commands satisfy the read-before-edit requirement, and which don't? (docs)**
A: `cat`, `head`, `tail`, `sed -n 'X,Yp'`, `grep`, `egrep`, `fgrep` — on a **single file with no pipes or redirects**. Piped output and any other command don't count.

**Q: The Claude API's text editor tool has the same uniqueness rule as Claude Code's `Edit`. Why does that matter? (docs)**
A: It shows the rule is a deliberate design principle, not a Claude Code quirk — `str_replace` on `str_replace_based_edit_tool` also requires exactly one match and errors on multiple.

---

## Domain 5 — Codebase Exploration

**Q: An agent is told "understand the billing system" and immediately reads all 25 files in the module. Name the anti-pattern and its failure mode.**
A: Bulk reading. The context window is consumed before any reasoning happens, most loaded files turn out irrelevant, and agent quality degrades as context fills.

**Q: State the incremental exploration loop in order.**
A: `Grep` for an entry point → `Read` the file it surfaced → trace what that file references → `Grep` for the next thing. Repeat.

**Q: Why does incremental exploration compound, rather than just being cheaper per step?**
A: Each step produces the next step's input — every search names the file worth reading, and every read reveals the next symbol worth searching for. Every file entering context was referenced by something already verified relevant. Bulk reading has no such filter.

**Q: An answer option contains the phrase "read all files first." What should that trigger?**
A: Treat it as a distractor marker — it is almost certainly the wrong answer in a codebase-exploration question.

**Q: Name the three phases of the exam's developer-productivity scenario and the tools in each.**
A: **Discovery** — `Glob` maps the folder structure. **Understanding** — `Grep` pinpoints, `Read` loads, then trace and repeat. **Action** — `Write` creates new files, `Edit` patches existing ones.

**Q: In that scenario, the agent uses `Write` for a new endpoint and `Edit` for a legacy constant. What decides the split?**
A: A property of the target, not the size of the change. The endpoint file doesn't exist, and `Write` is the only tool that creates files. The constant is a targeted change to an existing file with a unique anchor — `Edit`'s lane.

**Q: 30 files at ~5,200 tokens each; a trace touching 3 of them; ~200 tokens per search. Roughly how much does incremental exploration save over bulk reading?**
A: Bulk = 30 × 5,200 = 156,000 tokens. Incremental = 3 × (200 + 5,200) = 16,200. About **9.6× cheaper** — because 27 of the 30 files were never relevant, and bulk reading pays full price before discovering that.

---

*Back to: [[EP09 - Claude Built-in Tools]]*
