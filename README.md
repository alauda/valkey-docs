# Alauda Build of Valkey documentation

This repository contains the CLI-focused product documentation for Alauda Build
of Valkey. The site uses the same Doom documentation toolchain as `redis-docs`.

## Local development

Install Node.js and Yarn, then run:

```bash
yarn install
yarn dev
```

Other useful commands:

```bash
yarn lint
yarn build
yarn serve
```

Product procedures intentionally do not contain Web Console steps. Use `kubectl`
and `valkey-cli` for all instance operations.
