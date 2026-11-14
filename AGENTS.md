# gpu-fault-rca — agent guardrails

## Scope
- `faultsim/` is the system under test plus the chaos harness: topology, simulator, faults, scenarios, matrix, recovery.
- `rca/` is the analyzer. It must never import `faultsim` (`test_rca_never_imports_the_simulator`) and never read
  `ground_truth.json` (`test_ground_truth_is_never_read`).
- A new fault means a `Fault` subclass with `truth()`, a scenario in `scenarios.py`, detector and catalog entries
  in `rca/`, a row in `docs/SIGNALS.md`, and a matrix run proving precision and recall stay >= 0.95.

## Rules
- `from __future__ import annotations` in every module. Module-level constants (`ENCODING`, thresholds).
- JSON via `Path.open("r", encoding=ENCODING)` + `json.load`. No pandas or numpy: stdlib only.
- `MAX_ATTEMPTS`, never `MAX_RETRIES`. Use bare `raise`.
- Parsers are tested against hand-written fixtures in the upstream formats, not against simulator output.
- Never tune a fault so the RCA passes. If the RCA misses a realistic signature, fix the RCA.
- Never claim real-hardware validation. The telemetry formats are real; the faults are simulated.

## Done means
`python -m pytest` green, `python -m faultsim matrix` at precision and recall >= 0.95, and README tables updated if numbers move.
