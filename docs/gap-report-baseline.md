# Baseline gap report (comprs vs oxipng/mozjpeg)

Environment:
- Host toolchain: rustc 1.82.0 (release) for comprs, cargo +nightly for oxipng build (edition2024).
- External binaries:
  - oxipng: `/workspace/vendor/oxipng/target/release/oxipng` (built with `cargo +nightly build --release`)
  - mozjpeg cjpeg: `/workspace/vendor/mozjpeg/build/cjpeg` (built via `cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DENABLE_SHARED=OFF` && `cmake --build build --target cjpeg`)
- Harness command:  
  `FIXTURES=tests/fixtures OXIPNG_BIN=... MOZJPEG_CJPEG=... cargo run --example codec_harness --release`

Skipped fixtures: `reference/oxipng_corrupted_header.png` (invalid PNG signature).

## Results

Times are wall-clock from the harness; sizes are bytes.

### PNG
| Image | comprs size | oxipng size | Δ size | comprs time | oxipng time | Δ speed |
| --- | --- | --- | --- | --- | --- | --- |
| multi-agent.jpg | 2,179,141 | 1,730,627 | -20.6% | 269 ms | 2,621 ms | 9.7× slower |
| playground.png | 1,475,576 | 1,134,213 | -23.1% | 397 ms | 2,978 ms | 7.5× slower |
| squoosh_example.png | 2,372,264 | 1,633,408 | -31.2% | 329 ms | 6,275 ms | 19.1× slower |
| squoosh_example_palette.png | 268,636 | 104,206 | -61.2% | 54 ms | 357 ms | 6.6× slower |
| rocket.png | 1,655,092 | 1,280,518 | -22.6% | 157 ms | 1,190 ms | 7.6× slower |

### JPEG (quality 85, 4:4:4 vs mozjpeg progressive optimized)
| Image | comprs size | mozjpeg size | Δ size | comprs time | mozjpeg time | Δ speed |
| --- | --- | --- | --- | --- | --- | --- |
| multi-agent.jpg | 446,401 | 305,766 | -31.5% | 131 ms | 353 ms | 2.7× slower |
| playground.png | 439,422 | 254,881 | -42.0% | 218 ms | 451 ms | 2.1× slower |
| squoosh_example.png | 333,824 | 229,552 | -31.2% | 139 ms | 285 ms | 2.0× slower |
| squoosh_example_palette.png | 175,653 | 100,913 | -42.5% | 27 ms | 104 ms | 3.9× slower |
| rocket.png | 167,844 | 121,721 | -27.5% | 46 ms | 122 ms | 2.6× slower |

## Takeaways
- PNG: oxipng wins size on every case (20–61% smaller) at a substantial runtime cost (6–19× slower). Largest gains on palette-style images. Indicates need for palette/color reductions, metadata stripping, stronger filter search, and higher-compression deflate options.
- JPEG: mozjpeg with optimized Huffman + progressive significantly reduces size (27–42%) at ~2–4× slower. Highlights missing features in comprs: optimized Huffman tables, progressive scans, trellis/quant tuning, better subsampling heuristics.
- The harness proves out the comparison workflow; corrupted/invalid fixtures are now skipped gracefully.

## Next steps
- Implement PNG reductions and filter search improvements (Phase 2).
- Add optimized Huffman/progressive and trellis options (Phase 3).
- Extend harness to dump CSV and add assertions/bench targets once new features land.
