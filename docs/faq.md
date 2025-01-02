# Frequently Asked Questions

- [Frequently Asked Questions](#frequently-asked-questions)
  - [Supported Characters](#supported-characters)
  - [Compression? ZIP? How is my artifact stored?](#compression-zip-how-is-my-artifact-stored)
  - [Which versions of the artifacts packages are compatible?](#which-versions-of-the-artifacts-packages-are-compatible)
  - [How long will my artifact be available?](#how-long-will-my-artifact-be-available)

## Supported Characters

When uploading an artifact, the inputted `name` parameter along with the files specified in `files` cannot contain any of the following characters. If they are present in `name` or `files`,  the Artifact will be rejected by the server and the upload will fail. These characters are not allowed due to limitations and restrictions with certain file systems such as NTFS. To maintain platform-agnostic behavior, characters that are not supported by an individual filesystem/platform will not be supported on all filesystems/platforms.

- "
- :
- <
- \>
- |
- \*
- ?

In addition to the aforementioned characters, the inputted `name` also cannot include the following
- \
- /

## Compression? ZIP? How is my artifact stored?

When creating an Artifact, the files are dynamically compressed and streamed into a ZIP archive. Since they are stored in a ZIP, they can be compressed by Zlib in varying levels.

The value can range from 0 to 9:

- 0: No compression
- 1: Best speed
- 6: Default compression (same as GNU Gzip)
- 9: Best compression

Higher levels will result in better compression, but will take longer to complete.
For large files that are not easily compressed, a value of 0 is recommended for significantly faster uploads.

## Which versions of the artifacts packages are compatible?
[upload-artifact-s3](https://github.com/NinjaManatee/upload-artifact-s3) and [download-artifact-s3](https://github.com/NinjaManatee/download-artifact-s3), leverage [GitHub Actions toolkit](https://github.com/actions/toolkit) and [artifact-s3](https://github.com/NinjaManatee/artifact-s3) and are typically used together to upload and download artifacts in your workflows.

<!-- Commenting this out for future use
| upload-artifact-s3 | download-artifact-s3 | artifact-s3 |
|---|---|---|
| v4 | v4 | v2 |
| < v3 | < v3 | < v1 |

Use matching versions of `upload-artifact-s3` and `download-artifact-s3` to ensure compatibility. 
-->

In your GitHub Actions workflow YAML file, you specify the version of the actions you want to use. For example:

```yaml
  uses: NinjaManatee/upload-artifact@main
  # ...
  uses: NinjaManatee/download-artifact-s3@main
  # ...
```

**Release Notes:**
Check the release notes for each repository to see if there are any specific notes about compatibility or changes in behavior.

## How long will my artifact be available?
This is set by the AWS S3 bucket you are using for storage. See [Managing the lifecycle of objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) for more information.
