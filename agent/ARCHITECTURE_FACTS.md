---
title: Architecture facts
i18n:
  title:
    en: Architecture facts
---

# Architecture facts

Evidence below refers to `valkey-operator` commit
`20b3b6758fe6f5431e0e183e841d606b935c0853` (`origin/master`, 2026-08-11) plus
the code-free product-naming commits up to
`0135484d90d7bff19bd56fe5d08ed4657fc26df7`.
Both are ancestors of the shipped tag `v2.0.0`.
The server-image facts refer to the `valkey` container-source commit
`7cb5874adf2c867dc2fc423435e33edc82226caa`. The `v2.0.0` 7.2, 8.1, and 9.1 image
tags carry the suffix `cf797832`, which is that commit plus one Alpine
base-image bump and no server change; it still builds 7.2.14, 8.1.9, and 9.1.1.

## Product contract

- The product name is Alauda Cache Service E2, renamed from Alauda Build of
  Valkey: `.artifact/metadata.yaml` `displayName` and the OLM
  ClusterServiceVersion base, commits `ebb4cbaf` through `0135484d`. Those
  commits carried the upstream notice "Valkey and the Valkey logo are
  trademarks of LF Projects, LLC." Commit `8702ca3`, merged to `release-2.0` as
  `8f7e2cc` on 2026-09-05, replaced that notice in all four listing description
  fields with the notice the ACP component compliance rules require verbatim, and
  writes `Valkey®` at the first usage of the mark in every listing field. The
  current wording is in `TERMINOLOGY.md`. The `valkey-operator` `master` branch
  still carries the superseded notice until that change is cherry-picked.
- The current product release is `2.0.0`: `version:1`, shipped as
  `valkey-operator` tag `v2.0.0`. It was released on 2026-09-08 and runs on
  Alauda Container Platform v4.2, v4.3, and v4.4. Release date, lifecycle dates,
  and platform versions are product-owner requirements with no source-code
  evidence; see `SOURCE_POLICY.md`.
- The Operator image map selects Valkey server lines `7.2`, `8.1`, and `9.1`:
  `values.yaml:17-34`. These are the only supported versions in product docs.
  The pinned container source builds patch versions 7.2.14, 8.1.9, and 9.1.1.
- The high-level API is `rds.valkey.buf.red/v1alpha1`, kind `Valkey`, short name
  `vk`: `api/rds/v1alpha1/groupversion_info.go:27-35` and
  `api/rds/v1alpha1/valkey_types.go:166-183`.
- Supported high-level architecture literals are `cluster`, `failover`, and
  `replica`: `api/rds/v1alpha1/valkey_types.go:66-68`.
- The public authoring APIs are `Valkey` and `User`. `Cluster`, `Failover`, and
  `Sentinel` are Operator-owned implementation resources generated from
  `Valkey`; they must not be published as directly usable API references.
- The product has no Web Console. This is an explicit product-owner requirement,
  not an inference from source.

## Implemented user-visible capabilities

| Capability | Evidence | Documentation decision |
| --- | --- | --- |
| Cluster, Failover, and Replica creation | `internal/controller/rds/valkey/cluster.go`, `failover.go` | Document with `Valkey` manifests. |
| Cluster shard/member scaling and Failover/Replica member scaling | operation engines under `internal/ops/cluster` and `internal/ops/failover` | Document in-place changes with status watching; do not promise uninterrupted service. |
| Upgrade without downgrade | `internal/webhook/rds/v1alpha1/valkey_webhook.go:366-373` | Document that downgrade is rejected. The source does not define a tested forward upgrade matrix. |
| Access control list (ACL) users and password rotation | `api/v1alpha1/user_types.go`, `internal/controller/user/handler.go` | Document `User` plus Secret, password length 8–32. |
| ClusterIP, NodePort, LoadBalancer, IPv4, IPv6 | `api/core/types.go:112-148`, service builders | Document all Service types; use automatic NodePort allocation in examples. |
| TLS through cert-manager | `internal/builder/certbuilder/cert.go`, operation actors, and `cmd/run_*.sh` | Document `enableTLS`, issuer fields, generated `<name>-tls` Secret, default client-certificate authentication, and the exact generated DNS-name boundary. |
| Optional persistent volume claim (PVC) storage | `api/core/types.go:75-96`, StatefulSet builders | Document capacity and StorageClass; require explicit PVC review before deletion. |
| Exporter sidecar | `internal/builder/exporter.go`, high-level resource generators | Document port `9121` and `/metrics`; do not claim an automatic data-plane ServiceMonitor. |
| Scheduling controls | high-level API fields and StatefulSet builders | Document node selector, tolerations, and Pod security context. Describe affinity from builder behavior, not the broader API comments. |
| Hot configuration and restart-required configuration | `internal/builder/config.go`, update-config actors | Document the exact restart/forbidden lists. |
| Pause/resume and rolling restart | `internal/builder/const.go`, ensure-resource actors | Document `spec.podAnnotations` keys. |
| Creation-time Cluster slot plan | `internal/webhook/rds/v1alpha1/valkey_webhook.go:223-249`, `internal/ops/cluster/engine.go:358-405` | Document `spec.replicas.shardsConfig` only for initial allocation; do not describe it as an update-time rebalance API. |
| Startup module directives | Cluster and Failover ConfigMap builders; pinned server Dockerfiles | The Operator only renders `loadmodule`. The standard inspected images install no module files, and the high-level API has no image override; do not present modules as an available standard-image capability. |
| Stable role-selecting Services | `internal/builder/failoverbuilder/service.go:33-120` | Document `rfr-<name>-readwrite` and `rfr-<name>-readonly`; advise discovery rather than name construction in automation. |
| Webhook certificate modes | `charts/valkey-operator/templates/certmanager.yaml`, `webhook.yaml`, and chart values | The default chart enables webhooks and cert-manager; disabling cert-manager requires an externally supplied webhook certificate authority (CA) bundle. Instance TLS still uses cert-manager resources. |
| Credential protection | `pkg/security/password` ACL hashing, `pkg/security/crypto` and `internal/ops/util/crypto.go` per-instance key Secret, `internal/builder/exporter.go` Secret-name resolution, removed `/account` mounts, `cmd/run_*.sh` marker-gated encryption, sentinel tmpfs config, `internal/builder/helper.go` restricted security contexts; I: image `80_alauda_crypto.patch` `CONFIG GET` redaction | Document hashed ACL passwords, encrypted config credentials, redacted `CONFIG GET`, exporter Secret-name flow, and restricted-profile security contexts. Do not claim wire-level encryption: Sentinel `auth-pass` is applied live via `SENTINEL SET`. |

## Known source conflicts and boundaries

- `ValkeySpec.version` and the canonical custom resource definition (CRD) under
  `config/crd/bases` accept `8.0` and `9.0` in the enum,
  while `values.yaml` maps only `7.2`, `8.1`, and `9.1`. The pinned `valkey`
  container baseline builds only the three supported lines; earlier baselines
  also carried 8.0 and 9.0 build directories. Product docs label the broader
  schema surface as unsupported.
- The replication credential directive is not spelled the same on every supported
  line. The credential-encryption patch registers it as `masterauth` with no
  alias on 7.2 (`valkey/7.2/alpine/80_alauda_crypto.patch:146`) and as
  `primaryauth` with `masterauth` as its alias on 8.1 and 9.1
  (`valkey/8.1/alpine/80_alauda_crypto.patch:147`,
  `valkey/9.1/alpine/80_alauda_crypto.patch:146`);
  `internal/builder/config.go` forbids both spellings and records the rename in a
  comment. `requirepass` is registered with no alias on all three lines. Any
  passage that tells a reader to inspect the replication credential must name the
  directive per line: `CONFIG GET primaryauth` returns an empty result on 7.2,
  which reads as "not set" while the credential is in fact held by `masterauth`.
- The Helm Chart CRD copy under `charts/valkey-operator/crds` is stale at tag
  `v2.0.0`: it omits 9.0 and 9.1, while the API type and canonical generated CRD
  include them. That copy is not the delivered artifact. The chart is an upstream
  directory untouched since commit `d430d34`, referenced by neither the
  `Makefile` nor `.build/build.yaml`; `.artifact/metadata.yaml` declares
  `packageType: OperatorBundle`, and `make bundle` generates the bundle from
  `config/manifests`, which pulls `config/crd/bases`. Product docs therefore
  require installed-schema verification during release acceptance without
  attributing the risk to the in-repo chart copy. The stale copy should still be
  regenerated or removed in `valkey-operator`.
- `replicasOfShard` is described in API comments as replica count, but builders use
  it as a StatefulSet Pod count. Documentation uses the implemented member-count
  behavior and preserves the literal field name in YAML.
- `spec.access.ports` is a NodePort list: the API comment reads "the nodeports
  of NodePort service" and `api/core/helper.ParsePorts` returns a flat port
  list from comma-separated ports and dash ranges. The generated CRD pattern
  instead requires colon-separated numeric pairs (`port:port`, not `ip:port`),
  a format no runtime code consumes, so every schema-valid value fails
  admission when `serviceType` is `NodePort`. Do not publish fixed NodePort
  examples until the schema and parser agree.
- `retainAfterDeleted` has no effect at the inspected baseline: both StatefulSet
  builders assign PVC owner references to a value-copy inside a range loop, so
  generated PVCs never carry an instance owner reference and no reconcile path
  deletes them. Deletion always leaves PVCs behind; docs direct users to
  inspect and explicitly clean up leftover PVCs. `spec.storage.accessMode` is
  likewise ignored: both builders hard-code `ReadWriteOnce`.
- Disaster recovery is not supported. The repository contains no integrated
  backup/restore controller, cross-cluster replication or failover implementation,
  disaster-recovery API, integrated large-key inspection, parameter-template API,
  or Web Console. Do not migrate those Redis guides.
- The shipped ServiceMonitor selects the operator metrics Service, not Valkey
  data-plane Services. The exporter is implemented, but data-plane scrape
  integration is environment-specific.
- `valkey-cli --bigkeys` is a server CLI troubleshooting mechanism, not an
  operator-provided inspection report. In Cluster architecture it must be scoped
  across current primary nodes to cover the whole dataset.
- In `api/core/types.go`, `CustomAffinity` has the empty-string value, while the
  CRD admits the literal `CustomAffinity`. The Cluster StatefulSet switch compares
  against the empty value, so the admitted literal does not activate the custom
  branch. Its `AntiAffinity` and `AntiAffinityInShard` branches both receive a
  shard-specific selector and therefore have the same required placement scope.
  The Cluster ensure-resource actor additionally copies the old StatefulSet's
  affinity on update, so affinity changes never reconcile onto existing Cluster
  StatefulSets. Failover and Replica ignore the policy switch and copy non-nil
  `spec.customAffinity` directly.
- The high-level webhook enforces only `shards >= 3`; the 128 upper bound is
  enforced by the child Cluster CRD schema. An oversized shard count passes
  high-level admission and surfaces later as a `Failed` instance.
- Generated server commands do not set `tls-auth-clients no`, so upstream Valkey
  defaults to requiring a trusted client certificate. Cluster certificates cover
  `<name>`, `<name>.<namespace>`, and shard headless-Service names. Failover
  certificates cover role-Service names in `<service>.<namespace>` form and
  selected Sentinel names. They do not cover arbitrary external addresses or all
  `.svc` suffixes.
- Cluster access creates a per-Pod Service even for `ClusterIP`, and the init
  path resolves that Service into the address announced to clients. Those
  per-Pod Service names/addresses are absent from the generated certificate. The
  Operator's Go clients set `InsecureSkipVerify: true`, so internal reconciliation
  does not prove that a normal client can validate redirected Cluster endpoints.
- `status.lastVersion` is assigned from desired `spec.version`, and
  `status.lastShardCount` is controller bookkeeping. Neither is sufficient proof
  of every running Pod's state.
- `pkg/types/user/user.go` defines a username regex for 0–31 letters, digits, or
  hyphens, but the custom User admission path does not call `User.Validate`.
  Documentation recommends a non-empty value in that narrow range and warns that
  admission is not proof of runtime acceptance.
- `rename-command` is marked `RequireRestart` in `internal/builder/config.go`,
  but both topology ConfigMap builders skip it. `IsForbiddenValkeyConfig`
  lowercases and trims keys before lookup, and the Cluster, Failover, and
  Sentinel renderers all apply it, so forbidden directives are filtered
  case-insensitively. The forbidden set also covers `primaryauth`,
  `primaryuser`, `tls-key-file-pass`, and `tls-client-key-file-pass`. Product
  docs mark `rename-command` unavailable and use lowercase configuration keys.
