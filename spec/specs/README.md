# Task Specifications (Specs)

This directory contains task-specific, ephemeral build specifications. Specs are authored only when a specific change or implementation task is planned, defining the planned deltas, affected manifests/schemas, execution order, and verification criteria.

## Lifecycle Status
- **`draft`**: Spec is actively being authored or refined prior to implementation.
- **`complete`**: Task implementation and verification are complete.
- **`deprecated`**: Spec has been superseded by a newer task specification.

## Active & Historical Task Specs

| Spec | Title | Status | Description |
|---|---|---|---|
| [001-agent-as-data-deployment-spec.md](./001-agent-as-data-deployment-spec.md) | Agent-As-Data (AAD) Workload Deployment & PostgreSQL Integration | `complete` | Deploy agent-as-data via Flux CD v2 with CNPG PostgreSQL + pgvector persistence, ESO PushSecrets, and local-only ingress. |
