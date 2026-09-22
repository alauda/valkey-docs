---
title: Content evidence audit
i18n:
  title:
    en: Content evidence audit
---

# Content evidence audit

This ledger records the evidence used to review every English public page for
the 2.0.0 baseline. Each prose paragraph is governed by the row for its nearest
section heading. Command examples are checked against the same implementation or
official command source as the surrounding section.

## Baselines and claim classes

- **P — product requirement:** the product name Alauda Cache Service E2,
  CLI-only operation, supported server lines 7.2/8.1/9.1, and no disaster
  recovery. For the 2.0.0 release: release date 2026-09-08, a two-year lifecycle
  ending full support on 2027-09-30 and maintenance on 2028-09-30, and support on
  Alauda Container Platform v4.2, v4.3, and v4.4, and no predecessor product
  version — the 2.x line only. These release facts are supplied by the product
  owner and have no source-code evidence; `SOURCE_POLICY.md` records them.
- **O — Operator implementation:** `valkey-operator` `origin/master` commit
  `20b3b6758fe6f5431e0e183e841d606b935c0853`, plus the code-free
  product-naming commits up to `0135484d90d7bff19bd56fe5d08ed4657fc26df7`.
- **I — image implementation:** `valkey` commit
  `7cb5874adf2c867dc2fc423435e33edc82226caa`, pinned by the Operator image tags.
- **U — upstream behavior:** official Valkey command, topic, and client pages.
- **S — upstream server source:** format constants and the code that gates them,
  read at an exact released tag and cited by file and line. Used only where the
  official documentation states no number: `valkey-io/valkey` `7.2.14`, `8.1.9`,
  and `9.1.1`, and — for the source side of a Redis comparison only —
  `redis/redis` `6.0.20`, `7.2.16`, and `8.4.0`. See `SOURCE_POLICY.md` class 7.
- **E1 — sibling product implementation:** `redis-operator` and its packaged
  images, cited by file and line. Used only to state what a migration source
  looks like, never to claim a capability of this product.
- **V — runtime verification:** behavior observed on delivered images running
  under both Operators on a real cluster, recorded with the commands used and
  the exact server replies. It confirms a claim already sourced from O, I, S, or
  U; it never substitutes for one, because a single environment cannot establish
  a product contract.
- **D — derived operational rule:** a conclusion whose premises are cited in the
  same row. Derived rules use conditional wording and do not claim an Operator
  guarantee.

The local `valkey` repository is container build and entrypoint source, not the
upstream Valkey server source tree. It is authoritative here for the exact patch
download, TLS build flag, runtime files, and image contents only. Command and
protocol semantics come from the official Valkey documentation.

## Release-level conflicts

| Conflict | Evidence | Documentation resolution |
| --- | --- | --- |
| Supported 9.1 versus stale in-repo Helm Chart custom resource definition (CRD) copy | O: at tag `v2.0.0`, `api/rds/v1alpha1/valkey_types.go` and `config/crd/bases/rds.valkey.buf.red_valkeys.yaml` enumerate `7.2`/`8.0`/`8.1`/`9.0`/`9.1`, while `charts/valkey-operator/crds/rds.valkey.buf.red_valkeys.yaml` still enumerates `7.2`/`8.0`/`8.1`. The chart is an upstream artifact untouched since `d430d34`, is referenced by neither the `Makefile` nor `.build/build.yaml`, and is not the delivered package: `.artifact/metadata.yaml` declares `packageType: OperatorBundle`, and `make bundle` generates the bundle from `config/manifests`, which pulls `config/crd/bases`. | Declare the P support range and require installed-schema verification during release acceptance. Do not attribute the risk to the in-repo chart copy, which customers never install. |
| Broader 8.0/9.0 source surface | O: high-level enum and version map at tag `v2.0.0`, whose API comment records that no image is shipped for `8.0`/`9.0` and that without a `VALKEY_VERSION_MAP` entry they resolve to a bare `:8.0`/`:9.0` tag; I: the pinned baseline builds only 7.2/8.1/9.1 and removed the 8.0/9.0 directories | The version map and P contract are narrower: only 7.2/8.1/9.1 are supported; `8.0`/`9.0` are admissible but unshipped, untested, and usable only with a self-supplied image. |
| Affinity schema versus builders | O: `api/core/types.go`; `internal/builder/clusterbuilder/statefulset.go`; `internal/builder/failoverbuilder/statefulset.go`; the Cluster ensure-resource actor preserving old StatefulSet affinity on update | Document exact builder behavior, mark Cluster custom affinity unreliable, and document that Cluster affinity changes reach only StatefulSets created afterward. |
| Ignored `spec.storage.accessMode` | O: `api/core/types.go` default plus hard-coded `ReadWriteOnce` in the Cluster and Failover StatefulSet builders | Document the fixed `ReadWriteOnce` access mode and remove the access-mode choice from examples. |
| Dead `retainAfterDeleted` and PVC ownership | O: value-copy owner-reference loop in both StatefulSet builders; no production PVC-deletion path | Document that PVCs always remain after deletion and require explicit cleanup. |
| Shard upper bound enforced downstream | O: high-level webhook checks only `shards >= 3`; child Cluster CRD schema enforces 3–128 | Document the 3–128 contract and the late `Failed` outcome for oversized values. |
| Credential protection versus wire behavior | O: security-hardening paths listed in `ARCHITECTURE_FACTS.md`; I: image `CONFIG GET` redaction patch | Document storage-side hardening only; Sentinel `auth-pass` is still applied live via `SENTINEL SET`. |
| Fixed NodePort format | O: `api/core/types.go` pattern and `api/core/helper.ParsePorts` | Leave `spec.access.ports` unset in all examples. |
| TLS names and mutual authentication | O: certificate actors, `certbuilder`, `cmd/run_*.sh`, Cluster access Services/announcement init, and internal TLS configs; U: [TLS](https://valkey.io/topics/encryption/) | Require a certificate authority (CA) plus client certificate/key and an exact generated DNS name. Record that Cluster-announced per-Pod endpoints are absent from the certificate and never recommend `--insecure`. |
| Forward upgrade path | O: downgrade-only webhook comparison | Do not invent sequential or skipped-line support; require a release-tested matrix. |
| Status bookkeeping versus runtime state | O: high-level controller status assignments | Label `lastVersion` and `lastShardCount` as recorded values and verify Pods/server commands. |
| Module schema versus delivered images | O: ConfigMap builders; I: Dockerfiles and installed-file set | Explain that only `loadmodule` is rendered and standard images supply no module. |
| User username helper versus admission | O: `pkg/types/user/user.go` and `internal/webhook/v1alpha1/user_webhook.go` | Recommend the narrow helper range, but state that admission does not enforce it. |
| Configuration policy map versus rendering | O: `internal/builder/config.go` (`IsForbiddenValkeyConfig`) and the Cluster, Failover, and Sentinel ConfigMap builders | Exclude skipped `rename-command`, document case-insensitive forbidden-key filtering including the credential and TLS-passphrase directives, and distinguish restart classification from actual image/module availability. |

## Product and navigation pages

| Page and section | Claim basis |
| --- | --- |
| `index.mdx` — navigation | Site tree only; this landing page contains no product capability claims. |
| `intro.mdx` — release and server versions | P: release date and supported Alauda Container Platform versions; O: high-level enum and version map. |
| `intro.mdx` — reconciliation and capabilities | O: high-level controllers, topology actors, service/config/exporter/certificate builders, the credential-protection paths, and APIs; I: `CONFIG GET` redaction. |
| `intro.mdx` — architectures and member counts | O: webhook/defaulting logic and StatefulSet replica assignments; U: [Cluster specification](https://valkey.io/topics/cluster-spec/), [replication](https://valkey.io/topics/replication/), and [Sentinel](https://valkey.io/topics/sentinel/). |
| `intro.mdx` — responsibility boundary | P for unsupported backup, restore, disaster recovery, and Web Console; D from asynchronous replication and absence of protection controllers. |
| `architecture.mdx` — resource translation | O: `internal/controller/rds/valkey/cluster.go`, `failover.go`, and owner references in builders. |
| `architecture.mdx` — Cluster | O: Cluster engine/builders; U: [Cluster specification](https://valkey.io/topics/cluster-spec/). |
| `architecture.mdx` — Failover and Replica | O: high-level Failover generation, monitor selection, role-Service selectors; U: [replication](https://valkey.io/topics/replication/) and [Sentinel](https://valkey.io/topics/sentinel/). |
| `architecture.mdx` — storage and status | O: storage API/builders and phase constants/controller assignments; D for failure-domain and data-protection cautions. |
| `installation.mdx` — prerequisites and defaults | P: supported Alauda Container Platform versions. O: Helm values/templates, CRDs, role-based access control, webhook, cert-manager, labels, and metrics Service. Registry, namespace, channel, and approval strategy remain distribution-specific and explicitly unknown. |
| `upgrade.mdx` — scope and platform compatibility | P: no predecessor product version, supported Alauda Container Platform versions, and the v4.1 exclusion. The product source-to-target upgrade path for later 2.0.x releases remains delegated to release metadata. |
| `upgrade.mdx` — product upgrade | O: chart resources and API ownership; D from compatibility risk. Package commands and rollback contract are explicitly delegated to release metadata. |
| `lifecycle_policy.mdx` — timeline, compatibility, and support phases | P: release date 2026-09-08, end of full support 2027-09-30, end of maintenance 2028-09-30, and Alauda Container Platform v4.2/v4.3/v4.4. The phase, release, and maintenance wording follows the Alauda Cache Service E1 policy adapted to Valkey. |
| `lifecycle_policy.mdx` — version dimensions | P, O `version`/`values.yaml`, O status assignment, and I exact Dockerfile versions. |
| `limitations.mdx` — unsupported and conflicting features | P plus repository-absence checks and each conflict in the release-level table above. |
| `limitations.mdx` — migration from other products | O: absence of an import controller, restore workflow, or external-primary field, and the `internal/builder/config.go` forbidden entries for `replicaof`, `slaveof`, `dir`, and `dbfilename`; S: the reserved RDB format-version and payload-type ranges. |
| `release_notes.mdx` — compatibility and support matrix | P: release date and supported Alauda Container Platform versions; O: supported server lines. |
| `release_notes.mdx` — 2.0.0 scope | P, O implemented feature paths including the credential-protection hardening, I exact patch baseline and `CONFIG GET` redaction, and the recorded source conflicts. |

## Feature guides

| Page and section | Claim basis |
| --- | --- |
| `functions/index.mdx` | Site tree only; this landing page contains no product capability claims. |
| `10-create-instance.mdx` — fields and validation | O: high-level type, mutating/validating webhook, high-level resource generators, and resource/storage defaults. |
| `10-create-instance.mdx` — three manifests | O: architecture-specific generation and validation; examples use only P-supported versions and automatic NodePort allocation. |
| `10-create-instance.mdx` — verification | O status/labels/resources; U: [`CLUSTER INFO`](https://valkey.io/commands/cluster-info/) and [`INFO`](https://valkey.io/commands/info/). |
| `15-delete-instance.mdx` | O owner references/finalizers, and the builder paths that leave generated persistent volume claims (PVCs) without instance owner references, so PVCs remain after deletion; D for stopping writes and external protection. |
| `20-user.mdx` — requirements and lifecycle | O: User types, webhook, access control list (ACL) builder, controller, and Secret validation; O: `pkg/types/user/user.go` `NewUser` for the passwordless `default` account and its disallowed `acl`/`flushall`/`flushdb`/`keys` commands; U: [ACL](https://valkey.io/topics/acl/) and [`ACL SETUSER`](https://valkey.io/commands/acl-setuser/). |
| `20-user.mdx` — password rotation | O: ordered `passwordSecrets` processing resets the user then adds every supplied password. |
| `30-parameter.mdx` — hot/restart/forbidden behavior | O: `internal/builder/config.go`, ConfigMap builders, update-config actors, and the Sentinel monitor passthrough via `SENTINEL SET`; I: image `CONFIG GET` credential redaction; U: [`CONFIG GET`](https://valkey.io/commands/config-get/) and [command index](https://valkey.io/commands/). |
| `40-accessmethod.mdx` — Services | O: Cluster/Failover service builders, access type, Internet Protocol (IP) family, and port parser. |
| `40-accessmethod.mdx` — TLS | O certificate builders/actors and server scripts; I `BUILD_TLS=yes`; U: [TLS](https://valkey.io/topics/encryption/). |
| `50-update.mdx` | O: high-level propagation and topology operation engines; D for serialized changes and capacity checks. |
| `60-scheduling.mdx` | O: API constant values, exact Cluster/Failover/Sentinel StatefulSet builder branches, and the Cluster ensure-resource actor that preserves existing StatefulSet affinity on update. |
| `75-monitor.mdx` | O: exporter builder including Secret-name password resolution, Cluster and Failover metrics Services (the Failover metrics Service doubles as the governing headless Service), and chart ServiceMonitor selector. |
| `80-log.mdx` | O container names and labels; standard `kubectl logs`/Events behavior. |
| `85-start-stop.mdx` | O pause annotation check and workload scale-to-zero actors; D for data/availability precautions. |
| `90-restart.mdx` | O restart timestamp parsing and rolling workload update; D for topology/client-dependent availability. |

## How-to and troubleshooting pages

| Page and section | Claim basis |
| --- | --- |
| `how_to/index.mdx` | Site tree only; this landing page contains no product capability claims. |
| `20-cluster-scaling.mdx` | O Cluster engine slot migration and shard workload changes; U: [Cluster specification](https://valkey.io/topics/cluster-spec/), [`CLUSTER INFO`](https://valkey.io/commands/cluster-info/), and [`CLUSTER NODES`](https://valkey.io/commands/cluster-nodes/). |
| `30-upgrade.mdx` | O high-level version propagation and downgrade validation; U target-version [commands](https://valkey.io/commands/) and [`INFO`](https://valkey.io/commands/info/); forward matrix explicitly unknown. |
| `40-cluster-slots-distribution.mdx` | O create validation and Cluster engine initialization; U: [`CLUSTER SHARDS`](https://valkey.io/commands/cluster-shards/) and [Cluster specification](https://valkey.io/topics/cluster-spec/). |
| `50-migrate-from-redis.mdx` — Operator boundary | O: no import controller, restore workflow, or external-primary field anywhere in `api/`, `internal/`, `pkg/`, or `cmd/`; `internal/builder/config.go` forbids `replicaof`, `slaveof`, `dir`, and `dbfilename`. |
| `50-migrate-from-redis.mdx` — serialization format and matrix | S: `RDB_VERSION` and the `RDB_FOREIGN_VERSION_MIN`/`MAX` and `RDB_FOREIGN_TYPE_MIN`/`MAX` constants in `src/rdb.h`, the `RDB_TYPE_*` code lists and version gates in `src/rdb.h` and `src/rdb.c`, `verifyDumpPayload` in `src/cluster.c`, and the `rdb-version-check` default in `src/config.c`, at each cited tag (Redis 6.0.20 defines type codes 0-15 only; 7.2.16 and 8.4.0 reach 21 and 25); I: the patch versions the delivered image tags build. The nine-cell matrix is D from those gates, and V: on a live cluster the delivered 6.0.22, 7.2.16, 8.1.9 and 9.1.1 images reported footer versions 9, 11, 11 and 80; Redis 6.0 and 7.2 datasets ported whole into 8.1 and 9.1; and a payload differing only in its footer version was accepted at 11 and refused at 12 on both target lines. |
| `50-migrate-from-redis.mdx` — unsupported dataset content | O: ConfigMap builders render `loadmodule` only; I: the installed-file set supplies no module. P: active-active has no counterpart in this product. S: the hash field-expiration type codes on both sides. |
| `50-migrate-from-redis.mdx` — specification translation | E1: `redis-operator` `api/middleware/v1/redis_types.go` field set and its `values.yaml` image map; O: `api/rds/v1alpha1/valkey_types.go` and `api/core/types.go`. Every unmapped row is an absence check against the target API. |
| `50-migrate-from-redis.mdx` — one-shot copy and window sizing | O: absence of any replication or change-stream path, and the `replicaof`/`slaveof` forbidden entries; V: 20,000 small string keys copied into a three-shard Cluster target in 38 s on an idle cluster, quoted as an order of magnitude only. D: the outage equals the copy duration. |
| `50-migrate-from-redis.mdx` — `COPY`/`--cluster-copy` | U: [`MIGRATE`](https://valkey.io/commands/migrate/) moves a key unless `COPY` is given; V: without `COPY` the source went from 210 keys to 0, and with `COPY REPLACE` it retained all 302 while the target received 302 with time to live intact; `--cluster-copy` likewise retained a 20,000-key source. |
| `50-migrate-from-redis.mdx` — Cluster-wide counting and clearing | V: on a three-shard target, `FLUSHALL` sent to the bootstrap Service reduced the total from 20,102 to 13,373 — one shard — and `DBSIZE` likewise answers for the serving node only. |
| `50-migrate-from-redis.mdx` — choosing the target version | O: the downgrade-rejecting webhook comparison; S: `RDB_VERSION` 80 on `valkey-io/valkey` `9.1.1` versus 11 on `8.1.9` and `7.2.14`; V: a same dataset copied whole into 7.2, 8.1 and 9.1 targets with no procedural difference (Redis 7.2 -> Valkey 7.2 505/505; Redis 6.0 -> Valkey 7.2 20,000/20,000; Redis Cluster -> Valkey 7.2 Cluster 2,675/2,675); `CONFIG SET rdb-version-check` on 7.2 returns `ERR Unknown option`; and a format-80 payload taken from 9.1 was refused by 8.1, by 7.2 and by Redis 7.2 while restoring into 9.1 itself. |
| `50-migrate-from-redis.mdx` — procedure matrix and the cross-slot rule | U: [`MIGRATE`](https://valkey.io/commands/migrate/) multi-key form and [`CLUSTER GETKEYSINSLOT`](https://valkey.io/commands/cluster-getkeysinslot/); V: a `SCAN`-batched `MIGRATE ... KEYS` out of a Redis Cluster source failed every batch with `CROSSSLOT Keys in request don't hash to the same slot`, while the same dataset copied whole (2,675/2,675, 0 failures) once batched per slot — into a Failover target and into a Cluster target alike, with source retained, types, hash-tag grouping and time to live intact. The deciding factor is the source's cluster mode, not the target's. |
| `50-migrate-from-redis.mdx` — slot-walk cost | V: 2,675 keys over 2,346 occupied slots took ~260 s into a Cluster target and ~300 s into a Failover target, ~60 ms per slot over all 16,384, dominated by per-call connection setup in the sketch given. Quoted as an order of magnitude with the measurement conditions stated. |
| `50-migrate-from-redis.mdx` — copy procedure | U: [`MIGRATE`](https://valkey.io/commands/migrate/), [`RESTORE`](https://valkey.io/commands/restore/), [`SCAN`](https://valkey.io/commands/scan/), and [Valkey CLI](https://valkey.io/topics/cli/); S: `clusterManagerCommands[]` in `src/valkey-cli.c` for the `--cluster import` subcommand and its credential options. The procedure is not run by the Operator and carries no Operator guarantee. |
| `50-migrate-from-redis.mdx` — client re-pointing | O: `internal/builder/failoverbuilder/service.go` and `internal/builder/clusterbuilder/service.go` name generators, and the hardcoded `mymaster` monitor name in `internal/controller/failover_controller.go`; E1: the corresponding `redis-operator` generators. |
| `50-migrate-from-redis.mdx` — cutover and rollback | D from the absence of any rollback path, plus the recorded PVC-retention behavior. |
| `access/index.mdx` | Site tree only; this landing page contains no product capability claims. |
| `access/10-failover.mdx` | O role selectors, Sentinel/Replica generation, TLS DNS names; U: [replication](https://valkey.io/topics/replication/), [Sentinel](https://valkey.io/topics/sentinel/), [TLS](https://valkey.io/topics/encryption/), and [clients](https://valkey.io/clients/). |
| `access/20-cluster.mdx` | O bootstrap/per-Pod Services and announced endpoints; U: [Cluster specification](https://valkey.io/topics/cluster-spec/), [`CLUSTER SHARDS`](https://valkey.io/commands/cluster-shards/), [TLS](https://valkey.io/topics/encryption/), and [clients](https://valkey.io/clients/). |
| `trouble_shooting/index.mdx` | Site tree only; this landing page contains no product capability claims. |
| `10-instance-not-ready.mdx` | O admission, scheduling, storage, certificate, container, label, and topology paths; U command references linked on the page. |
| `20-large-key-analysis.mdx` | P/O absence of integrated report; U: [Valkey CLI](https://valkey.io/topics/cli/); Cluster-local coverage follows U Cluster keyspace distribution and O node status. |

## API pages

| Page and section | Claim basis |
| --- | --- |
| `apis/index.mdx` and `kubernetes_apis/index.mdx` | Site tree only; these landing pages contain no product capability claims. |
| `valkey/index.mdx` | Site tree only; this landing page contains no product capability claims. |
| `valkey/valkey.mdx` | O high-level type, webhook, controller status assignments, builders, and canonical CRD. |
| `valkey/user.mdx` | O User type, webhook, controller, ACL builder, and canonical CRD. |

## Review rule

If a future edit introduces a new capability, guarantee, command option, schema
field, generated name, version, or compatibility claim, add or update an evidence
row before publishing. If implementation, packaged schema, and upstream behavior
disagree, publish the narrower verified boundary and retain the conflict in this
ledger and `ARCHITECTURE_FACTS.md`.
