# 0004. Observation-only scanning

- Status: Accepted
- Date: 2026-11-02

## Context

A hardening scanner runs as root on every host. If rules can run commands, whoever controls the
rules — a compromised fleet server, a malicious rule pack, a leaked signing key — controls the
fleet. Blocking command execution alone is not enough: if the server can choose which files to
read, it can exfiltrate `/etc/shadow`, SSH keys or cloud credentials from every host.

## Decision

- Rules are data: YAML plus side-effect-free expressions over facts. They cannot execute
  commands — in any mode, signed or not.
- Facts come from collectors compiled into the binary. A collector that needs a command
  (e.g. `sshd -T`) uses a fixed argv: no shell, no rule-supplied arguments.
- Generic file reads pass through a built-in sensitive-path denylist (shadow files, private keys,
  credential stores, kube/cloud configs). Collectors that must parse sensitive files expose only
  non-secret fields (e.g. password aging from `/etc/shadow`, never hashes).
- Results carry evidence summaries, not raw file contents.
- Releases are signed and ship an SBOM.

## Review

The counter-review (Grok, 2026-11-02) pointed out the exfiltration path, the need for
command-based facts, and the risk of signed-rule execution; all three are reflected above.

## Consequences

- Some checks need a new collector (Go code plus review) instead of a one-line rule. That is
  intentional.
- The README can promise: hostlint never changes your host and never sends your secrets anywhere.
