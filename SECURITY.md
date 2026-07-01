# Security Policy

`tei-builds` publishes Linux binaries of the upstream
[text-embeddings-inference](https://github.com/huggingface/text-embeddings-inference)
(TEI) project, built unmodified in GitHub Actions.

## Scope

- **In scope:** the build workflow and this repository's supply-chain
  configuration (action pinning, permissions, provenance, release integrity).
- **Out of scope:** vulnerabilities in upstream TEI code or in ONNX Runtime.
  Report those to their respective upstream projects.

## Reporting a vulnerability

Please report suspected security issues **privately** via
**GitHub → Security → Report a vulnerability** (private vulnerability reporting
is enabled on this repository). Do not open a public issue for security reports.

We aim to acknowledge reports within a few business days.

## Verifying a release

Every release archive ships a SHA-256 checksum and a signed build-provenance
attestation. To verify an artifact came from this repository's workflow:

```sh
sha256sum -c tei-<tag>-linux-<arch>.tar.gz.sha256
gh attestation verify tei-<tag>-linux-<arch>.tar.gz -R pommacore/tei-builds
```
