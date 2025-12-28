# Changelog

All notable changes to this project will be documented in this file.

## [0.3.0] - 2025-12-27

### Added

- **Image Resizing API** — New `resize` module for high-quality image resizing
  - Three algorithms: `Nearest` (fastest), `Bilinear` (balanced), `Lanczos3` (highest quality, default)
  - Support for all color types: Gray, GrayAlpha, RGB, RGBA
  - `resize()` for simple usage, `resize_into()` for buffer reuse
  - Separable filtering with precomputed contributions for O(2n) per-pixel performance
  - Parallel processing support via the `parallel` feature flag

- **WASM Resize Support** — `resizeImage()` function in WASM bindings
  - Aspect ratio preservation option
  - Algorithm selection (nearest, bilinear, lanczos3)

### Example

```rust
use pixo::{resize, ColorType, ResizeAlgorithm};

// Resize a 100x100 RGBA image to 50x50 using Lanczos3
let pixels = vec![128u8; 100 * 100 * 4];
let resized = resize::resize(
    &pixels, 100, 100, 50, 50,
    ColorType::Rgba,
    ResizeAlgorithm::Lanczos3,
)?;
```

## [0.2.1] - 2025-12-27

### Changed

- Documentation link fixes for GitHub and docs.rs compatibility

## [0.2.0] - 2025-12-26

### Added

- PNG encoder with all filter types and DEFLATE compression
- JPEG encoder with baseline and progressive modes
- Lossy PNG via palette quantization
- Trellis quantization for JPEG
- SIMD acceleration (x86_64 AVX2/SSE, aarch64 NEON)
- WASM bindings with 149 KB binary size
- CLI tool for command-line compression
- PNG/JPEG decoding support
- Comprehensive documentation and guides

## [0.1.0] - 2025-12-21

### Added

- Initial release
- Core compression algorithms (Huffman, LZ77, DEFLATE)
- Basic PNG and JPEG encoding
