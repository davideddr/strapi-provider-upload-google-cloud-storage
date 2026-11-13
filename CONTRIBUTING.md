# Contributing

This is an unofficial fork of
[`strapi-community/strapi-provider-upload-google-cloud-storage`](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage).
It is maintained as a personal fork for use in private Strapi projects.

Contributions and bug reports about the fork are welcome on the
[fork issue tracker](https://github.com/davideddr/strapi-provider-upload-google-cloud-storage/issues).
If the issue also affects the original package, please report it upstream as well, so that it can be
fixed for everyone.

Please note that this project is released with a [Code of Conduct](CODE_OF_CONDUCT.md); by
participating you agree to abide by it.

## Development

Requirements: Node.js 20, 22 or 24 and Yarn 1.22.

```shell
yarn install --frozen-lockfile
yarn lint
yarn format:check
yarn test:unit
yarn build:prod
```

`yarn test:unit:watch` runs Jest in watch mode. `yarn format` applies Prettier.

## Pull requests

1. Create a branch from `master`.
2. Add tests for behaviour changes and keep the provider logic aligned with upstream where possible.
3. Make sure lint, tests and build pass (`yarn lint && yarn test:unit:ci && yarn build:prod`).
4. Update the documentation and [CHANGELOG.md](CHANGELOG.md) when the change is user-visible.
5. Open the pull request describing the change and the Strapi versions you tested.

Changes that only concern packaging, documentation or this fork's automation should not be sent
upstream. Functional fixes that apply to the original project are better contributed upstream first.

## Release process

See [FORK.md](FORK.md).
