# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses
[Semantic Versioning](https://semver.org/).

## [5.0.5] - 2026-11-13

First stable release of the fork, based on upstream `v5.0.5`.

### Changed

- Published as `@davideddr/strapi-provider-upload-google-cloud-storage`; the Strapi `provider`
  configuration now uses the fork package name.
- Package metadata (author, maintainers, contributors, repository, bugs, homepage) points to the
  fork while retaining the upstream credits.
- README includes a fork notice, requirements and fork-specific support and release notes.
- Documentation and signed-URL error messages link to the fork repository.
- Node.js support declared as 20, 22 and 24.
- `.npmignore` removed in favour of the `files` whitelist; `CHANGELOG.md` and `FORK.md` are now
  published.
- Release automation publishes `fork-v*` tags with npm provenance and creates the GitHub Release
  from this changelog.

### Fixed

- TypeScript test configuration includes the Jest types (`types: ["node", "jest"]`), so
  `yarn test:unit` type-checks the test files again.
- Build and prepack scripts use `npm run` internally, so `npm pack` and `npm publish` work without a
  global Yarn binary.
- `yarn.lock` regenerated in the Yarn 1 format declared by `packageManager`, so
  `yarn install --frozen-lockfile` works; `@types/node` pinned to `24.0.1` to match the supported
  Node range.

### Unchanged

- Provider logic (`src/`), options and behaviour are the same as upstream `v5.0.5`.

[5.0.5]: https://github.com/davideddr/strapi-provider-upload-google-cloud-storage/releases/tag/fork-v5.0.5
