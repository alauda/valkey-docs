---
title: Architecture facts
i18n:
  title:
    en: Architecture facts
---

# Architecture facts

Evidence below refers to `valkey-operator` commit
`5962a1a54257ec9e1fec64eccc599ad42f22afd7` (`origin/master`).

## Product contract

- The next major product release is `2.0.0`: `version:1`.
- Packaged Valkey server lines are `7.2`, `8.1`, and `9.1`:
  `values.yaml:17-36`. These are the only supported versions in product docs.
- The high-level API is `rds.valkey.buf.red/v1alpha1`, kind `Valkey`, short name
  `vk`: `api/rds/v1alpha1/groupversion_info.go:27-35` and
  `api/rds/v1alpha1/valkey_types.go:166-183`.
- Supported high-level architecture literals are `cluster`, `failover`, and
  `replica`: `api/rds/v1alpha1/valkey_types.go:66-68`.
- The product has no Web Console. This is an explicit product-owner requirement,
  not an inference from source.

## Implemented user-visible capabilities

| Capability | Evidence | Documentation decision |
| --- | --- | --- |
| Cluster, Failover, and Replica creation | `internal/controller/rds/valkey/cluster.go`, `failover.go` | Document with `Valkey` manifests. |
| Cluster shard/member scaling and Failover/Replica member scaling | operation engines under `internal/ops/cluster` and `internal/ops/failover` | Document online changes with status watching; do not promise uninterrupted service. |
| Upgrade without downgrade | `internal/webhook/rds/v1alpha1/valkey_webhook.go:357-376` | Document increasing `spec.version`; downgrade requires recreation. |
| ACL users and password rotation | `api/v1alpha1/user_types.go`, `internal/controller/user/handler.go` | Document `User` plus Secret, password length 8–32. |
| ClusterIP, NodePort, LoadBalancer, IPv4, IPv6 | `api/core/types.go:112-148`, service builders | Document all Service types; use automatic NodePort allocation in examples. |
| External-access TLS through cert-manager | `internal/builder/certbuilder/cert.go`, operation actors | Document `enableTLS`, issuer fields, and generated `<name>-tls` Secret. |
| Optional PVC storage | `api/core/types.go:75-96`, StatefulSet builders | Document capacity and StorageClass; require explicit PVC review before deletion. |
| Exporter sidecar | `internal/builder/exporter.go`, RDS generators | Document port `9121` and `/metrics`; do not claim an automatic data-plane ServiceMonitor. |
| Scheduling controls | high-level API fields and StatefulSet builders | Document affinity policy, custom affinity, node selector, tolerations, and Pod security context. |
| Hot configuration and restart-required configuration | `internal/builder/config.go`, update-config actors | Document the exact restart/forbidden lists. |
| Pause/resume and rolling restart | `internal/builder/const.go`, ensure-resource actors | Document `spec.podAnnotations` keys. |
| Creation-time Cluster slot plan | `internal/webhook/rds/v1alpha1/valkey_webhook.go:223-249`, `internal/ops/cluster/engine.go:358-405` | Document `spec.replicas.shardsConfig` only for initial allocation; do not describe it as an update-time rebalance API. |
| Startup module directives | Cluster and Failover ConfigMap builders | Document the field boundary but do not claim a packaged module catalog. The referenced module must already exist in the server image; the operator only renders `loadmodule`. |
| Stable role-selecting Services | `internal/builder/failoverbuilder/service.go:33-120` | Document `rfr-<name>-readwrite` and `rfr-<name>-readonly`; advise discovery rather than name construction in automation. |
| Webhook certificate modes | `charts/valkey-operator/templates/certmanager.yaml`, `webhook.yaml`, and chart values | The default chart enables webhooks and cert-manager; disabling cert-manager requires an externally supplied webhook CA bundle. Instance TLS still uses cert-manager resources. |

## Known source conflicts and boundaries

- `ValkeySpec.version` and the generated CRD still accept `8.0` and `9.0` in the
  enum, while the source comment and `values.yaml` explicitly support and package
  only `7.2`, `8.1`, and `9.1`. Product docs label the broader enum as a
  non-supported compatibility surface.
- `replicasOfShard` is described in API comments as replica count, but builders use
  it as a StatefulSet Pod count. Documentation uses the implemented member-count
  behavior and preserves the literal field name in YAML.
- The generated CRD pattern for `spec.access.ports` expects colon-separated pairs,
  while `api/core/helper.ParsePorts` accepts comma-separated ports and dash ranges.
  Do not publish fixed NodePort examples until the schema and parser agree.
- `retainAfterDeleted` requires additional implementation verification: the API
  comment and PVC owner-reference behavior do not provide a sufficiently clear
  retention guarantee. Deletion docs require users to inspect and protect PVCs.
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
