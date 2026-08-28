# Product Requirements Documents (PRDs)

This directory contains persistent Product Requirements Documents defining the architecture, capabilities, data models, and operational domains of the `home-k8s` GitOps cluster platform.

## PRD Index

| PRD | Title | Description |
|---|---|---|
| [001-gitops-core-architecture-prd.md](./001-gitops-core-architecture-prd.md) | GitOps Core Architecture & Multi-Tier Layering | Core Flux CD v2 GitOps architecture, directory layout, dependency trees, and reconciliation lifecycle. |
| [002-infrastructure-operators-and-storage-prd.md](./002-infrastructure-operators-and-storage-prd.md) | Infrastructure Operators & Storage Management | Baseline platform operators including Longhorn distributed storage, Cert-Manager, ECK, and Prometheus stack. |
| [003-networking-service-mesh-and-ingress-prd.md](./003-networking-service-mesh-and-ingress-prd.md) | Networking, Service Mesh & Ingress Management | Istio Service Mesh, Ingress Gateways, MetalLB bare-metal load balancing, and Cloudflare Zero-Trust Tunnels. |
| [004-identity-and-secret-distribution-prd.md](./004-identity-and-secret-distribution-prd.md) | Identity, Access & Cross-Namespace Secret Distribution | Keycloak IAM orchestration, Keycloak realms/clients/SAs, and External Secrets Operator (ESO) PushSecret patterns. |
| [005-data-persistence-and-streaming-prd.md](./005-data-persistence-and-streaming-prd.md) | Data Persistence & Event Streaming | CloudNativePG (CNPG) PostgreSQL database cluster management, user provisioning, and Confluent Kafka event streaming. |
| [006-workloads-ai-and-ephemeral-environments-prd.md](./006-workloads-ai-and-ephemeral-environments-prd.md) | Workloads, AI Inference & Ephemeral Preview Environments | Application workloads, Ollama local LLM inference server, GitHub ARC self-hosted runners, and Garden PR environments. |
