# Contributing

## Requirements

- Nix with flakes
- Node.js 24
- pnpm (the version pinned in `package.json`'s `packageManager`)

The dev shell (`nix develop`, or direnv) puts every tool on PATH, `bin/dprint` included; on a bare
shell that has nix the wrapper scripts resolve what they need themselves.

## Dependencies on core

https://nix.systemfsoftware.com is the package registry for `@systemfsoftware` packages: a producer pushes its package tarballs there from `main`, a consumer substitutes them through its flake inputs, and nothing is published to npm.

`flake.nix` pins core at a full commit (input `core`), and `pnpm-release-management.lib.mkProducerTarballs` lays core's pushed tarballs into `.deps/` when the dev shell starts. `pnpm-workspace.yaml` maps every core package to `file:.deps/<name>.tgz` through `overrides`.

To take newer core packages:

1. Bump the `core` input, then run `nix flake update core`.
2. Run `nix eval --raw .#producer-tarballs.overrides` and update `pnpm-workspace.yaml`'s `overrides` if core added or dropped a package.
3. Enter `nix develop` so `.deps/` holds the new tarballs.
4. Run `pnpm update '@systemfsoftware/*'` to record the new versions and integrities.

Other external dependencies come from the configured npm registry.

## Setup and workflow

```bash
nix develop   # the shell hook copies core tarballs into .deps/; then run the rest inside it
pnpm install
pnpm format
pnpm check:local
```

`pnpm check:local` runs what the required CI checks run: dprint, build, lint, typecheck, type tests, API reports,
attw and the tests. Run it before pushing.

## Pull requests

Open pull requests against `main` in systemfsoftware/rx-effect. Commit messages follow Conventional Commits
(`commitlint.config.ts`).
