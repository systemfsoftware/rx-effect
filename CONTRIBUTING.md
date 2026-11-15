# Contributing

## Requirements

- Nix with flakes
- Node.js 24
- pnpm (the version pinned in `package.json`'s `packageManager`)

The dev shell (`nix develop`, or direnv) puts every tool on PATH, `bin/dprint` included; on a bare
shell that has nix the wrapper scripts resolve what they need themselves.

## Dependencies on core

`@systemfsoftware` packages come from npm like any other dependency: package manifests and catalogs name caret ranges and `pnpm-lock.yaml` holds the exact versions. Run `pnpm update '@systemfsoftware/*'` to take newer ones.

`flake.nix` pins core at a full commit (input `core`) only for its dprint build and its nixpkgs.

## Setup and workflow

```bash
nix develop   # then run the rest inside it
pnpm install
pnpm format
pnpm check:local
```

`pnpm check:local` runs what the required CI checks run: dprint, build, lint, typecheck, type tests, API reports,
attw and the tests. Run it before pushing.

## Pull requests

Open pull requests against `main` in systemfsoftware/rx-effect. Commit messages follow Conventional Commits
(`commitlint.config.ts`).
