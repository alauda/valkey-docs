# Project agent instructions

Before writing or changing technical documentation, read:

- `agent/TECHNICAL_WRITING_POLICY.md`
- `agent/TERMINOLOGY.md`
- `agent/ARCHITECTURE_FACTS.md`
- `agent/SOURCE_POLICY.md`

These live at the repository root, outside `docs/`. Anything under `docs/` is
rendered as a documentation route and published; authoring policy must not be.

Treat `TERMINOLOGY.md` as the canonical vocabulary. Verify product claims against
the current `valkey-operator` `origin/master` source, generated CRDs, and packaged
image values. Do not copy Redis product capabilities unless Valkey Operator has a
corresponding implementation.

This product has no Web Console. All procedures must use CLI tools such as
`kubectl`, `valkey-cli`, and standard Kubernetes clients.

Before final output, review terminology and facts and list:

- unregistered terms;
- inconsistent names;
- unsupported claims;
- uncertain translations;
- terms that need human confirmation.
