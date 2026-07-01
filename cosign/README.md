# cosign

Cosign-focused test images that were previously hosted in a standalone
repository can now live under this use-case directory.

## Published image

- `ghcr.io/kyverno/ci-images/cosign:<tag>`

## Available tags

- `github-attestation`
- `github-sbom`
- `unsigned`
- `v2-traditional`
- `v2-keyless`
- `v3-traditional`
- `v3-keyless`
- `v3-bundle`

## Notes

- The Docker build context for all of these tags is `cosign/`.
- Key-based signing variants require `COSIGN_PRIVATE_KEY` and
  `COSIGN_PASSWORD` repository secrets.
- Keyless and GitHub attestation variants use the workflow identity from
  `.github/workflows/cosign.yml`.
