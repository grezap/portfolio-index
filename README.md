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

> **📘 Canonical blueprint:** The complete end-to-end plan — 14 app projects, 6 infrastructure
> repos, 30 enhancements, 12 build phases, 66-VM lab topology, 14 demo playbooks, and all
> per-project schema designs — lives in **[`nexus-platform-plan`](https://github.com/grezap/nexus-platform-plan)**
> (v0.1.0 Plan published). Start there for the architectural source of truth.

## What is NexusPlatform?

NexusPlatform is a collection of **14 production-grade .NET application projects**,
**6 infrastructure repositories**, and a **4-app native Windows suite**, engineered as
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
| 🟢 | [`nexus-cli`](https://github.com/grezap/nexus-cli) | .NET 10 Native AOT CLI — single binary, controls the entire VM/Swarm/Nomad/Kafka fleet. **`v0.5.0` released 2026-05-15 — Phase 0.F closed; all 5 of 5 master-plan verbs live.** `kafka failover {east-to-west, west-to-east}` ships (ADR-0008: demo-grade DR via vmrun-suspend × 3 source brokers + RF=3 produce/consume round-trip on the target, vmrun-resume; **live RTOs 13.20 s + 13.57 s** under the 60 s master-plan gate). Earlier: **v0.4.0** `demo {list, run, record}` (JSON-spec orchestrator + VHS `.tape` recorder); **v0.3.x** `failover-test {consul-leader, nomad-leader, swarm-manager}` (live RTOs 1.55 s / 2.716 s / 21.59 s, ADR-0007 SSH.NET); **v0.2.x** `infrastructure {list, status, suspend, resume}` (ADR-0006 hand-rolled `vms.yaml` reader); **v0.1.0 alpha** `cluster-status`. 22.75 MB win-x64 AOT binary, 2.25 MB headroom against the 25 MB exit gate. Verbs / RTOs all verified live end-to-end. |
| ⚪ | `nexus-shared` | Shared .NET 10 libraries — OTel bootstrap, Vault client, Problem Details, Outbox helpers, auth primitives |
| 🟡 | [`nexus-infra-vmware`](https://github.com/grezap/nexus-infra-vmware) | Packer templates + Terraform (vmware-desktop provider) for the 66-VM lab. Shipped: 6 Packer templates (Debian 13, Ubuntu 24.04, Windows Server 2025 core/desktop, Windows 11 Enterprise, Vault) · `foundation` env (DC promotion + AD DS forest + domain-joined jumpbox + AD hardening + Vault-KV-backed bootstrap creds via AppRole + KV→AD rotation overlay + GMSA scaffolding + Vault Agent on Windows hosts + **NFSv4 export from gateway for Portainer CE shared `/data`** [0.E.4a] + **gateway dhcp-host reservations for all 9 `05-oltp` tier VMs (6 redis + 3 mongo)** [0.G.1/0.G.2]) · `security` env (3-node HA Vault on Raft with **transit auto-unseal** + internal PKI hierarchy with 90-day leaf TTL + LDAPS auth/ldap + secrets/ldap AD password rotation + nexus-foundation-reader AppRole + nexus/foundation/* cred seed + 6 narrow Vault Agent AppRoles for swarm nodes + PKI roles `consul-server`/`nomad-server`/**`kafka-broker`** (90 d, `allowed_domains` extended to all 15 `03-kafka` hostnames) + token role **`nomad-cluster`** with `nomad-jobs` policy [0.E.3.3b] + manager Vault Agent policies v4 + **15 per-broker AppRoles + JSON sidecars for the kafka tier [0.H.2-0.H.5]** + **9 per-instance AppRoles + PKI roles `oltp-redis`/`oltp-mongo` + KV seeds for redis cluster password, mongo keyFile, and mongo smoke-user creds for the oltp data tier [0.G.1/0.G.2]**). **Phase 0.D fully closed** + 0.E.3.3b + 0.E.4a + **0.H + 0.G Vault-side scaffolding** ✅ live (~80-check chained smoke gate ALL GREEN). |
| 🟢 | [`nexus-infra-swarm-nomad`](https://github.com/grezap/nexus-infra-swarm-nomad) | Tier-2 orchestration — Docker Swarm (3+3) + Nomad + Consul + Portainer CE + Vault Agent on every node. **`v0.2.0` released 2026-05-08 — Phase 0.E fully closed, cold-rebuildable.** All 6 sub-phases sealed (`0.E.1`/`2.1-3`/`3.1-3`/`4`/`4e`/`5`); cold-rebuild proven end-to-end via `terraform destroy` → `packer build` → `terraform apply` → 409-check smoke gate ALL GREEN with stock root-only CA bundle on build host. Earlier: **Phase 0.E.3.3 ✅** (~155-check chained smoke green: 0.E.1 swarm cluster + 0.E.2.1 Consul gossip + 0.E.2.2 Consul TLS + 0.E.2.3 Consul ACL deny-mode + 0.E.3.1 Nomad TLS (mTLS RPC + raft + HTTPS:4646, `verify_server_hostname=true`) + 0.E.3.2 Nomad ACL (mgmt token in Vault KV, 6 per-host operator tokens, anon-deny enforced) + **0.E.3.3a Nomad → Consul HTTPS rewire** (Nomad agents talk Consul over `https://127.0.0.1:8501` with per-host ACL token rendered by Vault Agent; legacy `consul { address = "127.0.0.1:8500" }` block surgical-removed from `nomad.hcl`) + **0.E.3.3b Nomad-Vault integration**)). **Phase 0.E.4 + 0.E.4e ✅ closed** (Portainer CE clustered Swarm service + TLS full-chain on the wire + `inet filter forward` ingress-mesh accept rules + cold-rebuild proof: `terraform destroy` → `packer build` → `terraform apply` → smoke ALL GREEN with stock root-only CA bundle on the build host). **v0.1.1 tagged 2026-05-08.** |
| 🟢 | [`nexus-infra-kafka`](https://github.com/grezap/nexus-infra-kafka) | Tier-3 data backbone — the Kafka ecosystem. **`v0.1.0` released 2026-05-15 — Phase 0.H complete, all 6 sub-phases closed, cold-rebuildable.** 15 VMs: two 3-node KRaft clusters (`kafka-east` primary + `kafka-west` DR) on **mutual TLS**, a Schema Registry HA pair, a Confluent REST Proxy, a Kafka Connect distributed cluster (Debezium Postgres + SQL Server plugins), a ksqlDB cluster, and a **MirrorMaker 2 cross-cluster DR pair** — every node holds a per-node Vault-PKI keystore and talks to the brokers over mTLS. The **Phase 0.H exit gate** is cleared: a record produced to `kafka-east` appears on the mirrored topic on `kafka-west` via MirrorMaker 2, and the reverse. Cold-rebuild proven end-to-end (`destroy` → `security apply` → `apply` → smoke `0.H.2`-`0.H.5` ALL GREEN, no operator hot-state). Sub-phases: **`0.H.1`** `kafka-node` Packer template (Temurin JDK 21 + Apache Kafka 3.8.1 + Confluent Community 7.7.1) + both KRaft clusters (38 checks); **`0.H.2`** broker mutual TLS (92); **`0.H.3`** Schema Registry HA pair + REST Proxy (37); **`0.H.4`** Kafka Connect + Debezium + ksqlDB (48); **`0.H.5`** MirrorMaker 2 + the phase exit gate (38); **`0.H.6`** close-out canon batch + cold-rebuild proof. |
| 🟢 | [`nexus-infra-oltp`](https://github.com/grezap/nexus-infra-oltp) | Tier-4 OLTP data backbone — **OLTP TIER SEALED 2026-05-22 (5/5 clusters cold-rebuild proven)**. Phases 0.G.1 + 0.G.2 + 0.G.3 + 0.G.3.5 + 0.G.4 closed 2026-05-18 → 2026-05-19 (4 Linux data clusters proven end-to-end). **Phase 0.G.7 (SQL Server FCI + Always On AG on Windows Server 2025) LIVE-RATIFIED 2026-05-22 — `smoke-0.G.7.ps1` ALL GREEN 56/56** adds the 5th cluster + 4 ws2025-desktop VMs: a 2-node WSFC Failover Cluster Instance (virtual name `sqlfci` @ `.16`, sharing an iSCSI LUN from `nexus-gateway` as a clustered Physical Disk per ADR-0026) + 2 async AG replicas (`nexus-ag`, both SYNCHRONIZING+HEALTHY, MANUAL-seeded since the FCI keeps DBs on shared `S:\` vs replicas on local `C:\`) + 3 WSFC-managed VIPs (cluster `.15`, FCI `.16`, AG Listener `.17`). SQL service identity = `nexus.lab\gmsa-sql-engine$` on the FCI (first real GMSA consumer; replicas run as NETWORK SERVICE); AG endpoint authentication = certificate-based per ADR-0027. Per ADR-0025 the AG Listener IS the LB-tier HA primitive — a remote domain client `sqlcmd -E -N` (Encrypt + strict cert-chain validate) reaches the primary across the listener (cert IP-SAN .17 + Kerberos SPNs on the gmsa). Engines live: **6-node Redis 8.0 Cluster** (3 masters + 3 replicas, TLS-only on 6379/16379 with Vault-PKI per-node leaf certs, cluster bus on VMnet10 backplane), **3-node MongoDB 8.0 Replica Set `nexus-rs`** (mTLS on 27017 + keyFile internal auth + SCRAM-SHA-256 smoke-rw user), **3-node Percona XtraDB Cluster 8.0.45 + 2-node ProxySQL 2.6** (mTLS-only on 3306 via per-host Vault PKI, Galera SST/IST encrypted over VMnet10 via `pxc-encrypt-cluster-traffic=ON`, ProxySQL `galera_hostgroups` with writer/backup_writer/reader splits, VRRP-floated VIP `192.168.70.50` between proxysql-1 MASTER + proxysql-2 BACKUP using unicast mode). Also live: **3-node Patroni 4.0.5 PostgreSQL 17 HA cluster** (`pg-primary` + `pg-replica-1` + `pg-replica-2`) + **3-node etcd 3.5.16 raft quorum** (Patroni DCS at `/service/nexus-pg/` with HTTP basic-auth RBAC, mTLS for wire encryption) + **2-node HAProxy 3.0 LTS HA pair** (`haproxy-pg-1` + `haproxy-pg-2`) with **VRRP-floated VIP `192.168.70.60`** routing `:5432` to current Patroni leader via REST `/leader` health probes (mirrors the 0.G.3 proxysql HA pair pattern; no SPOF on the LB tier). All cluster smoke gates ALL GREEN; 0.G.4 smoke gate 152/152 ALL GREEN. **0.G.3.5 was a mid-phase architectural refactor** — the monolithic `packer/oltp-node/` + `terraform/envs/oltp/` had a 30-min iteration loop that stalled the 0.G.3 ratification at 16 transients; the refactor split into per-engine templates + per-cluster states + per-cluster nftables overlays + per-cluster operator scripts, shrinking iteration to ~5-10 min per cluster. 27 distinct transients root-caused + permanently fixed across both ratifications. Architectural rule canonized: multi-cluster infra tiers default to per-cluster state + per-engine template (now applied across 0.G.4 → 0.G.7). 0.G.7 ratification surfaced 40+ transients all fixed in source (handbook §3.5b + §3.5c). **Coming next**: nexus-cli `IClusterAdapter` SqlFci/SqlAg adapters + System B demos. |
| 🟢 | [`nexus-infra-analytics`](https://github.com/grezap/nexus-infra-analytics) | Tier-4 analytics data backbone — ClickHouse + StarRocks (15 VMs on `04-analytics`). **Phases 0.G.5 + 0.G.6 SEALED 2026-05-23 (`v0.1.0`)** — both clusters live-ratified + cold-rebuild-proven (`smoke-0.G.5.ps1` **129/129** · `smoke-0.G.6.ps1` **73/73** GREEN; StarRocks 3.5.17 on JDK 21; 15 apply-time transients fixed in source). StarRocks CN / shared-data tier deferred to 0.L. **ClickHouse (0.G.5)**: a genuine **3 shards × 2 replicas** cluster (6 data nodes) + a dedicated **3-node ClickHouse Keeper** RAFT quorum (NOT ZooKeeper — the lab runs zero ZooKeeper VMs) — `Distributed` tables over `ReplicatedMergeTree` with `internal_replication=true` + per-node `{shard}`/`{replica}` macros; mTLS-only (secure ports 8443/9440/9010 + Keeper 9281) via per-host Vault PKI (PKCS#8). **StarRocks (0.G.6)**: a **3 FE (BDB-JE quorum: 1 leader + 2 followers) + 3 BE** cluster — tables `DISTRIBUTED BY HASH(...) BUCKETS n` with `replication_num=3` so tablets shard across all 3 BE; MySQL protocol on `:9030`. **Client front door = round-robin DNS** (`clickhouse.nexus.lab` / `starrocks-fe.nexus.lab`), **no VRRP VIP** — both engines are any-node-addressable so there's no fixed-endpoint SPOF (resolves ADR-0025's analytics open question in ADR-0031). Backup repository = NFS export from `nexus-gateway` (MinIO/S3 deferred to 0.L, ADR-0032). Per-engine Packer templates (`analytics-clickhouse-{keeper,server}-node`, `analytics-starrocks-{fe,be}-node`) + per-cluster Terraform states + per-cluster smoke gates (`smoke-0.G.5.ps1` / `smoke-0.G.6.ps1`) + 22 System B demos + System A `DEMO-15` + ADRs 0028–0032. RAM right-sized for the lab (logged in `vms.yaml`). |
| 🟡 | `nexus-infra-lakehouse` | Lakehouse tier (`08-spark`, Phase 0.L) — MinIO (S3 object store) + Apache Iceberg (REST catalog + dedicated PG HA) + Apache Spark. **0.L.1 MinIO SEALED 2026-05-23** — a **4-node distributed erasure-coded** cluster (each node a dedicated xfs data drive, default EC:2, tolerates a node loss), mTLS via per-host Vault PKI, client front door = round-robin DNS `minio.nexus.lab` (no VIP, ADR-0033). Live-ratified + **cold-rebuild proven** (`smoke-0.L.1.ps1` 41/41 GREEN; 2 apply-time transients fixed in source — multi-disk preseed + the VMnet10 NO-CARRIER auto-recovery). **0.L.2 Iceberg catalog SEALED 2026-05-24** — **Project Nessie** ×2 stateless instances (round-robin DNS `iceberg.nexus.lab`, Iceberg REST API on HTTPS `:19120`) backed by a **dedicated PostgreSQL 17 master-replica HA pair** (streaming replication + **keepalived VRRP VIP** `iceberg-db.nexus.lab .151` with auto-promotion) — making the lakehouse self-contained; warehouse data in MinIO via a secret-URN S3 credential. Live-ratified + **cold-rebuild proven** (`smoke-0.L.2.ps1` 28/28 GREEN; 8 apply-time transients fixed in source, handbook §3.4; ADR-0034). **0.L.3 Spark SEALED 2026-05-24** — Apache Spark 3.5.3 standalone **HA: 2 masters (ZooKeeper-elected, active/standby) + 3 workers** + a dedicated **3-node Apache ZooKeeper** ensemble (the platform's one deliberate Apache-ZK exception — Kafka is KRaft, ClickHouse uses Keeper; `recoveryMode=ZOOKEEPER`, no master VIP). Spark writes Iceberg tables through the Nessie REST catalog (warehouse-by-name + `S3FileIO`/AWS SDK v2) into the MinIO warehouse — the end-to-end Spark→Iceberg→MinIO write path 0.L.1/0.L.2 deferred. Live-ratified + **cold-rebuild proven** (`smoke-0.L.3.ps1` 28/28 GREEN; 10 apply-time transients fixed in source incl. the executor-RPC firewall gap, handbook §3.6; ADR-0035). Lakehouse tier now 16 VMs. Next: 0.L.4 Harbor (`nexus-infra-registry`) · 0.L.5 StarRocks shared-data/CN (extends analytics). |
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
