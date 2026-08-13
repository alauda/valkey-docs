---
title: Technical writing policy
i18n:
  title:
    en: Technical writing policy
---

# Technical writing policy

## Audience and format

Write for Kubernetes administrators and application operators. Lead with the
supported action, then show a complete CLI command or manifest and a verification
command. Preserve API field names exactly in code and use plain English in prose.

## Required rules

- Use only CLI procedures. Do not describe or imply a Web Console workflow.
- Distinguish product support from values merely accepted by an upstream or legacy
  CRD schema.
- State prerequisites, destructive effects, availability impact, data-retention
  boundaries, and verification steps.
- Do not promise zero downtime, automatic backup, disaster recovery, monitoring
  integration, or compatibility unless the inspected implementation proves it.
- Do not publish a disaster-recovery guide for 2.0.0. State that disaster
  recovery is unsupported and keep it distinct from in-cluster high availability.
- Prefer the high-level `Valkey` resource for user procedures. Treat `Cluster`,
  `Failover`, and `Sentinel` as operator-managed implementation resources unless a
  low-level API reference is specifically required.
- Keep example resource requests and limits equal because the validating webhook
  warns when they differ.
- Never embed real passwords or tokens. Refer to a Kubernetes Secret in the same
  namespace and use interactive authentication with `valkey-cli`.

## Review checklist

Before publishing, verify commands, relative links, CRD names, API groups, field
paths, supported versions, architecture names, Service names, status values, and
unsupported-feature statements.
