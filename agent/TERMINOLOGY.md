---
title: Canonical terminology
i18n:
  title:
    en: Canonical terminology
---

# Canonical terminology

| Canonical name | API literal or abbreviation | Usage |
| --- | --- | --- |
| Alauda Cache Service E2 | — | Product name. Renamed from “Alauda Build of Valkey”; do not use the former name in new text. |
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
| Alauda Container Platform | — | The platform the product is installed on. Always write the full name; do not abbreviate it to ACP in published pages. |
| Full Support | — | First lifecycle phase of a minor version, from its release date to its end of full support. All bug fixes and security patches are delivered on that minor line. |
| Maintenance | — | Second lifecycle phase, from end of full support to end of maintenance. Only critical bug fixes and security patches are delivered; non-critical fixes are not backported. |
| end of full support | — | The date on which a minor version leaves Full Support. Lowercase in prose; title case only as a table column heading. |
| end of maintenance | — | The date after which a minor version is no longer supported. Lowercase in prose; title case only as a table column heading. |

## Trademark notice and symbol

Valkey® is a registered trademark of LF Projects, LLC.

Alauda is an independent vendor. This product is not affiliated with, endorsed by, or sponsored by LF Projects, LLC. All trademarks are the property of their respective owners and are used here for identification purposes only.

Those two paragraphs are the notice the ACP component compliance rules require
verbatim. Reproduce them without rewording: in the product-listing description
fields, and once in user documentation, where they are the `## Trademarks`
section of `docs/en/intro.mdx`. Write `Valkey®`, never `Valkey™`: the mark is
registered, and the symbol belongs at the first usage of the mark in each display
field. Use the mark as an adjective with a generic noun, for example “Valkey®
server 7.2” and “built on Valkey® technology”. Code spans naming the API kind
`Valkey`, the CRD group, image paths, and field names are technical identifiers:
they carry no symbol and are never renamed.
