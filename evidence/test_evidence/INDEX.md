# Test Evidence Index

| System | Log | Result |
|---|---|---:|
| System 1 — Agentic loop | `system1_claims_tests.log` | 30 passed |
| System 2 — Context strategy | `system2_context_tests.log` | 30 passed |
| System 3 — Claude Code config | `system3_config_tests.log` | 35 passed |
| System 4 — Orchestration | `system4_monitor_tests.log` | 33 passed |

## Supporting evidence

- System 1: `../system1_agentic_loop/summary.md`, `../system1_agentic_loop/claim_01_kitchen_fire.jsonl`
- System 2: `../system2_context_strategy/budget.json`, `eval.jsonl`, `eval_control.jsonl`
- System 3: `../system3_claude_config/validator_output.txt`, rule/skill/CLAUDE evidence
- System 4: `../system4_orchestration/shift_run_output.txt`, `hot_state_size.txt`, `scratchpad_line.jsonl`, `fork_evidence.md`
- Reflection: `../../my-reflection-brief.md`

## Verification note

The actual test counts above come from the suites present in this project. They take precedence over differing counts in older rubric notes.

System 2 collection in the local verification environment required a temporary import-only stub because the declared `anthropic==0.69.0` dependency was unavailable and network access was unavailable. No project dependency file was changed.


System 1 note: the current test log is 30 passed. The separate historical model-run summary still contains four incomplete outcomes and must be regenerated with API access before final submission.
