# Security-First Homelab Reference

A security-first reference architecture for a small self-hosted environment. This repository documents patterns, decision records, runbooks, and sanitized examples—not live topology, credentials, or production configuration.

## Design principles

- Storage resilience before applications.
- Direct-disk access for ZFS; never hide disks behind hardware RAID virtual volumes.
- Management interfaces remain private.
- Monitoring produces actionable states, not vanity dashboards.
- Document recovery and test restores.
- Separate public examples from live operational details.

## Architecture

```mermaid
flowchart LR
  Internet --> Gateway[Gateway / Firewall]
  Gateway --> LAN[Private LAN]
  LAN --> Control[Control plane]
  LAN --> Monitor[Network security monitoring]
  LAN --> Display[Read-only status display]
  LAN --> Storage[ZFS storage server]
  Control --> Storage
  Monitor --> Control
  Storage --> Backup[Independent backup target]
```

## Repository layout

- `docs/architecture.md` — component boundaries and trust model.
- `docs/storage.md` — ZFS-oriented storage design and acceptance checklist.
- `docs/SECURITY-STACK.md` — sanitised shape of a small self-hosted security stack, with the controls it needs and the ones it is missing.
- `docs/security.md` — publication and operational-security rules.

## Scope

This is a living technical portfolio. It intentionally omits:

- public IPs, LAN addressing, MAC addresses, hostnames, domains, or service URLs;
- router/firewall exports, port-forward rules, and live configuration;
- API keys, OAuth tokens, credentials, backups, telemetry, and device inventories.

See `SECURITY.md` before contributing.
