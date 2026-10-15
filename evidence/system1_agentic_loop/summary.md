# Run summary — 20260925_082413

- Model: `claude-haiku-4-5-20251001`
- Fixtures processed: 8
- Estimated total cost: **$0.1142** USD

| claim_id | outcome | claim_type | severity | turns | clarifications_asked | input_tokens | output_tokens | est_cost_usd | elapsed_s |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| claim_01_kitchen_fire | incomplete | - | - | 2 | 0 | 6281 | 425 | 0.0084 | 4.2 |
| claim_02_stolen_bike | incomplete | - | - | 2 | 0 | 6154 | 448 | 0.0084 | 4.5 |
| claim_03_water_damage | incomplete | - | - | 2 | 0 | 6224 | 446 | 0.0085 | 4.2 |
| claim_04_neighbor_injury | routed | liability | medium | 4 | 0 | 14615 | 953 | 0.0194 | 12.0 |
| claim_05_auto_collision | routed | auto | high | 3 | 0 | 11066 | 1055 | 0.0163 | 9.4 |
| claim_06_low_confidence_escalation | escalated | property_damage,auto,liability | - | 5 | 1 | 18806 | 1430 | 0.026 | 17.9 |
| claim_07_tree_falls_on_car | incomplete | - | - | 2 | 0 | 6298 | 469 | 0.0086 | 5.5 |
| claim_08_minor_porch_damage | routed | property_damage | low | 4 | 0 | 14398 | 847 | 0.0186 | 9.1 |


## Important verification note

This is the recorded 2026-09-25 model run, not a regenerated run after the current test fix. Four claims ended `incomplete` in that historical artifact, so it does **not** satisfy the requirement that every claim terminates in routing or escalation. The updated test suite now has 30 passing tests; regenerate this model-run artifact with valid API access before claiming all eight claims terminate correctly.


**Live-run limitation:** This is a historical model run. Four claims are `incomplete`, so this artifact does not yet demonstrate that all eight claims reached routing or escalation. A fresh run with valid Anthropic API credentials is required; no terminal outcomes have been fabricated. The current offline test suite is separate and passes 30 tests.
