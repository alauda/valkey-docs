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
Alauda Cache Service E2, align trademark usage with upstream, and change no
code. The server-image baseline pinned by the Operator image tags is
`7cb5874adf2c867dc2fc423435e33edc82226caa`.

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
