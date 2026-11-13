> **Fork notice** — This is an unofficial fork of
> [`@strapi-community/strapi-provider-upload-google-cloud-storage`](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage),
> published on npm as
> [`@davideddr/strapi-provider-upload-google-cloud-storage`](https://www.npmjs.com/package/@davideddr/strapi-provider-upload-google-cloud-storage)
> and maintained by [Davide Da Rech](https://github.com/davideddr).
>
> It is **not affiliated with, endorsed by or supported by** Strapi Community, Strapi Inc. or VirtusLab.
> The original project is the one linked above; please report bugs of this fork on the
> [fork issue tracker](https://github.com/davideddr/strapi-provider-upload-google-cloud-storage/issues).
>
> See [FORK.md](FORK.md) for the differences from upstream, the versioning rules and the sync process.

<div align="center">
  <h1>
    <span style="display: block">Strapi Provider Upload</span>
    <span style="display: block; font-size: 1.75rem">Google Cloud Storage</span>
  </h1>
  <p><strong>Community</strong> Google Cloud Storage Provider for Strapi Upload <em>(unofficial fork)</em></p>
  <a href="https://www.npmjs.org/package/@davideddr/strapi-provider-upload-google-cloud-storage">
    <img alt="NPM version" src="https://img.shields.io/npm/v/@davideddr/strapi-provider-upload-google-cloud-storage.svg">
  </a>
  <a href="https://www.npmjs.org/package/@davideddr/strapi-provider-upload-google-cloud-storage">
    <img src="https://img.shields.io/npm/dm/@davideddr/strapi-provider-upload-google-cloud-storage.svg" alt="Monthly download on NPM" />
  </a>
</div>

## 📋 Table of Contents

- [📦 Installation](#installation)
- [✅ Requirements](#requirements)
- [🪣 Create your Bucket on Google Cloud Storage](#create-bucket)
- [🔐 Setting up Google authentication](#setup-auth)
- [⚙️ Setting up the configuration file](#setting-up-the-configuration-file)
- [🔒 Setting up `strapi::security` middlewares](#setting-up-strapisecurity-middlewares-to-avoid-csp-blocked-url)
- [📝 Configuration Variables](#how-to-configure-variable)
- [❓ FAQ](#faq)
- [🍴 Fork notes](#fork-notes)
- [🔗 Upstream & Credits](#upstream--credits)
- [💬 Support](#support)
- [📄 License](#license)

## 📦 Installation

Install the package from your app root directory

with `npm`

```
npm install @davideddr/strapi-provider-upload-google-cloud-storage --save
```

or `yarn`

```
yarn add @davideddr/strapi-provider-upload-google-cloud-storage
```

## ✅ Requirements

|              | Version                                  |
| ------------ | ---------------------------------------- |
| Strapi       | 5.x                                      |
| Node.js      | 20, 22 or 24                             |
| Google Cloud | Cloud Storage bucket (fine-grained ACLs) |

> Using the original upstream package instead? Install
> `@strapi-community/strapi-provider-upload-google-cloud-storage` and follow its documentation.

## 🪣 <a name="create-bucket"></a> Create your Bucket on Google Cloud Storage

The bucket should be created with **fine grained** access control, as the plugin will configure uploaded files with public read access.

### How to create a bucket ?

- https://cloud.google.com/storage/docs/creating-buckets

### Where my bucket can be located ?

- https://cloud.google.com/storage/docs/locations

## 🔐 <a name="setup-auth"></a> Setting up Google authentication

If you are deploying to a Google Cloud Platform product that supports [Application Default Credentials](https://cloud.google.com/docs/authentication/production#finding_credentials_automatically) (such as App Engine, Cloud Run, and Cloud Functions etc.), then you can skip this step.

If you are deploying outside GCP, then follow these steps to set up authentication:

1. In the GCP Console, go to the **Create service account key** page.
   - **[Go to the create service account key page](https://console.cloud.google.com/apis/credentials/serviceaccountkey)**
2. From the **Service account** list, select **New service account**.
3. In the **Service account name** field, enter a name.
4. From the **Role** list, select **Cloud Storage > Storage Admin**.
5. Select `JSON` for **Key Type**
6. Click **Create**. A JSON file that contains your key downloads to your computer.
7. Copy the full content of the downloaded JSON file
8. Open the Strapi configuration file
9. Paste it into the "Service Account JSON" field (as `string` or `JSON`, be careful with indentation)

## ⚙️ Setting up the configuration file

You will find below many examples of configurations, for each example :

1. If you are deploying outside GCP, then follow the steps above [Setting up Google authentication](#setup-auth)
2. Set the `#bucketName#` field and replace `Bucket-name` by yours [previously create](#create-bucket)
3. Default `baseUrl` is working, but you can replace it by yours (if you use a custom baseUrl)
4. Save the configuration file
5. Enjoy!

**Example with application default credentials (minimal setup)**

This works only for deployment to GCP products such as App Engine, Cloud Run, and Cloud Functions etc.

Edit `./config/plugins.js`

```js
module.exports = {
  upload: {
    config: {
      provider: '@davideddr/strapi-provider-upload-google-cloud-storage',
      providerOptions: {
        bucketName: '#bucketName#',
        publicFiles: false,
        uniform: false,
        basePath: '',
      },
    },
  },
  //...
};
```

If you set `publicFiles` to `false`, the assets will be signed on the Content Manager (not the Content API). Consequently, they will only be visible to users who are authenticated.

You can set the expiry time of the signed URL by setting the `expires` option in the `providerOptions` object. See more in [expires](#expires).

**Example with credentials for outside GCP account**

Edit `./config/plugins.js`

```js
module.exports = {
  upload: {
    config: {
      provider: '@davideddr/strapi-provider-upload-google-cloud-storage',
      providerOptions: {
        bucketName: '#bucketName#',
        publicFiles: true,
        uniform: false,
        serviceAccount: {}, // replace `{}` with your serviceAccount JSON object
        baseUrl: 'https://storage.googleapis.com/{bucket-name}',
        basePath: '',
      },
    },
  },
  //...
};
```

If you have different upload provider by environment, you can override `plugins.js` file by environment :

- `config/env/development/plugins.js`
- `config/env/production/plugins.js`

This file, under `config/env/{env}/` will be overriding default configuration present in main folder `config`.

**Example with environment variable**

```js
module.exports = ({ env }) => ({
  upload: {
    config: {
      provider: '@davideddr/strapi-provider-upload-google-cloud-storage',
      providerOptions: {
        serviceAccount: env.json('GCS_SERVICE_ACCOUNT'),
        bucketName: env('GCS_BUCKET_NAME'),
        basePath: env('GCS_BASE_PATH'),
        baseUrl: env('GCS_BASE_URL'),
        publicFiles: env('GCS_PUBLIC_FILES'),
        uniform: env('GCS_UNIFORM'),
      },
    },
  },
  //...
});
```

Environment variable can be changed has your way.

## 🔒 Setting up `strapi::security` middlewares to avoid CSP blocked url

Edit `./config/middlewares.js`

- In the field `img-src` and `media-src` add your own CDN url, by default it's `storage.googleapis.com` but you need to add your own CDN url

```js
module.exports = [
  'strapi::errors',
  {
    name: 'strapi::security',
    config: {
      contentSecurityPolicy: {
        useDefaults: true,
        directives: {
          'connect-src': ["'self'", 'https:'],
          'img-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          'media-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          upgradeInsecureRequests: null,
        },
      },
    },
  },
  'strapi::cors',
  'strapi::poweredBy',
  'strapi::logger',
  'strapi::query',
  'strapi::body',
  'strapi::favicon',
  'strapi::public',
];
```

## 📝 How to configure variable ?

#### `serviceAccount` :

JSON data provide by Google Account (explained before).

**For GCP environments (App Engine, Cloud Run, Cloud Functions, GKE, Compute Engine):**
You can leave this omitted, and authentication will work automatically using Application Default Credentials (ADC). The provider will detect the GCP environment and attempt to use ADC for signing URLs.

**For non-GCP environments (local development, on-premises, other cloud providers):**
You must provide explicit service account credentials with both `client_email` and `private_key` fields for signed URL generation when `publicFiles` is set to `false`.

**Behavior:**

- **GCP environment + no serviceAccount**: Uses ADC for signed URLs ✅
- **GCP environment + explicit serviceAccount**: Uses provided credentials ✅
- **Non-GCP environment + no serviceAccount + publicFiles: true**: Returns direct URLs with warning ⚠️
- **Non-GCP environment + no serviceAccount + publicFiles: false**: Throws error ❌
- **Non-GCP environment + explicit serviceAccount**: Uses provided credentials ✅

Can be set as a String, JSON Object, or omitted.

**Example for GCP environments (minimal setup):**

```js
// No serviceAccount needed - uses ADC automatically
{
  bucketName: 'my-bucket',
  publicFiles: false, // Signed URLs will work with ADC
}
```

**Example for non-GCP environments:**

```js
{
  bucketName: 'my-bucket',
  publicFiles: false,
  serviceAccount: {
    project_id: 'your-project',
    client_email: 'your-service-account@your-project.iam.gserviceaccount.com',
    private_key: '-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n'
  }
}
```

#### `bucketName` :

The name of the bucket on Google Cloud Storage.

- Required

You can find more information on Google Cloud documentation.

#### `baseUrl` :

Define your base Url, first is default value :

- https://storage.googleapis.com/{bucket-name}
- https://{bucket-name}
- http://{bucket-name}

#### `basePath` :

Define base path to save each media document.

- Optional

#### `publicFiles`:

Boolean to define a public attribute to file when upload file to storage.

- Default value : `true`
- Optional

#### `uniform`:

Boolean to define `uniform` access, when uniform bucket-level access is enabled.

- Default value : `false`
- Optional

#### `skipCheckBucket`:

Boolean to define `skipCheckBucket`, when skipCheckBucket is enabled, we skip to check if the bucket exist. It's useful for private bucket.

- Default value : `false`
- Optional

#### `cacheMaxAge`:

Number to set the cache-control header for uploaded files in seconds. This value is used by the default metadata function to set the `Cache-Control` header as `public, max-age=${cacheMaxAge}`.

- Default value : `3600` (1 hour)
- Optional

Example:

```js
module.exports = {
  upload: {
    config: {
      provider: '@davideddr/strapi-provider-upload-google-cloud-storage',
      providerOptions: {
        bucketName: 'my-bucket',
        cacheMaxAge: 604800, // 7 days in seconds
      },
    },
  },
};
```

**Note**: If you provide a custom `metadata` function, the `cacheMaxAge` option will be ignored. You'll need to handle caching in your custom metadata function if needed.

#### `gzip`:

Value to define if files are uploaded and stored with gzip compression.

- Possible values: `true`, `false`, `auto`
- Default value : `auto`
- Optional

#### `expires`:

Value to define expiration time for signed URLS. Files are signed when `publicFiles` is set to `false`.

- Possible values: `Date`, `number`, `string`
- Default value : `900000` (15 minutes)
- Max value : `604800000` (7 days)
- Optional

### `metadata`:

Function that is executed to compute the metadata for a file when it is uploaded.

When no function is provided, the following metadata is used (using the configured `cacheMaxAge` value):

```ts
{
  contentDisposition: `inline; filename="${file.name}"`,
  cacheControl: `public, max-age=${cacheMaxAge}`, // Uses the cacheMaxAge from your configuration
}
```

- Default value: `undefined`
- Optional

Example:

```ts
  metadata: (file: File) => ({
    cacheControl: `public, max-age=${60 * 60 * 24 * 7}`, // One week
    contentLanguage: 'en-US',
    contentDisposition: `attachment; filename="${file.name}"`,
  }),
```

The available properties can be found in the [Cloud Storage JSON API documentation](https://cloud.google.com/storage/docs/json_api/v1/objects/insert#request_properties_JSON).

### `generateUploadFileName`:

Function that is executed to generate the name of the uploaded file. This method can give more control over the file name and can for example be used to include a custom hashing function or dynamic path.

When no function is provided, the [default algorithm](src/types.ts#L77-L82) is used.

- Default value: `undefined`
- Optional

Example:

```ts
  generateUploadFileName: async (basePath: string, file: File) => {
    const hash = await ...; // Some hashing function, for example MD-5
    const extension = file.ext.toLowerCase().substring(1);
    return `${extension}/${slugify(path.parse(file.name).name)}-${hash}.${extension}`;
  },
```

### `getContentType`:

Function that is executed to get the content type for a file when it is uploaded.

When no function is provided, the following content type is used:

```ts
file.mime;
```

**Important**: When a custom `getContentType` function is provided, the file's MIME type will be updated both in Google Cloud Storage metadata and in the Strapi database to ensure consistency.

- Default value: `undefined`
- Optional

Example:

```ts
  getContentType: (file: File) => {
    // Custom logic to determine content type
    if (file.ext === '.csv') {
      return 'text/csv';
    }
    return file.mime; // Fallback to original MIME type
  },
```

**Note**: This function affects both the `contentType` set in Google Cloud Storage and the `mime` field stored in the Strapi database.

## ❓ FAQ

### Common errors

#### Uniform access

`Error uploading file to Google Cloud Storage: Cannot insert legacy ACL for an object when uniform bucket-level access is enabled`

When this error occurs, you need to set `uniform` variable to `true`.

#### Service Account JSON

`Error: Error parsing data "Service Account JSON", please be sure to copy/paste the full JSON file`

When this error occurs, it's probably because you have missed something with the service account json configuration.

Follow this step :

- Open your `ServiceAccount` json file
- Copy the full content of the file
- Paste it under the variable `ServiceAccount` in `plugins.js` config file in JSON

#### Signed URL Generation Issues

**Error: `Cannot generate signed URLs without service account credentials`**

This error occurs when:

1. You're running in a **non-GCP environment** (local development, other cloud providers)
2. You have `publicFiles: false` (requiring signed URLs)
3. You haven't provided explicit `serviceAccount` credentials

**Solutions:**

- For **GCP environments**: Ensure your service account has proper permissions (`Storage Object Admin` or `Storage Admin` role)
- For **non-GCP environments**: Provide explicit `serviceAccount` configuration with `client_email` and `private_key`
- Alternatively: Set `publicFiles: true` to use direct URLs instead of signed URLs

**Error: `Failed to generate signed URL in GCP environment`**

This error occurs in GCP environments when Application Default Credentials (ADC) cannot sign URLs, typically due to:

1. Insufficient permissions on the default service account
2. Missing IAM roles for URL signing

**Solutions:**

- Ensure your GCP service account has `Storage Object Admin` or `Storage Admin` role
- Verify that the default service account has signing permissions
- Consider providing explicit `serviceAccount` credentials if ADC continues to fail

## 🍴 Fork notes

This fork tracks the upstream project and keeps the provider implementation compatible with Strapi 5.

- **Versioning**: stable releases use plain semver aligned with upstream (upstream `5.0.5` → fork `5.0.5`).
  Fork-only fixes increment the patch version; if that version would collide with a newer upstream release,
  the fork bumps to the next patch. See [CHANGELOG.md](CHANGELOG.md) for the version mapping.
- **Upstream sync**: add the original repository as a remote, fetch its tags and merge the upstream tag
  into `master`. Conflicts are expected only in `package.json`, `README.md` and `.github/`.
  ```shell
  git remote add upstream https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage.git
  git fetch upstream --tags
  git merge upstream/vX.Y.Z
  ```
- **Release tags**: the fork tags releases as `fork-vX.Y.Z`, because the upstream tags (`vX.Y.Z`)
  already exist in this repository. Tags matching `fork-v*` trigger the npm publish workflow.
- **Publishing**: GitHub Actions builds the package, checks the tarball, publishes with npm provenance
  and creates a GitHub Release from the changelog.

## 🔗 Upstream & Credits

Original project and documentation:

- [Upstream repository](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage)
- [Upstream package on npm](https://www.npmjs.com/package/@strapi-community/strapi-provider-upload-google-cloud-storage)
- [Strapi website](http://strapi.io/)
- [Strapi community on Slack](http://slack.strapi.io)

This fork exists thanks to the original authors: Vanessa Pasque (versions <=4.x), Strapi Inc. and
VirtusLab Ltd (versions >=5.x). See the [LICENSE](LICENSE) file.

## 💬 Support

- [Fork issue tracker](https://github.com/davideddr/strapi-provider-upload-google-cloud-storage/issues)
  for bugs and questions about this fork.
- For the original package, use the
  [upstream issue tracker](https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage/issues).

## 📄 License

MIT. See the [LICENSE](LICENSE) file: it retains the original copyright notices and adds the fork maintainer.
