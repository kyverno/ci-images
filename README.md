# ci-images

Container images used by Kyverno CI and test scenarios.

## Layout

Each top-level directory represents a separate image use-case. Images are
published to a nested GHCR repository based on that directory name:

- Example in this repository: `ghcr.io/kyverno/ci-images/cosign:<tag>`

This keeps unrelated image families separated while still letting this
repository host multiple CI image use-cases over time.

## Current use-cases

- [`cosign/`](./cosign/) - cosign verification and attestation test images

## Publishing

The GitHub Actions workflow in `.github/workflows/cosign.yml` builds the images
from `cosign/` and publishes the resulting tags to `ghcr.io/<owner>/<repo>/cosign`.
