# Architecture and Trust Boundaries

## Roles

| Component | Responsibility | Trust boundary |
|---|---|---|
| Gateway | Routing, segmentation, and controlled external exposure | Internet ↔ private LAN |
| Control plane | Automation, dashboards, and documentation | Administrative access only |
| Monitoring sensor | Network visibility and event collection | Receives mirrored/telemetry traffic |
| Status display | Renders sanitized health state | No infrastructure credentials |
| Storage server | ZFS datasets, snapshots, backup/replication endpoints | Storage management plane |
| Backup target | Independent recovery copy | Separate failure domain |

## Data-flow rules

1. Dashboards consume sanitized state from a backend; displays never hold infrastructure credentials.
2. Monitoring is read-only toward observed workloads unless a separately authorized response workflow exists.
3. Storage administration stays on the private management network.
4. Public exposure is exceptional, documented, owned, and reviewed periodically.
5. Backups are validated by restoration, not assumed from job success.

## Design decisions

- Use direct-disk HBA/JBOD/IT-mode presentation for ZFS.
- Keep storage datasets separate from application state.
- Prefer low-privilege, read-only monitoring identities.
- Record recovery objectives before selecting replication, snapshots, and backup frequency.
