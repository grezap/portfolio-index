# NexusPlatform Portfolio — Index

> Senior .NET &amp; Data Engineer portfolio by **Greg Zapantis**. This repository is the
> **start here** for recruiters, CTOs, and prospective clients. It maps every project
> in the portfolio to the skills it demonstrates.

<p align="center">
  <a href="https://github.com/grezap">👤 GitHub Profile</a> &nbsp;·&nbsp;
  <!-- <a href="https://gregzapantis.dev">🌐 Website</a> &nbsp;·&nbsp; -->
  <a href="https://www.linkedin.com/in/grigoris-zapantis-1a0638b/">💼 LinkedIn</a> &nbsp;·&nbsp;
  <a href="./PORTFOLIO.md">📊 Skills matrix</a> &nbsp;·&nbsp;
  <a href="https://github.com/grezap/nexus-platform-plan">🗺️ Master plan (nexus-platform-plan)</a> &nbsp;·&nbsp;
  <a href="https://github.com/grezap/nexus-platform-plan/blob/main/docs/glossary.md">📖 Tool stack glossary</a>
</p>

> **New to the stack?** The portfolio uses a lot of named tools — Vault, Consul, Nomad, Kafka, Iceberg, ClickHouse, StarRocks, Prefect, Marquez, … — that not every reader has encountered. Open the [tool stack glossary](https://github.com/grezap/nexus-platform-plan/blob/main/docs/glossary.md) for plain-English definitions of each, grouped by where in the stack they sit.

---

> **📘 Canonical blueprint:** The complete end-to-end plan — 14 app projects, 5 infrastructure
> repos, 30 enhancements, 12 build phases, 65-VM lab topology, 14 demo playbooks, and all
> per-project schema designs — lives in **[`nexus-platform-plan`](https://github.com/grezap/nexus-platform-plan)**
> (v0.1.0 Plan published). Start there for the architectural source of truth.

## What is NexusPlatform?

NexusPlatform is a collection of **14 production-grade .NET application projects**,
**5 infrastructure repositories**, and a **4-app native Windows suite**, engineered as
a single coherent portfolio. Every project is built to senior-engineering standards:
correct architectural patterns, HA-capable infrastructure, full observability,
automated tests, and documented runbooks.

It targets four readers:

- **Enterprise Data Architects** — looking for modern data-platform expertise.
- **.NET Technical Leads / CTOs** — looking for architectural judgement and full DevOps ownership.
- **AI-Forward Businesses** — looking for ML/LLM systems that ship.
- **Upwork / freelance clients** — looking for polished, working demonstrations of capability.

## How to navigate this portfolio

1. Start with the [**skills matrix**](./PORTFOLIO.md) if you're filtering by technology.
2. Browse the [**project grid**](#project-grid) below if you want the one-paragraph pitch.
3. Click into any project repository for full architecture, demo, and case study.

## Project grid

> Legend: 🟢 shipped · 🟡 in progress · ⚪ planned

### Infrastructure

| Status | Project | What it is |
|:---:|---|---|
| ⚪ | `nexus-cli` | .NET 10 Native AOT CLI — single binary, controls the entire VM/Swarm/Nomad fleet |
| ⚪ | `nexus-shared` | Shared .NET 10 libraries — OTel bootstrap, Vault client, Problem Details, Outbox helpers, auth primitives |
| 🟡 | [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | Packer templates + Terraform (vmware-desktop provider) for the 66-VM lab. Shipped: 6 Packer templates (Debian 13, Ubuntu 24.04, Windows Server 2025 core/desktop, Windows 11 Enterprise, Vault) · `foundation` env (DC promotion + AD DS forest + domain-joined jumpbox + AD hardening + Vault-KV-backed bootstrap creds via AppRole + KV→AD rotation overlay + GMSA scaffolding + Vault Agent on Windows hosts) · `security` env (3-node HA Vault on Raft with **transit auto-unseal** + internal PKI hierarchy with 90-day leaf TTL + LDAPS auth/ldap + secrets/ldap AD password rotation + nexus-foundation-reader AppRole + nexus/foundation/* cred seed + 2 narrow Vault Agent AppRoles). **Phase 0.D fully closed** (all 5 sub-deliverables of 0.D.5 live; ~80-check chained smoke gate ALL GREEN). |
| 🟡 | [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | Tier-2 orchestration — Docker Swarm (3+3) + Nomad + Consul + Vault with mTLS via Consul Connect. **Phase 0.E starting.** |
| ⚪ | `nexus-infra-k8s` | Tier-3 orchestration — kubeadm HA control plane, Cilium CNI, cert-manager, Argo CD, Kyverno policy |
| ⚪ | `nexus-infra-registry` | Harbor private registry + Trivy scanning + cosign image signing |
| 🟢 | [`local-data-stack`](https://github.com/grezap/local-data-stack) | Reproducible single-host Compose stack (Kafka KRaft, ClickHouse, Redis, OTel, Prometheus, Grafana, Jaeger, Seq). [v0.1.0 released](https://github.com/grezap/local-data-stack/releases/tag/v0.1.0). |

### Application projects

| # | Status | Project | Architecture | One-line pitch |
|---:|:---:|---|---|---|
| 0 | ⚪ | `portfolio` | Clean Arch | This website — Blazor Server portfolio homepage |
| 1 | ⚪ | `dataflow-studio` | Modular Monolith | SQL Server CDC → Kafka → StarRocks DWH + ClickHouse analytics |
| 2 | ⚪ | `tenantcore` | Clean Arch | Multi-tenant SaaS on Percona MySQL XtraDB + ProxySQL |
| 3 | ⚪ | `sentinelml` | Vertical Slice | Fraud + anomaly ML with automated drift-triggered retraining |
| 4 | ⚪ | `localmind` | Clean Arch | Local LLM gateway — Semantic Kernel + Ollama + OpenAI-compatible API |
| 5 | ⚪ | `pulsenlp` | Vertical Slice | NLP pipeline: ML.NET + DistilBERT + BERT NER all served via ONNX in .NET |
| 6 | ⚪ | `visioncore` | Clean Arch | Computer vision — PyTorch → ONNX → C# inference, no OpenCV |
| 7 | ⚪ | `recoengine` | Modular Monolith | Recommendation engine — ML.NET MatrixFactorization + Kafka Streams |
| 8 | ⚪ | `chronosight` | Vertical Slice | Time-series forecasting on ClickHouse + ksqlDB |
| 9 | ⚪ | `querylens` | Vertical Slice | SQL Server performance intelligence with AI rewrite suggestions |
| 10 | ⚪ | `fieldsync` | Clean Arch | .NET MAUI + gRPC bidirectional sync + on-device ONNX |
| 11 | ⚪ | `nexus-platform` | Microservices | 6-service reference: gRPC + Kafka + REST, 2 sagas, K8s manifests |
| 12 | ⚪ | `streamcore` | Vertical Slice | Kafka ecosystem showcase — Streams, ksqlDB, MirrorMaker 2 DR demo |
| 13 | ⚪ | `nexus-desk` | Monorepo | 4 native Windows apps: WinForms, WPF, WinUI 3, WinUI3+WPF hybrid |
| 14 | ⚪ | `lakehouse-core` | Medallion Lakehouse | Iceberg on MinIO (Bronze/Silver/Gold) + PySpark + dbt Core + Prefect + Trino federation |

## Highlights

- **.NET 10 / C# 13 throughout** — primary constructors, Generic Math, Native AOT, System.Threading.Channels, pattern-matching exhaustiveness.
- **Three deployment tiers** — VMware HA (Tier 1) · Docker Swarm + Nomad (Tier 2) · Kubernetes (Tier 3). The same project ships to all three.
- **Full observability from day one** — OpenTelemetry → Prometheus · Grafana · Jaeger · Seq, with W3C TraceContext propagation across every service boundary.
- **ML portability** — ML.NET, PyTorch, and HuggingFace models all served through ONNX in .NET with no Python runtime in production.

## Contact

- **Email:** `gzapas@gmail.com`
- **LinkedIn:** `https://www.linkedin.com/in/grigoris-zapantis-1a0638b/`
- **Upwork:** `https://www.upwork.com/freelancers/~01de3f0552684544ee`

## License

This repository (the index) is licensed under [MIT](./LICENSE). Individual project
repositories are licensed separately — see each repo's LICENSE file.
