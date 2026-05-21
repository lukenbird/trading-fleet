# Incident Taxonomy

Closed-enum classification for what can go wrong during a live session. Runtime tooling accepts only categories listed here. Extending the enum requires updating this document AND the corresponding tool code.

## Categories

| Category | Severity | Day class | Blocks next start | Affects edge evidence | Adjusted truth |
|---|---|---|---|---|---|
| `DATA_GAP` | low–med | WATCH | no | no (affects mirror parity) | no |
| `IB_DISCONNECT_RECOVERED` | low | CLEAN if clean, else WATCH | no | no | no |
| `WATCHDOG_EMERGENCY_FLATTEN` | HIGH | INCIDENT | yes until resolved | yes | yes |
| `BOOKKEEPING_GAP` | HIGH | INCIDENT if combined with flatten, else WATCH | no after patch | yes | yes |
| `STATE_STALE` | medium | WATCH | yes until manually cleared | no | no |
| `TRACEBACK` | HIGH | INCIDENT | yes until root cause noted | depends | maybe |
| `MANUAL_OPERATOR_INTERRUPT` | low | WATCH | no | no | no |
| `BROKER_FLAT_BOT_STATE_OPEN` | HIGH | INCIDENT | yes | yes (broker truth wins) | yes |
| `OPEN_POSITION_AFTER_CLOSE` | medium | WATCH if same-day resolution, INCIDENT if carry-forward | yes if still open at next start | depends | depends |
| `MISSING_LOGS` | medium | INVALID | no | yes (day excluded from headline) | no |
| `UNKNOWN` | HIGH (precautionary) | INCIDENT until reclassified | yes | yes until cleared | maybe |

## Evidence requirements

- **DATA_GAP:** `tools/ibkr_5s_coverage_audit.py` shows missing `data/5s/<sym>/<date>.csv` files
- **IB_DISCONNECT_RECOVERED:** `runtime_audit_*.jsonl` shows `on_ib_disconnect` events re-armed within policy budget
- **WATCHDOG_EMERGENCY_FLATTEN:** `emergency_flatten_initiated` + `emergency_flatten_completed` audit events
- **BOOKKEEPING_GAP:** difference between `live_trades_*.csv` total and broker-truth total
- **STATE_STALE:** `logs/open_trades.json` or `logs/position_state.json` non-empty when broker is flat
- **TRACEBACK:** Python traceback in `logs/paper_console_*.txt` runtime didn't handle
- **MANUAL_OPERATOR_INTERRUPT:** paper console shows operator-initiated stop; no broker safety event
- **BROKER_FLAT_BOT_STATE_OPEN:** broker reports zero positions but local state shows positions
- **OPEN_POSITION_AFTER_CLOSE:** position remains open at session end without EOD_FORCE exit
- **MISSING_LOGS:** expected log file missing for a known session
- **UNKNOWN:** anomaly that does not fit categories above

## Resolution markers

Three ways to mark an incident resolved (used by `prestart` checks):

1. **Sidecar JSON field:** `incident_resolved: true` in `diagnostics/day_closeouts/day_closeout_<YYYYMMDD>.json`
2. **Marker file:** `diagnostics/day_closeouts/incident_resolved_<YYYY-MM-DD>.flag` (any contents)
3. **Patch packet:** a `CODEX_REVIEW_PACKET_*_PATCH.md` exists indicating the future-event path is patched. Doesn't erase historical incident.

## Hard rules

- Raw CSV never mutated. Adjusted-truth overlays computed alongside, never replace.
- Day classification can be reclassified UPward (CLEAN → WATCH → INCIDENT) by steward tooling, never downward. Only operator adds resolution markers.
- This document is canonical. Tools refer to it; tools do not silently extend the enum.
