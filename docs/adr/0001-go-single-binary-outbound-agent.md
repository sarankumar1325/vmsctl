# 0001. Go, single binaries, outbound agents

- Status: Accepted
- Date: 2026-11-02

## Context

hostlint's predecessor, VMS, used a Flask/MongoDB server and a Python agent that listened on a
TCP port and executed shell scripts sent to it. Anyone who could reach that port could run code
as root. The project's goal is open-source adoption, so the first-run experience
(install → first result) matters more than raw development speed.

## Decision

- Go for everything — CLI, agent and server — in one module.
- Distribute static binaries. The server embeds its web UI (React + Vite + shadcn/ui) and
  defaults to SQLite (PostgreSQL optional), so one binary or one `docker run` is enough to try it.
- Fleet mode is pull-based: the agent dials out to the server and receives work over a gRPC
  bidirectional stream with mutual TLS. Agents open no listening ports.
- Whether the fleet roles ship as subcommands of one binary or as separate binaries is decided
  in v0.2 (binary size vs. simplicity).

## Alternatives considered

- **Rust agent + TypeScript server** — strongest memory-safety story, but two languages and the
  slowest start.
- **TypeScript full stack + Go agent** — fastest MVP, weakest identity; long-lived streams fit
  Next.js poorly.
- **FastAPI + Go agent** — cheapest migration from VMS, least reason to rebuild.

## Consequences

- One toolchain, cross-compiled binaries, simple `curl | sh` installs.
- Server-side CRUD is more verbose in Go than in TypeScript; acceptable for a small UI surface.
