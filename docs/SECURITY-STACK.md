# Security Stack

This document describes the *shape* of a small self-hosted security stack: which components exist, what each one is accountable for, and which rules keep the arrangement safe. It deliberately contains no hostnames, addresses, ports, or vendor configuration for any live system. See `SECURITY.md` for the publication rules that govern this.

## Design position

Detection is not prevention. The observability half of this stack is mature; the enforcement half is where the real work is. A sensor that records everything is worth little if the default posture is accept-all.

Two rules follow from that, and both are easy to get backwards:

1. **Bind management and search surfaces to loopback.** Anything that can query, mutate, or delete state belongs behind loopback or an authenticated proxy. Exposing a search cluster directly to the LAN is the single most common failure in this kind of setup, because the cluster usually ships with authentication disabled.
2. **Open a collector port only to its known sender.** An ingest port bound to all interfaces is an unauthenticated write path into the log store. Anyone who can reach it can plant records, which corrupts the audit trail the stack exists to produce.

## Component layers

| Layer | Responsibility | Trust posture |
|---|---|---|
| Capture interface | Bring a mirror or tap NIC up in promiscuous mode before analysis starts | Privileged, host-local, no routing |
| Packet sensor | Signature and protocol analysis on mirrored traffic; writes alert events | Read-only toward observed workloads |
| Protocol analyser | Long-term connection and protocol metadata for retrospective queries | Read-only, no response workflow |
| Log shipper | Collects events from sensors and from upstream infrastructure such as a gateway | Sole writer into the log store |
| Log store | Holds searchable event history | Loopback-only, authentication enforced in a real deployment |
| Visualisation | Dashboards and alert triage for the operator | Loopback-only or behind an authenticated proxy |
| Patch baseline | Scheduled package list refresh and unattended upgrades | Host-local, monitored for pending reboot |
| Fail-closed gate | Loads mandatory local policy before the network comes up | Runs at the earliest boot target |
| Host firewall | Denies by default, permits narrowly | Persistent config, verified after load |

## Ordering rule

The capture interface must be up before the sensors bind to it. A sensor that starts on a down interface silently records nothing, and the failure looks identical to a quiet network. Enable the interface as a oneshot unit with `RemainAfterExit`, ordered before the sensor services, and confirm the sensor logs its packet counts on startup.

## Loopback-only services

Keep these bound to `127.0.0.1`:

- log store and its transport port
- dashboard and visualisation UI
- log shipper dashboard/API
- local inference and scraping endpoints
- developer tooling endpoints, including browser remote debugging
- static file servers used for study or scratch material

Binding is the first control, not the only one. It removes LAN reachability; it does nothing against a compromised process on the same host, and it says nothing about WAN reachability.

## What this stack deliberately lacks

Stated plainly, because an inventory that only lists strengths is marketing:

- **No brute-force protection.** Without a ban-on-failure mechanism, an SSH service on a default-accept host is an invitation. Pair rate limiting with the firewall policy below.
- **No default-deny host policy.** A stock ruleset with empty chains and no policy is not a firewall. Enforcement must be stated in persistent configuration, not assumed.
- **No restore test.** Snapshots are not backups until a restore has been performed. Record the objective, then test it.

## Remediation order

1. Write and enable the default-deny host firewall policy, preserving container-manager chains and any custom rulesets.
2. Add ban-on-failure protection for remote access.
3. Restrict collector ingest ports to the identified sender address, and confirm with a test event afterwards.
4. Decide the audience for any LAN-reachable application with state-changing endpoints, then either bind it to loopback or firewall-allow it explicitly per device.
5. Reboot into pending kernel updates and re-audit.

## Review rules

- Bind first, firewall second. Never the reverse, and never both by assumption.
- After any firewall change, verify from a second device on the network, not from the host itself. A loopback test proves nothing about LAN reachability.
- Confirm external exposure from a network outside the perimeter, after checking the gateway's forwarding and automatic port-mapping rules. Local audits cannot establish WAN state in either direction.
- Treat monitoring data as sensitive. It describes the shape of the network and the behaviour of its hosts.

## Related

- `docs/architecture.md` for component boundaries and trust model
- `docs/storage.md` for storage design and acceptance checks
- `docs/security.md` for publication and operational-security rules