# `artifact-s3`

Interact programmatically with [Actions Artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts).

This is the core library that powers the [`upload-artifact-s3`](https://github.com/NinjaManatee/upload-artifact-s3) and [`download-artifact-s3`](https://github.com/NinjaManatee/download-artifact-s3) actions.


- [`artifact-s3`](#artifact-s3)
  - [Quick Start](#quick-start)
  - [Examples](#examples)
    - [Upload and Download](#upload-and-download)
    - [Delete an Artifact](#delete-an-artifact)
    - [Speeding up large uploads](#speeding-up-large-uploads)
  - [Additional Resources](#additional-resources)

## Quick Start

Install the package:

```bash
npm i artifact-s3
```

Import the module:

```js
// ES6 module
import {DefaultArtifactClient} from 'artifact-s3'

// CommonJS
const {DefaultArtifactClient} = require('artifact-s3')
```

Then instantiate:

```js
const artifact = new DefaultArtifactClient()
```

ℹ️ For a comprehensive list of classes, interfaces, functions and more, see the [generated documentation](./docs/generated/README.md).

## Examples

### Upload and Download

The most basic scenario is uploading one or more files to an Artifact, then downloading that Artifact. Downloads are based on the Artifact ID, which can be obtained in the response of `uploadArtifact`, `getArtifact`, `listArtifacts` or via the [REST API](https://docs.github.com/en/rest/actions/artifacts).

```js
const {id, size} = await artifact.uploadArtifact(
  // name of the artifact
  'my-artifact',
  // files to include (supports absolute and relative paths)
  ['/absolute/path/file1.txt', './relative/file2.txt'],
  {
    // optional: how long to retain the artifact
    // if unspecified, defaults to repository/org retention settings (the limit of this value)
    retentionDays: 10
  }
)

console.log(`Created artifact with id: ${id} (bytes: ${size}`)

const {downloadPath} = await artifact.downloadArtifact(id, {
  // optional: download destination path. otherwise defaults to $GITHUB_WORKSPACE
  path: '/tmp/dst/path',
})

console.log(`Downloaded artifact ${id} to: ${downloadPath}`)
```

### Delete an Artifact

To delete an artifact, all you need is the name.

```js
const {id} = await artifact.deleteArtifact(
  // name of the artifact
  'my-artifact'
)

console.log(`Deleted Artifact ID '${id}'`)
```

It also supports options to delete from other repos/runs given a github token with `actions:write` permissions on the target repository is supplied.

```js
const findBy = {
  // must have actions:write permission on target repository
  token: process.env['GITHUB_TOKEN'],
  workflowRunId: 123,
  repositoryOwner: 'actions',
  repositoryName: 'toolkit'
}


const {id} = await artifact.deleteArtifact(
  // name of the artifact
  'my-artifact',
  // options to find by other repo/owner
  { findBy }
)

console.log(`Deleted Artifact ID '${id}' from ${findBy.repositoryOwner}/ ${findBy.repositoryName}`)
```

### Speeding up large uploads

If you have large files that need to be uploaded (or file types that don't compress well), you may benefit from changing the compression level of the Artifact archive. NOTE: This is a tradeoff between artifact upload time and stored data size.

```ts
await artifact.uploadArtifact('my-massive-artifact', ['big_file.bin'], {
  // The level of compression for Zlib to be applied to the artifact archive.
  // - 0: No compression
  // - 1: Best speed
  // - 6: Default compression (same as GNU Gzip)
  // - 9: Best compression
  compressionLevel: 0
})
```

## Additional Resources

- [Releases](./RELEASES.md)
- [Contribution Guide](./CONTRIBUTIONS.md)
- [Frequently Asked Questions](./docs/faq.md)
