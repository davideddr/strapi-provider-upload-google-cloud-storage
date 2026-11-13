# Fork notes

This repository is an unofficial fork of
[`strapi-community/strapi-provider-upload-google-cloud-storage`](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage),
published on npm as
[`@davideddr/strapi-provider-upload-google-cloud-storage`](https://www.npmjs.com/package/@davideddr/strapi-provider-upload-google-cloud-storage).

It is not affiliated with, endorsed by or supported by Strapi Community, Strapi Inc. or VirtusLab.
The original copyright notices are retained in [LICENSE](LICENSE).

## Status: temporary workaround

This fork is not intended to be a permanent alternative to the upstream provider. It will be
deprecated once upstream publishes a release that includes Node.js 24/26 support, and users should
then switch back to
[`@strapi-community/strapi-provider-upload-google-cloud-storage`](https://www.npmjs.com/package/@strapi-community/strapi-provider-upload-google-cloud-storage).

## Why this fork exists

The fork is a temporary workaround for the Node.js engine range declared by the published upstream
package.

Upstream `@strapi-community/strapi-provider-upload-google-cloud-storage@5.0.5` declares
`engines.node: ">=18.0.0 <=22.x.x"`. Projects on Node.js 24 or 26 with strict engine checks
(`npm install --engine-strict`, or Yarn 1, which enforces `engines` by default) fail with
`EBADENGINE`, even though Strapi 5 supports those Node versions. Publishing the package under a
separate scope let those projects install it while the change went through upstream review.

Upstream has since merged
[PR #220](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage/pull/220),
which sets `engines.node` to `>=22.20.0 <=26.x.x`. My earlier
[PR #219](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage/pull/219)
was closed as superseded. The change is on upstream `master` but not yet in a published npm release;
until it is, this fork remains available for Node.js 24 projects.

The provider implementation is kept identical to upstream. Any functional change should be proposed
upstream first; a fork should only be maintained for a change that cannot be accepted there.

## Differences from upstream

- `engines.node` is `>=20.0.0 <=24.x.x` instead of `>=18.0.0 <=22.x.x` (published upstream 5.0.5).
  Upstream `master` has already moved to `>=22.20.0 <=26.x.x`.
- npm package name and scope: `@davideddr/strapi-provider-upload-google-cloud-storage`.
- `provider` value in the Strapi configuration uses the fork name.
- README includes the fork notice, requirements and support information.
- Documentation and error messages point to this repository instead of the upstream one.
- Release automation publishes from tags named `fork-vX.Y.Z` with npm provenance.
- `.github` workflows, issue templates and governance files are adapted to this repository.
- Build scripts use `npm run` internally (they work with both npm and Yarn) and `prepack` builds the
  package before packing or publishing.
- `yarn.lock` is stored in Yarn 1 format to match the `packageManager` field; the upstream lockfile
  was in the Yarn Berry format and could not be used with `--frozen-lockfile`.
- `@types/node` is pinned to a version compatible with the supported Node range (20–24).

The provider logic in `src/` is unchanged. Any functional change made only in the fork must be listed
in [CHANGELOG.md](CHANGELOG.md).

## Versioning

- Stable releases use plain semver aligned with upstream: upstream `5.0.5` → fork `5.0.5`.
- A fix that only exists in the fork increments the patch version.
- If a newer upstream release would collide with a fork version, the fork bumps to the next patch
  (e.g. upstream releases `5.0.6` while the fork is already at `5.0.6` → fork `5.0.7`).
- Pre-releases, if needed, use `X.Y.Z-fork.N` and must be published under the `next` dist-tag, never
  under `latest`.
- Every release is documented in [CHANGELOG.md](CHANGELOG.md), including the upstream version it is
  based on.

## Syncing with upstream

```shell
git remote add upstream https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage.git
git fetch upstream --tags
git merge upstream/vX.Y.Z
```

Conflicts are expected only in `package.json`, `README.md` and `.github/`. `src/` should merge
cleanly; if it does not, review the upstream change before accepting it.

After a sync:

1. Keep the fork-specific `name`, `version`, `repository`, `author` and `publishConfig`.
2. Keep the fork notice in the README and re-apply the package name in the installation and
   configuration examples.
3. Re-apply the fork URLs in `src/index.ts` and its test.
4. Update the changelog with the new upstream version.

## Releasing

1. Update `version` in `package.json` and add the entry to `CHANGELOG.md`.
2. Commit and push to `master`.
3. Create and push the tag: `git tag fork-vX.Y.Z && git push origin fork-vX.Y.Z`.
4. The `Publish to npm` workflow checks that the tag matches `package.json`, runs lint, tests and
   build, verifies the tarball, publishes with provenance and creates the GitHub Release.

The npm authentication uses the `NPM_TOKEN` repository secret. `publishConfig.provenance` and
`--provenance` require a public repository and a `repository` field matching the GitHub repository.
