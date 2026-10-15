# Reflection Brief — Harness Engineering Capstone

**Name:** Piyush
**Date:** 2026-09-29

Every answer below cites a concrete artifact from this project or its verification evidence.

## Environment

- Model(s): `claude-haiku-4-5-20251001` in the recorded System 1 run; System 2 artifacts identify the Anthropic token-counting methodology. (`evidence/system1_agentic_loop/summary.md`, `evidence/system2_context_strategy/budget.json`)
- OS / Python: Linux; Python 3.13 in the local verification environment. (`evidence/test_evidence/system1_claims_tests.log`, `system3_config_tests.log`, `system4_monitor_tests.log`)
- Approx. API spend: System 1 recorded **$0.1142 USD** for eight fixtures. (`evidence/system1_agentic_loop/summary.md`)

---

## Part 1 — Per-system

### System 1 — Agentic loop

1. **Loop control.**
   → In `evidence/system1_agentic_loop/claim_01_kitchen_fire.jsonl`, the trace has the sequence `tool_use → end_turn`. The decision is implemented in `Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/01-loop/solution/claims_intake/loop.py`: `while True` continues only for `response.stop_reason == "tool_use"`, returns on `"end_turn"`, and raises `UnexpectedStopReason` otherwise. The same file calls `budget.check()` before each turn, so the budget is a safety mechanism rather than the primary loop condition.

2. **Anti-pattern.**
   → `Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/01-loop/solution/tests/test_antipatterns.py` checks that the loop does not use string-membership tests against assistant text and does not use an integer-literal iteration cap. The 2026-09-29 verification log shows these static checks passing as part of **30 passed** tests (`evidence/test_evidence/system1_claims_tests.log`). If the loop used text matching such as `"DONE" in response_text`, control flow could depend on natural-language wording instead of the API's structured `stop_reason`.

3. **Tool design.**
   → `Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/01-loop/solution/claims_intake/tools.py` distinguishes `lookup_policy` from `record_claim_fact` through explicit descriptions and required input schemas, while classification and routing use explicit enums for claim type. The tool dispatcher returns structured JSON errors such as `is_error`, `error_category`, `is_retryable`, and `message`; `tests/test_tools.py` verifies graceful errors (`evidence/test_evidence/system1_claims_tests.log`). That lets the model inspect a specific error category/message and adapt instead of receiving an opaque string or crashing the loop.

4. **Your numbers.**
   → For `claim_01_kitchen_fire`, the recorded run has **2 turns**, **6,281 input tokens**, **425 output tokens**, and estimated cost **$0.0084** (`evidence/system1_agentic_loop/summary.md`). Its trace shows 2,809 input / 352 output tokens on turn 1 and 3,472 input / 73 output on turn 2 (`evidence/system1_agentic_loop/claim_01_kitchen_fire.jsonl`). These numbers differ from any README example because they are measurements from this project's actual run, model configuration, tool calls, and transcript state rather than a fixed illustrative sample.

### System 2 — Context strategy

5. **The reduction.**
   → `evidence/system2_context_strategy/budget.json` records **38,708 baseline tokens**, **16,911 assembled tokens**, and a **56.31% reduction**. The `active` section dominates at **15,789 tokens**, compared with 204 case-facts, 415 resolved-refund, and 521 resolved-subscription tokens. The active issue is kept verbatim because it is still being resolved and the solution README explicitly treats its turn-by-turn details as decision-load-bearing (`Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution/README.md`).

6. **Summarize vs preserve.**
   → The strategy summarizes resolved issues and preserves the active issue byte-exact, while keeping a compact structured case-facts block at the top. The measured section sizes are **204 / 415 / 521 / 15,789 tokens** for case facts / refund / subscription / active (`evidence/system2_context_strategy/budget.json`). This follows the implementation rule documented in `04-assemble-and-locate/solution/README.md`: resolved narrative can tolerate compression after facts stabilize, while the active issue remains verbatim.

7. **Facts block.**
   → The engineered context's `eval.jsonl` passes **Q6**, returning the exact structured status token `in_progress`; the control file `eval_control.jsonl`, which removes the case-facts block, fails Q6 and says the token is not present (`evidence/system2_context_strategy/eval.jsonl`, `evidence/system2_context_strategy/eval_control.jsonl`). Q1 passes in both variants because `$22.14` survived in a resolved summary. This demonstrates that the case-facts block is specifically load-bearing for the structured status token, not that every answer requires it. The full assembled-context evaluation passes **6/6** questions; the control evaluation passes **1/2** questions, with **Q6 regressing** when the Case Facts block is removed (`evidence/system2_context_strategy/eval.jsonl`, `eval_control.jsonl`).

### System 3 — Claude Code config

8. **Path-scoped rules.**
   → `evidence/system3_claude_config/evidence_rule_react.md` begins with `paths:` followed by `src/components/**/*` and `src/pages/**/*`. The project `CLAUDE.md` explains that `.claude/rules/` is preferred over a directory-level `CLAUDE.md` for cross-cutting conventions because the same rule can activate across matching paths throughout the repository. This is also verified by `test_us02_path_scoped_rules.py` in the **35 passed** suite (`evidence/test_evidence/system3_config_tests.log`).

9. **Forked skill.**
   → `evidence/system3_claude_config/evidence_rule_skill.md` declares `context: fork` and an `allowed-tools` list containing only `Read`, `Grep`, `Glob`, and read-oriented Git/GitHub commands. The skill's rationale says the fork keeps verbose discovery output out of the main session, while the read-only allowlist prevents the skill from modifying files, pushing, or deploying. Without the fork, intermediate deploy-check output would pollute the main context; without the allowlist, the task would have a larger write/side-effect surface.

10. **Scope.**
    → `evidence/system3_claude_config/CLAUDE.md` identifies `./CLAUDE.md`, `.claude/standards/`, and `.claude/rules/` as project-level configuration, while `~/.claude/CLAUDE.md`, `~/.claude/commands/`, and `~/.claude/skills/` are user-level and intentionally not versioned. The concrete project-level example is the repository root `CLAUDE.md`; the concrete user-level example documented by the config is `~/.claude/CLAUDE.md`. The configuration validator artifact reports `OK` (`evidence/system3_claude_config/validator_output.txt`).

### System 4 — Orchestration

11. **Push work down.**
    → The warm SQLite database contains **40** defect rows. The refreshed offline Shift C run uses the SQL predicate `ts > 2026-04-01T00:00:00Z`, selects **17 of 40** rows, and reports **17 new defects** (`evidence/system4_orchestration/sql_filtered_slice.txt`, `evidence/system4_orchestration/shift_run_output.txt`). `shift_monitor/warm.py` implements `SELECT * FROM defects WHERE ts > ? ORDER BY ts DESC LIMIT ?`, and the query plan uses `idx_defects_ts`; the corresponding test guarantees SQL-side filtering rather than loading and filtering the full table in Python (`evidence/test_evidence/system4_monitor_tests.log`). `shift_monitor/pipeline.py` passes only `gather_new_defects(...)` into the rich prompt, so the model does not receive the full warm history.

12. **Crash recovery.**
    → `shift_monitor/recovery.py` uses a **30-minute** `STALE_RESUME_THRESHOLD_MINUTES`: incomplete state newer than 30 minutes resumes, while empty, complete, or stale state starts fresh. The System 4 suite explicitly passes the truth-table cases around 29, 30, and 31 minutes and checks the 30-minute constant (`evidence/test_evidence/system4_monitor_tests.log`). A fresh start can be more reliable when the partial state is stale because the system can rebuild from persisted warm data and inject the already-captured summary instead of continuing a potentially obsolete session.

13. **Small state.**
    → `evidence/system4_orchestration/hot_state_size.txt` records `data/hot_state.json` at **643 bytes**. The implementation sets a **5,120-byte** hot-state budget in `shift_monitor/state.py` and writes atomically after validating the size. For a system that runs once per shift indefinitely, a bounded hot state prevents prompt/state growth from becoming an unbounded cross-shift cost and keeps the in-context working set predictable.

---

## Part 2 — Synthesis

14. **Three layers.**
    → **Model:** `Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/01-loop/solution/claims_intake/system_prompt.py` and `tools.py` define the domain instructions and tool schemas the model reasons over; the recorded trace shows the resulting tool-use decisions (`evidence/system1_agentic_loop/claim_01_kitchen_fire.jsonl`).
    → **Harness:** `Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution/CLAUDE.md` plus `.claude/rules/` and `.claude/skills/` define deterministic session/configuration behavior; the validator reports `OK` (`evidence/system3_claude_config/validator_output.txt`).
    → **Orchestration:** `Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution/shift_monitor/pipeline.py` coordinates SQL retrieval, prompt construction, one model call, hot-state update, and scratchpad persistence; System 4's **33 tests passed** (`evidence/test_evidence/system4_monitor_tests.log`).

15. **Deterministic vs prompt.**
    → A deterministic example is System 4's `HotState.write_atomic()` in `shift_monitor/state.py`: it rejects payloads over 5,120 bytes and uses `fsync` plus `os.replace`; the suite directly tests the atomic-write behavior (`evidence/test_evidence/system4_monitor_tests.log`). A prompt-guided example is System 1's `system_prompt.py`, which tells the model to classify once, assess severity once, and choose exactly one terminal tool. Deterministic enforcement is appropriate when violating the rule must be mechanically impossible; prompt guidance is appropriate when the model must exercise domain judgment among valid actions.

16. **Context, two faces.**
    → System 2 manages **intra-session** context by reducing a **38,708-token** baseline to **16,911 tokens (56.31% reduction)** while preserving the **15,789-token active segment** verbatim (`evidence/system2_context_strategy/budget.json`). System 4 manages **cross-session/shift** context by keeping a **643-byte** hot state and querying only new warm-tier defects, with a 30-minute resume/fresh boundary (`evidence/system4_orchestration/hot_state_size.txt`, `shift_monitor/recovery.py`). The shared principle is to preserve a small set of decision-load-bearing state while discarding or summarizing material that no longer needs full fidelity; the mechanism differs because System 2 compresses one conversation, while System 4 persists and reloads state between shifts.

17. **Reliability you can't see in one run.**
    → System 4's crash-recovery tests guarantee behavior across multiple states that one successful shift run would not establish: incomplete manifests resume only within the 30-minute threshold, stale partials start fresh, and mid-write reads retain prior complete lines (`evidence/test_evidence/system4_monitor_tests.log`). A single successful run cannot demonstrate those boundary cases because it does not exercise crashes, partial writes, or stale state. This matters before shipping because those are exactly the states that occur outside the happy path.

18. **Blast radius.**
    → For System 4, a bad model response is constrained by the orchestration boundary: the model receives only the SQL-selected defects and hot state, while `pipeline.py` parses the response, validates the resulting `HotState`, enforces the 5,120-byte budget, writes atomically, and appends a scratchpad record. The main hard stop is the hot-state size check in `shift_monitor/state.py`; the test suite also verifies the invocation shape and state-write behavior (`evidence/test_evidence/system4_monitor_tests.log`). Because warm history is retained separately in SQLite, a bad hot-state update does not require rewriting the entire historical store.

---

## Part 3 — Honest assessment

19. **What broke.**
    → The first fresh System 1 verification failed **15 tests** because `01-loop/solution/claims_intake/tools.py` and its anti-pattern tests still contained exercise TODOs; after replacing those with the completed implementations, the suite reached **30 passed** (`evidence/test_evidence/system1_claims_tests.log`). System 2 initially failed collection because the local environment lacked the declared `anthropic==0.69.0` SDK. The code now treats the SDK as an optional import for offline unit-test collection and raises a clear runtime error if API inference is requested without the SDK. No stub was used for the final test run; the suite passed **30 tests** (`evidence/test_evidence/system2_context_tests.log`). A live model evaluation still requires the SDK and valid API credentials.

20. **What you'd change.**
    → I would make the System 2 evaluation contract more explicit about canonical facts: the current control result shows Q1 still passes after the case-facts block is removed because `$22.14` survived in a resolved summary, while Q6 fails because `in_progress` exists only in the structured facts (`evidence/system2_context_strategy/eval.jsonl`, `eval_control.jsonl`). I would therefore add more evaluation questions whose answers depend on exact structured fields and ensure resolved summaries do not accidentally become a second canonical source for those fields. That would make the boundary between “durable facts” and “compressed narrative” easier to verify automatically.


**Fork isolation.** `shift_monitor/fork.py::fork_for_hypothesis` creates a per-hypothesis directory under `data/forks/<hypothesis_id>/` and copies the base hot state into that fork. The investigation writes to its own scratchpad, not the main `data/hot_state.json`. `tests/test_us04_fork_scratchpad.py` checks that main-session findings remain present and fork findings are merged without replacing existing findings; `evidence/system4_orchestration/fork_evidence.md` records the test behavior.
