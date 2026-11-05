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
| Alauda Cache Service E1 | — | The sibling product built on Redis® OSS, the releases up to and including Redis server 7.2, and the source of the migration guide. Write “built on Redis® OSS”, not “Redis® technology”. Write the full name wherever text means that product, its instances, its Operator, its API, or its packages. Do not write “the Redis product” or “a Redis deployment”. |
| Redis server | `Redis`, `redis_exporter`, `RDB_TYPE_*` | The upstream server software, named only to identify it by version, as in “Redis server 7.2”. Always an adjective with a generic noun; never a noun, verb, or possessive. Code spans are technical identifiers: they carry no symbol and are never renamed. |
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

## Redis trademark notice and symbol

Redis® is a registered trademark of Redis Ltd. Any rights therein are reserved to Redis Ltd. Any use by Alauda is for referential purposes only and does not indicate any sponsorship, endorsement or affiliation between Redis Ltd. and Alauda.

Alauda is an independent vendor. This product is not affiliated with, endorsed by, or sponsored by Redis Ltd. All trademarks are the property of their respective owners and are used here for identification purposes only.

Those two paragraphs are the notice Alauda Cache Service E1 already ships, verbatim:
`redis-docs` `docs/en/intro.mdx` and `redis-operator` `.artifact/metadata.yaml`.
Reproduce them without rewording. Unlike the Valkey notice, they are not placed
once in `docs/en/intro.mdx`: the Redis trademark policy
(<https://redis.io/legal/trademark-policy/>, last updated 2026-09-15) asks for the
attribution on each page where the mark appears, at the bottom of that page. They
are the `## Trademarks` section of `docs/en/how_to/50-migrate-from-redis.mdx`.

Keep the mark off every other page. Name the product Alauda Cache Service E1
instead, and write “snapshot (RDB)” where a page needs the format but not the
expansion. A new page that must use the mark takes the same notice at its bottom.
Write `Redis®` at the first usage of the mark on a page. The policy text reads
“always add the registration symbol”; first usage is the product owner's decision
of 2026-11-05, and it matches the Valkey rule above.
Do not call this product compatible with Redis server software: the policy bars a
fork from that wording.
