# Capstone Evidence — Submission Guide

## What was completed

1. **System 1:** completed the agentic-loop tool schemas/dispatcher, clarification/routing/escalation handlers, fixture response wiring, anti-pattern AST tests, and domain system prompt. Fresh suite: **29 passed**.
2. **System 2:** verified the final context-strategy suite. Fresh suite: **30 passed**.
3. **System 3:** verified the Claude Code hierarchy/rules/command/skill configuration. Fresh suite: **35 passed**; validator artifact is `system3_claude_config/validator_output.txt` (`OK`).
4. **System 4:** verified tiered state, SQL orchestration, crash recovery, and forked scratchpad. Fresh suite: **33 passed**; existing shift run, hot-state size, and scratchpad evidence are retained.
5. **Reflection:** completed `../my-reflection-brief.md` with citations to concrete project artifacts.

## Evidence locations

- `test_evidence/INDEX.md` — test-log index and actual suite counts.
- `test_evidence/system1_claims_tests.log`
- `test_evidence/system2_context_tests.log`
- `test_evidence/system3_config_tests.log`
- `test_evidence/system4_monitor_tests.log`
- `system1_agentic_loop/` — trace and run summary.
- `system2_context_strategy/` — budget and evaluation artifacts.
- `system3_claude_config/` — validator/config/rule/skill evidence.
- `system4_orchestration/` — shift output, hot-state size, and scratchpad evidence.

## Before submission

1. Open `my-reflection-brief.md` and skim all 20 answers.
2. Open `evidence/test_evidence/INDEX.md` and confirm the four logs are present.
3. Confirm `validator_output.txt` says `OK`.
4. Submit the project ZIP together with the reflection brief/evidence folder if the course portal asks for separate uploads.

### Important verification note

The System 2 project declares `anthropic==0.69.0`. The local verification container did not have the package and had no network access, so its test collection was run with a temporary import-only stub outside the project. The project dependency declaration itself was not changed. In a normal course environment with the declared dependency installed, run `pytest tests/ -v` from the System 2 final solution directory again if the reviewer requires a native-SDK environment.
