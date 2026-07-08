# restorecheck

Status: actively developed — pre-v0.1

A Kubernetes operator that continuously verifies Velero backups by actually
restoring them: on a schedule it restores the latest completed backup into a
scratch namespace, runs declared checks, reports results via CR status, Events,
and Prometheus metrics, then cleans up after itself.

Does not create, schedule, or delete backups.
