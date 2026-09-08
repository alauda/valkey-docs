---
title: Source policy
i18n:
  title:
    en: Source policy
---

# Source policy

Use evidence in this order:

1. Explicit product requirements supplied by the product owner.
2. `valkey-operator` `origin/master` product version and image map.
3. API type definitions, admission webhooks, controllers, builders, operation
   engines, and tests from the same `origin/master` commit.
4. Canonical generated custom resource definitions (CRDs) under
   `config/crd/bases`, with separately verified package copies under the Helm
   Chart.
5. The `valkey` container source pinned by the Operator image tag for exact patch
   versions, build flags, installed files, and entrypoint behavior.
6. Official Valkey documentation for command, protocol, persistence, replication,
   access control list, TLS, and client-library behavior; official Kubernetes,
   cert-manager, and Prometheus Operator documentation for their own APIs.

For the initial 2.0.0 documentation, the inspected operator baseline is
`origin/master` commit `20b3b6758fe6f5431e0e183e841d606b935c0853` from
2026-08-11, together with the product-naming commits up to
`0135484d90d7bff19bd56fe5d08ed4657fc26df7`, which rename the display title to
Alauda Cache Service E2 and change no code. Both are ancestors of the shipped
`valkey-operator` tag `v2.0.0`. The server-image baseline pinned by the `v2.0.0`
Operator image tags is `cf797832`, which is `7cb5874adf2c867dc2fc423435e33edc82226caa`
plus one Alpine base-image bump and no server change; it builds Valkey 7.2.14,
8.1.9, and 9.1.1. The release candidate pinned in the `artifacts` registry,
`33812cb4`, is an ancestor of `v2.0.0` and differs from it only in the
`redis-exporter` image tag, so the version, schema, and server-image claims in
these documents hold for either commit.

The following 2.0.0 release facts are product-owner requirements (evidence class
1). They have no source-code evidence and must not be inferred from the
repositories:

- release date 2026-09-08;
- a two-year lifecycle: end of full support 2027-09-30, end of maintenance
  2028-09-30. Lifecycle dates are the release date plus one and two years,
  rounded to the end of that month, following the Alauda Cache Service E1
  convention;
- supported platform versions Alauda Container Platform v4.2, v4.3, and v4.4;
- 2.0.0 has no predecessor product version. The documentation covers the 2.x
  line only: `valkey-operator` `release-1.0`, `release-1.1`, `v1.0.0`, and
  `v1.1.0` are out of scope, no 1.x row belongs in the lifecycle timeline, and no
  1.x-to-2.0.x upgrade path is documented. Because there is no predecessor,
  2.0.0 carries no fixed-issue, known-issue, or security-fix release-note
  sections.

Trademark wording does not come from the upstream project. `TERMINOLOGY.md`
carries the notice and the symbol rule that the ACP component compliance rules
prescribe, and it is the source for every mark, notice, and symbol in these
documents.

The local `valkey` repository is container-source packaging rather than the
upstream server source tree. Do not use it as evidence for command or protocol
semantics; use official Valkey documentation for those claims.

When sources conflict, document the narrower product contract and record the
conflict in `ARCHITECTURE_FACTS.md`. Do not silently choose the broader claim.
Generated CRDs are authoritative for what the Kubernetes API server accepts;
runtime code is authoritative for reconciliation behavior after admission.
When multiple generated copies disagree, document the conflict and verify the
artifact actually installed by the release package. The official Valkey command,
topic, and client indexes begin at <https://valkey.io/commands/>,
<https://valkey.io/topics/>, and <https://valkey.io/clients/>.

The absence of an Operator API, controller, builder, or explicitly supported
integration is not a reason to create a feature guide. Record an absent
capability only where readers need the product boundary, such as Current
limitations or release notes. Do not create an external-workaround chapter under
Guides unless the product owner explicitly defines that integration as supported.

Do not expose Operator-owned child CRDs as public authoring APIs. Public API
reference pages are limited to resources that users are expected to create or
update directly; internal resources may be named only when diagnostics require
observing them without modification.
