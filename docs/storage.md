# Storage Design

## Acceptance checklist

- Every physical drive is independently visible to the storage OS.
- No hardware RAID virtual disk sits below ZFS.
- Pool redundancy is selected from failure tolerance, usable capacity, and rebuild-risk requirements.
- SMART monitoring, periodic scrubs, snapshots, and alert delivery are configured before applications.
- A restoration test proves that snapshots and backups are usable.
- Application state and synchronized user data use separate datasets.

## Monitoring states

Alert on pool degradation, disk-health warnings, scrub errors, stale snapshots/replication, capacity thresholds, and backup failures. Do not alert merely because a dashboard graph moved.
