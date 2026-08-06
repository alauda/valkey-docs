---
title: Canonical terminology
i18n:
  title:
    en: Canonical terminology
---

# Canonical terminology

| Canonical name | API literal or abbreviation | Usage |
| --- | --- | --- |
| Alauda Build of Valkey | — | Product name. |
| Valkey Operator | — | Kubernetes operator delivered with the product. |
| Valkey | `Valkey`, `vk` | High-level namespaced custom resource. |
| Cluster architecture | `spec.arch: cluster` | Sharded deployment using 16,384 hash slots. |
| Failover architecture | `spec.arch: failover` | Primary-replica deployment monitored by Valkey Sentinel. |
| Replica architecture | `spec.arch: replica` | Primary-replica deployment without Sentinel. One member is a single-node deployment. |
| primary | API status may expose `master` | Preferred prose term for the writable node. Do not rewrite literal API values. |
| replica | — | Preferred prose term for a node replicating from a primary. Do not use “slave”. |
| shard | `spec.replicas.shards` | One Cluster partition. |
| member count per shard | `spec.replicas.replicasOfShard` | Actual Pod count per Cluster shard, or total data-node count for Failover/Replica. The field name is legacy and can be misleading. |
| Valkey Sentinel | `Sentinel` | Failure detector and failover coordinator used by Failover architecture. |
| access control list | ACL | Valkey command, key, and channel permissions managed through `User`. |
| custom resource definition | CRD | Kubernetes API schema installed by the operator. |
| persistent volume claim | PVC | Persistent storage claim created for a Valkey Pod. |
| rolling restart | — | Sequential Pod replacement triggered by configuration, version, template, or restart annotation changes. |
| Cluster-aware client | `valkey-cli -c` | Client that discovers Cluster topology and follows `MOVED` and `ASK` redirections. |
| server line | `spec.version` | Supported Valkey major/minor line packaged by the product, such as 8.1; not the exact container patch build. |
| operator-owned resource | — | Child resource generated and reconciled from `Valkey`; users inspect it but change the owning high-level resource. |
| large key | `valkey-cli --bigkeys` | Key whose size or collection cardinality creates workload-specific operational risk. Do not imply one universal size threshold. |
| Web Console | — | Unsupported for this product; do not provide UI instructions. |

Valkey is a trademark of LF Projects, LLC.
