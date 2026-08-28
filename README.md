# soci-index-builder

Lambda ZIPs for the AWS SOCI Index Builder, built from awslabs source with SOCI v2 support.

## Why

The [AWS SOCI Index Builder](https://github.com/awslabs/cfn-ecr-aws-soci-index-builder) generates
[SOCI](https://github.com/awslabs/soci-snapshotter) indices for images in Amazon ECR. AWS hosts
its Lambda ZIPs in the `aws-quickstart` S3 buckets, and those ZIPs generate SOCI v1 indices only.
SOCI v2 index generation requires a build from current source.

This repository builds the two Lambdas from the awslabs source and publishes them as GitHub
release assets:

- `ecr-image-action-event-filtering.zip` filters ECR image push events by repository and tag.
- `soci-index-generator-amd64.zip` and `soci-index-generator-arm64.zip` generate the SOCI index
  and push it back to the repository. Pick the ZIP that matches the Lambda architecture.

Each release also contains a `SHA256SUMS` file over all ZIPs, and each ZIP has a build
provenance attestation.

## Usage

Download the assets for a pinned tag and verify them:

```sh
gh release download <tag> --repo evil8io/soci-index-builder
sha256sum --check SHA256SUMS
gh attestation verify soci-index-generator-arm64.zip --repo evil8io/soci-index-builder
```

Deploy the ZIPs with your own infrastructure code, for example as `aws_lambda_function`
resources with `filename` and `source_code_hash`. Deployment notes:

- The filter Lambda runs on a Python runtime, with handler
  `ecr_image_action_event_filtering_lambda_function.lambda_handler`.
- The generator runs on `provided.al2023`. The ZIP contains a static `bootstrap` binary.
- The env var `soci_index_version` on the generator selects `V1` or `V2`.

## Build and versioning

Upstream has no releases, so we track its `main` branch by commit. `build.yaml` runs daily. It
resolves the upstream HEAD, derives the tag, and builds a release when that tag does not exist
yet. A tag has the form `v<YYYYMMDD>-<shortsha>`: the date of the upstream commit plus the first
8 characters of its SHA. The tag is thus a pure function of the upstream commit.

`upstream-sha` contains the last released upstream commit. The workflow updates it on `main`
after each release, so the git log of that file is the build history. When nothing was built for
50 days, the workflow pushes an empty keepalive commit, because GitHub disables scheduled
workflows after 60 days without repository activity.

Manual build of a specific upstream commit:

```sh
gh workflow run build.yaml -f upstream_sha=<sha>
```

## Relationship to upstream

This repository repackages [awslabs/cfn-ecr-aws-soci-index-builder](https://github.com/awslabs/cfn-ecr-aws-soci-index-builder).
It is not affiliated with, or endorsed by, Amazon Web Services. The upstream source is licensed
under the Apache License 2.0.

## License

[Apache License 2.0](LICENSE)
