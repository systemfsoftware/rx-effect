# rx-effect

Bridge RxJS and Effect-TS — turn an Observable into a typed Effect Stream with backpressure and proper interruption.

Extracted from `systemfsoftware/systemfsoftware` (core); package history came with the move.

## Packages

| Package                      | Version | Distributed |
| ---------------------------- | ------- | ----------- |
| `@systemfsoftware/rx-effect` | 1.0.2   | yes         |

## Dependencies on core

https://nix.systemfsoftware.com is the package registry for `@systemfsoftware` packages: a producer pushes its package tarballs there from `main`, a consumer substitutes them through its flake inputs, and nothing is published to npm.

`flake.nix` pins core at a full commit (input `core`), and `pnpm-release-management.lib.mkProducerTarballs` lays core's pushed tarballs into `.deps/`. `pnpm-workspace.yaml` maps every core package to `file:.deps/<name>.tgz` through `overrides`; the moved manifests name ordinary semver ranges. To take newer core packages: bump the `core` input, run `nix flake update core`, then `pnpm update '@systemfsoftware/*'`.

Local setup:

```bash
nix develop   # the shell hook copies core's tarballs into .deps/
pnpm install
```

## Gate

- `pnpm check:local` before delivery: dprint, build, lint, typecheck, type tests, API reports, attw, tests at the
  local conformance profile.
- CI (`.github/workflows/ci.yml`, SYS-1369) calls core's reusable checks at a pinned core commit. A pull request runs
  the static lane and the affected tests (`.github/ci/pr-tests.mjs`: `turbo --affected`, `vitest --changed`) and no
  packed test job. A Mergify queue draft (`mergify/merge-queue/*`) runs the full suite: the static lane and every
  package's whole suite. Main runs the full suite again after each merge, and a package whose content hash already
  passed whole is a cache hit, not left out by name. `nix.yml` builds the flake outputs on main only.
- Never start a local mutation run; mutation runs in CI only.
- Version bumps: every change to a publishable package's packed output advances its version at least a patch.

## Conventions

- Run `pnpm --filter <pkg> <cmd>` from the root; never `cd` into packages, never `npx`.
- Commits: `type(scope): subject` (commitlint).
- Tooling scripts are TypeScript run by the toolchain already in the workspace; no synchronous IO in new scripts.
