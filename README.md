# tei-builds

Linux builds of
[text-embeddings-inference](https://github.com/huggingface/text-embeddings-inference)
(TEI, Apache-2.0), built **unmodified from upstream** in GitHub Actions for
**amd64** and **arm64**.

Each build comes from a **pinned upstream commit**, a **pinned Rust toolchain**,
and **locked dependencies**, using upstream's source with a portable feature set
(`--features ort,candle,static-linking,http --no-default-features`) on a
generic/portable CPU baseline (never `target-cpu=native`). Intel MKL is omitted
for cross-arch parity; ONNX Runtime (`-F ort`) is the inference backend.

> **Not (yet) bit-for-bit reproducible.** A true reproducibility claim requires
> an independent double-build and a `diffoscope` comparison, which we don't do.
> What we do guarantee: a pinned, auditable build with verifiable checksums and
> signed build provenance.

## CPU optimization (why no MKL / Intel-specific build)

The `-F ort` inference path runs on **ONNX Runtime**, whose kernels select
AVX2/AVX-512 **at runtime** — so these portable builds already run CPU-optimized
on Intel and AMD, with no per-CPU build needed. Intel MKL only accelerates the
secondary `candle` matmul path; it is deliberately omitted for cross-arch parity,
to avoid a large proprietary static dependency, and because its default static
build broke linking (missing `hgemm_`). A dedicated MKL- or `target-cpu`-tuned
Intel variant was evaluated and intentionally **not** added: it does not benefit
the ORT embedding path.

## Release contents

Each `tei-<tag>-linux-<arch>.tar.gz` bundles:

- `text-embeddings-router` — the binary
- `LICENSE` — upstream Apache-2.0 license (Apache-2.0 §4(a))
- `THIRD-PARTY-NOTICES.txt` — ONNX Runtime (MIT), statically linked via `-F ort`
- `BUILDINFO.txt` — upstream tag + commit SHA, toolchain, features, build run

A matching `.sha256` checksum is published alongside each archive.

## Verify

```sh
# integrity
sha256sum -c tei-<tag>-linux-<arch>.tar.gz.sha256

# provenance — who/what/where built it (GitHub Artifact Attestations, SLSA)
gh attestation verify tei-<tag>-linux-<arch>.tar.gz -R pommacore/tei-builds
```

## Build

Actions → **build-tei** → **Run workflow** → enter the upstream tag (e.g. `v1.9.3`).

## License

The workflow and docs in this repository are Apache-2.0 (see `LICENSE`). Released
binaries remain under upstream TEI's Apache-2.0 license; the statically linked
ONNX Runtime is MIT. Attribution notices ship inside each release archive.
