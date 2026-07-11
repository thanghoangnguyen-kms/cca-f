# 08 — Quick-Reference Tables (all in one place)

Print this and [`00`](00-golden-rules-cheatsheet.md). Everything factual you might need to recall fast.

---

## 1. The agentic loop
1. Send request → 2. check `stop_reason` → 3. if `tool_use`, execute tool(s) →
4. append **assistant message + tool results** → 5. repeat until **`end_turn`**.
Handle **all** `tool_use` blocks in a response; return **all** `tool_result`s.
`MAX_ITERATIONS` = safety ceiling only.

## 2. `tool_choice` modes
| Mode | Text reply? | Must use a tool? | Specific tool? |
|---|---|---|---|
| `auto` | Yes | No | No |
| `any` | No | Yes | No |
| `tool` | No | Yes | **Yes (named)** |
| `none` | Yes | No | No tools offered |

Force order: `tool` on turn 1 → `auto` afterward.

## 3. Structured error categories
| Category | Meaning | Action |
|---|---|---|
| `transient` | timeout, rate limit | retry (backoff) |
| `validation` | bad input | fix input, retry |
| `business` | rule violated | explain to user, **no retry** |
| `permission` | access denied | escalate/stop, **no retry** |

Always set `isError`/`is_error = true` + readable message. Structured error
fields: `errorCategory` + `isRetryable` + cause (+ customer-friendly message).
"Order not found" may be a valid empty result, not an error.

## 4. Sessions
| Action | Command | Use when |
|---|---|---|
| Resume specific | `--resume <name/id>` | Continue a particular past session |
| Resume most recent | `--continue` | Continue the latest in this dir |
| Fork | `fork_session` | Branch alternatives from a shared baseline; original preserved |
| Fresh start | (new session) | Old context noisy/wrong or need isolation |

On resume with changes → **communicate the delta** (which files changed).

## 5. Hooks
| Hook | Fires | Use for |
|---|---|---|
| **PreToolUse** | before a tool runs | block/validate (compliance gates) |
| **PostToolUse** | after a tool runs | normalize output, log, fix format |

Prompt = soft guidance; **hook/code = enforcement.**

## 6. Escalation triggers
✅ Explicit human request · policy-exception need · no meaningful progress ·
repeated failure / retry exhausted · calibrated confidence/validation threshold ·
tool returns permission/business error.
❌ Sentiment · fixed failed-call counter · uncalibrated confidence · giant rules engine.

## 7. Batch vs synchronous
| | Batch API | Synchronous API |
|---|---|---|
| Cost | **50% off** | full |
| Latency | up to **24h** (or when all done) | immediate |
| Use for | high-volume, offline, no SLA pressure | latency-bound, user waiting |

**SLA math**: `worst case = max wait before batching + 24h`. Keep margin
(SLA 30h → batch every 4h = 28h). `custom_id` matches results (order not
guaranteed) & retries only failures. A context-length error
(`context_length_exceeded` — name illustrative/unverified) → **chunk input**
(not `max_tokens`, which is output).

## 8. Tool count per agent
~**4–5 tools per agent**. More → split into specialized agents. Avoid "god agent".
Wrong tool used → **architecture first** (reduce/split) → **tool description** →
**prompt last**.

## 9. Structured output recipe
Define tool `input_schema` → force with `tool_choice` → **validate** → on fail,
feed error into a **retry loop**. Schema = structure only → add **semantic
validation**; computed fields need **reasoning/scratchpad**.

## 10. Few-shot construction order
System context → example user msg → example assistant answer → repeat a few →
**real input last.** Make examples **diverse** (cover edge cases), `<example>` tags.
Fixes: interpretation, format, recall. Does **not** fix missing data or logic.

## 11. Grounding / hallucination
Nullable + inventing values → "**return null when not stated**". Required fields
**force** invention. Retry-with-feedback fixes **format/shape**, not **absent
info**. Long docs → surface **supporting quotes/locations**.

## 12. Schema design patterns
| Situation | Pattern |
|---|---|
| Multiple valid values (amendments) | value + **source location + effective date** |
| Enum fails on new values | **`"other"` + `*_detail` string** |
| Numbers must reconcile | **computed + stated**, flag mismatch |
| Messy source formatting | strict schema + **normalization rules in prompt** |

## 13. Review / evaluation
Limited reviewers → **calibrated field-level confidence** routing (not random
sampling). Before automating → **disaggregate by segment × field** (aggregate
hides failures); **stratified sampling**. Validation = known errors; evaluation +
human review = new errors/drift. **Continuously monitor** production.

## 14. Context management
Don't dump a large codebase into one window → **multi-pass** (focused pass per
concern → synthesis pass on summaries). Long tasks → **scratchpad /
compaction / subagents** (fresh clean window). Bigger window only delays
degradation.

## 15. Just-in-time exploration
**Grep entry points → read → follow imports** incrementally. Enumerate aliases
(read re-exports) before grepping for callers. Explore from architectural root
outward. Unknown bug → **adaptive, evidence-driven** decomposition.

## 16. Default tool selection
Edit small change / large file few sections · Write new file / full overwrite ·
Read before edit · Grep text across files · Glob find by name · Bash
run/test/build/lint · `replace_all:true` for all occurrences of one exact string ·
avoid `sed -i`.

## 17. CLAUDE.md vs settings.json
**CLAUDE.md** (committed) = architecture rules, naming, forbidden patterns,
testing expectations. **`.claude/settings.json`** = enables/gates MCP servers
(defined in `.mcp.json`, e.g. via `enabledMcpjsonServers`), plus env vars,
commands/args.

## 18. CI / plan mode / `--bare`
- CI non-interactive → **`claude -p --output-format json`** (missing `-p` = hang).
- **Plan mode** → unfamiliar codebase / high-risk / needs review / align architecture.
- **`--bare`** skips: hooks, LSP, plugin sync, attribution, auto-memory,
  background prefetch, keychain reads, CLAUDE.md auto-discovery.

## 19. Skills frontmatter
`name` · `description` (when to use) · `context` (`fork` = separate subagent;
omit = main conversation — `inherit` is not a `context` value) · `allowed-tools`
· `argument-hint`.

## 20. The golden rule (again — it's that important)
**MUST always happen → CODE.** **Usually right → PROMPT.**
Architecture before prompt. Structure survives; prose is dropped. Ground to the
source; prefer "unknown" over a guess.
