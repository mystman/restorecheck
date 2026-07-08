# AGENTS.md

Canonical instructions for AI coding agents (and a fair briefing for
humans) working in this repository. `CLAUDE.md` imports this file.

## Project

`restorecheck` is a Kubernetes operator that continuously verifies Velero
backups by actually restoring them: on a schedule it takes the latest
completed backup, restores it into a scratch namespace, runs declared
checks, reports results (CR status, Events, Prometheus metrics), and
cleans up after itself.

## Scope — v0.1 (hard boundary)

In scope:

- One CRD: `RestoreCheck` (v1alpha1).
- Selecting the latest completed Velero Backup by schedule name or label
  selector.
- Cron-triggered Velero `Restore` with `namespaceMapping` into a
  generated scratch namespace.
- Checks: `RestorePhase` (completed, zero errors; warnings configurable)
  and `ResourceCount` (declared kinds with minimum counts).
- Cleanup with TTL and finalizers; history limits.
- Status conditions, Events, Prometheus metrics.
- Install via kustomize; least-privilege RBAC.

Out of scope for v0.1 — do not implement, do not scaffold "for later":

- Creating or scheduling backups; DR orchestration; any non-Velero
  backend; a UI; app-aware validation (the `Probe` check type is v0.2);
  Helm chart (v0.2).

If a task drifts past this boundary: stop, say so, and hand the idea
back to the user for the parking list instead of coding it.

## Conventions

- Go, kubebuilder / controller-runtime idioms. Single reconcile loop,
  one worker, no concurrency cleverness in v0.1.
- Velero objects are accessed via the dynamic client / unstructured.
  Do not import Velero's Go module; revisit only with explicit approval.
- Errors wrapped with context. User-visible outcomes surface as Events
  and status conditions per Kubernetes API conventions.
- Tests: table-driven; every feature lands with envtest coverage; e2e
  (kind + MinIO + Velero fs-backup) covers the happy path only.
- Commits: conventional prefixes (feat/fix/test/docs/chore), imperative,
  small; explain the why in the body when it isn't obvious.
- README rules: status line at the top; plain scope statements
  ("does X; does not do Y"); no hype, no self-deprecation.

## Commands

Scaffold pending (session 1). Once the Makefile exists, keep this
section current: build, unit tests (envtest), lint, e2e (kind).

## Guardrails

- No new dependencies without explicit approval in the session.
- Any RBAC addition must be justified in the commit body.
- `.notes/` is gitignored local working material. You may read it when
  the user points you at it, for context only. Its contents, vocabulary,
  and file names must never appear in committed files, code comments,
  commit messages, or PR text.
- One bounded task per session: finish it or hand back with a summary
  and a single proposed next action. Do not open parallel workstreams.

## Definition of done for any task

Compiles; lint clean; tests updated and passing; behavior observable
(status, Event, or metric) where applicable; README touched only if
user-facing behavior changed.
