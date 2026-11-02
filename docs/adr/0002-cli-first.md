# 0002. Ship a standalone CLI first; fleet mode reuses it

- Status: Accepted
- Date: 2026-11-02

## Context

The long-term architecture includes a server, agents and a web UI (ADR 0001). Building all of
it before anyone can try hostlint delays feedback on the part that matters most: the checks.

## Decision

- v0.1 ships a standalone CLI, `hostlint scan`, plus `hostlint report`, which merges many hosts'
  JSON results into one static HTML report. No server, agent or mTLS in v0.1.
- The engine and the JSON result schema are designed for fleet use from day one: results carry
  host identity, scan ID, timestamps and rule/profile versions, and the status model includes
  waivers, so history and drift can be added later without breaking the format.
- Fleet mode (v0.2) wraps the same engine.

## Review

A counter-review (Grok, 2026-11-02) argued that a CLI-only release would be compared with Lynis
on rule count, and that the real differentiator is fleet-wide, per-control visibility. Accepted
in part: the multi-host HTML report gives that visibility without a server. Also accepted: the
risk that the engine calcifies around one-shot runs — hence the fleet-ready result schema.

## Consequences

- Fastest path to a usable release; the README demo is a single command.
- v0.1 must already be good at per-control reporting, not just at individual checks.
