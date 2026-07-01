# cosign

Cosign-focused test images that were previously hosted in a standalone
repository can now live under this use-case directory.

## Published image

- `ghcr.io/kyverno/test-images/cosign:<tag>`

## Available tags

- `github-attestation`
- `github-sbom`
- `unsigned`
- `v2-traditional`
- `v2-keyless`
- `v3-traditional`
- `v3-keyless`
- `v3-bundle`

## Image details

| Tag | Cosign version | Signing method | Artifacts created |
|-----|---------------|----------------|-------------------|
| `github-attestation` | N/A | GitHub Attestation | SLSA v1.0 build provenance |
| `github-sbom` | N/A | GitHub Attestation | SPDX SBOM attestation |
| `unsigned` | N/A | None | No signatures |
| `v2-traditional` | v2.4.1 | Key-based (by tag) | Traditional `.sig` OCI image |
| `v2-keyless` | v2.4.1 | Keyless OIDC (by tag) | Traditional `.sig` OCI image + Fulcio cert + Rekor entry |
| `v3-traditional` | v3.0.4 | Key-based (by tag) | Traditional `.sig` OCI image (backward compatible with v2) |
| `v3-keyless` | v3.0.4 | Keyless OIDC (by tag) | Traditional `.sig` OCI image + Fulcio cert + Rekor entry |
| `v3-bundle` | v3.0.4 | Key-based (by digest) | Traditional `.sig` OCI image |

## Verifying images

### GitHub Attestations

```bash
# Build provenance (SLSA v1.0)
cosign verify-attestation \
  --type https://slsa.dev/provenance/v1 \
  --certificate-identity=https://github.com/kyverno/ci-images/.github/workflows/cosign.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/kyverno/test-images/cosign:github-attestation

# SBOM (SPDX)
cosign verify-attestation \
  --type https://spdx.dev/Document \
  --certificate-identity=https://github.com/kyverno/ci-images/.github/workflows/cosign.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/kyverno/test-images/cosign:github-sbom
```

### Key-based signatures

The public key is available at [`examples/cosign.pub`](examples/cosign.pub).

```bash
# v2-traditional
cosign verify --key examples/cosign.pub ghcr.io/kyverno/test-images/cosign:v2-traditional

# v3-traditional
cosign verify --key examples/cosign.pub ghcr.io/kyverno/test-images/cosign:v3-traditional

# v3-bundle (signed by digest)
cosign verify --key examples/cosign.pub ghcr.io/kyverno/test-images/cosign:v3-bundle
```

### Keyless signatures

```bash
# v2-keyless
cosign verify \
  --certificate-identity=https://github.com/kyverno/test-images/.github/workflows/cosign.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/kyverno/test-images/cosign:v2-keyless

# v3-keyless
cosign verify \
  --certificate-identity=https://github.com/kyverno/test-images/.github/workflows/cosign.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/kyverno/test-images/cosign:v3-keyless
```

## Kyverno policy compatibility

Sample policies are in [`examples/sample-policies/`](examples/sample-policies/).

| Policy file | Kind | Signing method | `github-attestation` | `github-sbom` | `v2-traditional` | `v2-keyless` | `v3-traditional` | `v3-keyless` | `v3-bundle` |
|-------------|------|---------------|----------------------|---------------|------------------|--------------|------------------|--------------|-------------|
| `cpol.yaml` | ClusterPolicy | Keyless | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ |
| `cpol-key.yaml` | ClusterPolicy | Key-based | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ |
| `cpol-att.yaml` | ClusterPolicy | GitHub Attestation | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `cpol-sbom.yaml` | ClusterPolicy | GitHub SBOM | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `ivpol.yaml` | ImageValidatingPolicy | Keyless | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ |
| `ivpol-key.yaml` | ImageValidatingPolicy | Key-based | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ | ✅ |
| `ivpol-gh-att.yaml` | ImageValidatingPolicy | GitHub Attestation | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| `ivpol-sbom.yaml` | ImageValidatingPolicy | GitHub SBOM | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |

**ClusterPolicy** only supports cosign v2 signatures. **ImageValidatingPolicy** supports both v2 and v3, and is required for GitHub attestation verification.

## Notes

- The Docker build context for all of these tags is `cosign/`.
- Key-based signing variants require `COSIGN_PRIVATE_KEY` and
  `COSIGN_PASSWORD` repository secrets.
- Keyless and GitHub attestation variants use the workflow identity from
  `.github/workflows/cosign.yml`.
