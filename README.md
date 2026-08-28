# soci-index-builder

Lambda ZIPs for the AWS SOCI Index Builder, built from awslabs source with SOCI v2 support.

## Why

The [AWS SOCI Index Builder](https://github.com/awslabs/cfn-ecr-aws-soci-index-builder) generates
[SOCI](https://github.com/awslabs/soci-snapshotter) indices for images in Amazon ECR. AWS hosts
its Lambda ZIPs in the `aws-quickstart` S3 buckets, and those ZIPs generate SOCI v1 indices only.
SOCI v2 index generation requires a build from source.

This repository builds the two Lambdas from the awslabs source and publishes the ZIPs as GitHub
release assets:

- `ecr-image-action-event-filtering.zip` filters ECR image push events by repository and tag.
- `soci-index-generator.zip` generates the SOCI index and pushes it back to the repository.

Each release also contains a `SHA256SUMS` file for both ZIPs.

## Usage

Download the assets for a pinned tag and verify the checksums:

```sh
gh release download <tag> --repo evil8io/soci-index-builder
sha256sum --check SHA256SUMS
```

Deploy the ZIPs with your own infrastructure code, for example as `aws_lambda_function`
resources with `filename` and `source_code_hash`.

## Versioning

A tag has the form `v<upstream-version>-<build>`, for example `v0.4.1-1`. The first part is the
upstream version that the release was built from. The second part is the build revision of this
repository for that upstream version.

## Relationship to upstream

This repository repackages [awslabs/cfn-ecr-aws-soci-index-builder](https://github.com/awslabs/cfn-ecr-aws-soci-index-builder).
It is not affiliated with, or endorsed by, Amazon Web Services. The upstream source is licensed
under the Apache License 2.0.

## License

[Apache License 2.0](LICENSE)
