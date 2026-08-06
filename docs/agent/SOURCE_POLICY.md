---
title: Source policy
i18n:
  title:
    en: Source policy
---

# Source policy

Use evidence in this order:

1. Explicit product requirements supplied by the product owner.
2. `valkey-operator` `origin/master` packaged values, version file, and generated CRDs.
3. API type definitions, admission webhooks, controllers, builders, operation
   engines, and tests from the same `origin/master` commit.
4. Official Kubernetes, cert-manager, Prometheus Operator, or Valkey documentation
   for external behavior not defined by the operator.

For the initial 2.0.0 documentation, the inspected operator baseline is commit
`5962a1a54257ec9e1fec64eccc599ad42f22afd7` from 2026-08-05.

When sources conflict, document the narrower product contract and record the
conflict in `ARCHITECTURE_FACTS.md`. Do not silently choose the broader claim.
Generated CRDs are authoritative for what the Kubernetes API server accepts;
runtime code is authoritative for reconciliation behavior after admission.
