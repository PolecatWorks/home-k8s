# PRD: Workloads, AI Inference & Ephemeral Preview Environments

## 1. Overview & Vision
This PRD outlines the application workload layer, local large language model (LLM) execution via **Ollama**, self-hosted CI/CD runners via **GitHub Actions Runner Controller (ARC)**, and on-demand ephemeral PR preview environments orchestrated by **Garden**.

## 2. Workload & Inference Architecture

```mermaid
flowchart TD
    subgraph Microservices & Workloads: dev / sward
        AngryAgent[angry-agent Service]
        MicroRecon[k8s-micro / recon-dev]
        SwardWarden[sward-warden-dev]
        MfeShell[mfe-shell UI]
    end

    subgraph Local LLM & AI Engine: ollama namespace
        OllamaEngine[Ollama Inference Server]
        ModelCache[Persistent Model Volume: qwen2.5-coder:14b]
        OllamaVS[VirtualService: ollama.k8s / Mesh]
        OllamaEngine --> ModelCache
        OllamaVS --> OllamaEngine
    end

    subgraph CI/CD & Build Execution: arc-systems
        ARCController[GitHub ARC Controller]
        RunnerPods[Autoscaling Ephemeral Runners]
        ARCController --> RunnerPods
    end

    subgraph Ephemeral Environments: Garden
        GardenEngine[Garden.io Runner / garden.yml]
        PRNamespace[angry-agent-pr-{PR_NUMBER}]
        GardenTemplate[push-secret-config ConfigTemplate]

        GardenEngine --> PRNamespace
        GardenTemplate -->|ESO Push Secret| PRNamespace
    end

    AngryAgent -.->|Internal Mesh Query| OllamaVS
    RunnerPods -.->|Run CI Builds| GardenEngine
```

## 3. Core Functional Capabilities

### 3.1 Application Workloads
* **Multi-Repo Flux GitRepositories**: Applications sourced continuously from separate repositories:
  * `angry-agent` (`https://github.com/PolecatWorks/angry-agent.git`)
  * `k8s-micro` (`https://github.com/PolecatWorks/k8s-micro.git`)
  * `sward-warden-dev`
* **Micro-Frontend / UI Shell**: Delivery of frontend shell components (`mfe-shell`) connected to underlying backend services.

### 3.2 In-Cluster Local LLM Inference (Ollama)
* **Dedicated Inference Server**: Deployed in `ollama` namespace via Helm chart with dedicated CPU/memory requests (e.g. 16Gi RAM, 2 CPUs).
* **Model Management & Caching**: Automated pulling of specified LLM models (e.g., `qwen2.5-coder:14b`) with persistent volume caching backed by Longhorn storage.
* **Mesh Integration**: Exposed via Istio `VirtualService` (`ollama.k8s`) to both internal mesh clients and gateway routing on port 11434.

### 3.3 Self-Hosted GitHub Actions Runners (ARC)
* **GitHub ARC Integration**: Deployed via `base/controllers/github-arc.yaml` to dynamically scale Kubernetes pods as runner instances for PolecatWorks GitHub workflows.
* **Local Build Performance**: Facilitates fast in-cluster Docker builds, manifest validations, and end-to-end testing without external runner limits.

### 3.4 Ephemeral PR Environments (Garden.io)
* **Dynamic Namespace Provisioning**: `garden.yml` provisions isolated namespaces (`angry-agent-pr-${var.pr-number}`) per pull request.
* **Automated Credential Injection**: Uses Garden `ConfigTemplate` (`push-secret-config`) combined with External Secrets Operator `PushSecret` to inject required DB and registry credentials into ephemeral namespaces automatically.

## 4. Operational & Resource Requirements
* **Resource Quotas & Allocation**: High-memory allocations reserved for the Ollama inference server; lightweight limits applied to runner and test pods.
* **Lifecycle Cleanup**: Ephemeral PR namespaces and their associated `PushSecret` bindings are purged immediately upon PR closure.

## 5. Related Specifications & PRDs
* [001-gitops-core-architecture-prd.md](./001-gitops-core-architecture-prd.md)
* [003-networking-service-mesh-and-ingress-prd.md](./003-networking-service-mesh-and-ingress-prd.md)
* [004-identity-and-secret-distribution-prd.md](./004-identity-and-secret-distribution-prd.md)
* [005-data-persistence-and-streaming-prd.md](./005-data-persistence-and-streaming-prd.md)
* [007-agent-as-data-workload-prd.md](./007-agent-as-data-workload-prd.md)
