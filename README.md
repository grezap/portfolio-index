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
| 🟡 | [`nexus-cli`](https://github.com/grezap/nexus-cli) | .NET 10 Native AOT CLI — single binary, controls the entire VM/Swarm/Nomad fleet. **`v0.3.0` released 2026-05-13** — third master-plan verb live: `failover-test consul-leader` (SSH.NET 2025.1.0 adapter [ADR-0007], raft polling, RTO measurement, auto-recovery). Verified live: **1.55 second RTO** on the first run — swarm-manager-1 stopped via SSH, swarm-manager-3 elected, consul auto-restarted, cluster back to 6 alive. Earlier: **v0.2.x** (`infrastructure {list, status, suspend, resume}` + `suspend-cluster` alias, ADR-0006 hand-rolled `vms.yaml` reader, ADR-0007 SSH client choice); **v0.1.0 alpha** (`cluster-status`). 22.34 MB win-x64 AOT binary, under the 25 MB exit gate. Two remaining master-plan verbs (`kafka failover`, `demo run/record`) are stubs. Next slice = v0.3.1 `failover-test nomad-leader`. |
| ⚪ | `nexus-shared` | Shared .NET 10 libraries — OTel bootstrap, Vault client, Problem Details, Outbox helpers, auth primitives |
| 🟡 | [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | Packer templates + Terraform (vmware-desktop provider) for the 66-VM lab. Shipped: 6 Packer templates (Debian 13, Ubuntu 24.04, Windows Server 2025 core/desktop, Windows 11 Enterprise, Vault) · `foundation` env (DC promotion + AD DS forest + domain-joined jumpbox + AD hardening + Vault-KV-backed bootstrap creds via AppRole + KV→AD rotation overlay + GMSA scaffolding + Vault Agent on Windows hosts + **NFSv4 export from gateway for Portainer CE shared `/data`** [0.E.4a]) · `security` env (3-node HA Vault on Raft with **transit auto-unseal** + internal PKI hierarchy with 90-day leaf TTL + LDAPS auth/ldap + secrets/ldap AD password rotation + nexus-foundation-reader AppRole + nexus/foundation/* cred seed + 6 narrow Vault Agent AppRoles for swarm nodes + PKI roles `consul-server`/`nomad-server` + token role **`nomad-cluster`** with `nomad-jobs` policy [0.E.3.3b] + manager Vault Agent policies v4). **Phase 0.D fully closed** + 0.E.3.3b + 0.E.4a Vault/gateway scaffolding ✅ live (~80-check chained smoke gate ALL GREEN). |
| 🟢 | [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | Tier-2 orchestration — Docker Swarm (3+3) + Nomad + Consul + Portainer CE + Vault Agent on every node. **`v0.2.0` released 2026-05-08 — Phase 0.E fully closed, cold-rebuildable.** All 6 sub-phases sealed (`0.E.1`/`2.1-3`/`3.1-3`/`4`/`4e`/`5`); cold-rebuild proven end-to-end via `terraform destroy` → `packer build` → `terraform apply` → 409-check smoke gate ALL GREEN with stock root-only CA bundle on build host. Earlier: **Phase 0.E.3.3 ✅** (~155-check chained smoke green: 0.E.1 swarm cluster + 0.E.2.1 Consul gossip + 0.E.2.2 Consul TLS + 0.E.2.3 Consul ACL deny-mode + 0.E.3.1 Nomad TLS (mTLS RPC + raft + HTTPS:4646, `verify_server_hostname=true`) + 0.E.3.2 Nomad ACL (mgmt token in Vault KV, 6 per-host operator tokens, anon-deny enforced) + **0.E.3.3a Nomad → Consul HTTPS rewire** (Nomad agents talk Consul over `https://127.0.0.1:8501` with per-host ACL token rendered by Vault Agent; legacy `consul { address = "127.0.0.1:8500" }` block surgical-removed from `nomad.hcl`) + **0.E.3.3b Nomad-Vault integration**)). **Phase 0.E.4 + 0.E.4e ✅ closed** (Portainer CE clustered Swarm service + TLS full-chain on the wire + `inet filter forward` ingress-mesh accept rules + cold-rebuild proof: `terraform destroy` → `packer build` → `terraform apply` → smoke ALL GREEN with stock root-only CA bundle on the build host). **v0.1.1 tagged 2026-05-08.** |
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
