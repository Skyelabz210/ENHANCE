# ENHANCE! v4.0: Pure Integer Image Enhancement System - User and Developer Guide

## 1. Introduction

ENHANCE! v4.0 is a revolutionary image enhancement system built upon the Quantum Modular Number Field (QMNF) architecture. This system is specifically optimized for image processing, leveraging pure integer arithmetic to achieve high performance, determinism, and auditable operations without any floating-point contamination. It represents the fourth major implementation of QMNF, following the main system, MELD, and Apollonian Arithmetic.

### 1.1 Core Innovations

ENHANCE! v4.0 introduces several groundbreaking innovations:

*   **1024-dimensional integer hypermembranes**: Enables dense feature encoding without relying on floating-point numbers.
*   **Pure modular arithmetic**: All operations are performed within ℤₘ (multiple modular spaces), ensuring mathematical rigor and preventing floating-point inaccuracies.
*   **Integer k-NN search**: Facilitates hyperdimensional similarity computations without floating-point operations.
*   **MAP inference with φ-recursion**: Implements Bayesian reconstruction using the golden ratio for iterative refinement.
*   **Geometric corrections**: Utilizes integer plane fitting to address reflections and distortions in images.
*   **Multi-layer caching**: Achieves over 80% hit rates for sub-millisecond operations, significantly boosting performance.

### 1.2 Performance Targets

The system is designed for efficiency, with verified performance targets on a 2011 dual-core netbook (8GB RAM):

*   **Membrane encoding**: Less than 10µs per 64×64 block.
*   **k-NN search**: Less than 100µs for 50 neighbors in a 1000-element database.
*   **MAP inference**: Less than 2ms per iteration.
*   **Full enhancement**: Less than 70ms for a 1080p patch (depth=7).
*   **Memory footprint**: Less than 200MB.

### 1.3 Safety & Correctness

ENHANCE! v4.0 prioritizes safety and correctness through:

*   **No undefined behavior**: All modular operations include overflow protection.
*   **Deterministic**: Identical inputs consistently produce identical outputs.
*   **Auditable**: Every operation is traceable within ℤₘ.
*   **No floating-point**: Compile-time verified via the type system to ensure no floating-point contamination.

## 2. Architectural Overview

The ENHANCE! v4.0 pipeline transforms a low-resolution image into an enhanced version through a series of modular, integer-based operations. The architecture can be visualized as follows:

```text
┌─────────────────────────────────────────────────────────────┐
│  Low-Res Image (uint8 pixels)                               │
└────────────────┬────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  1024D Integer Membrane Encoding                            │
│  • Color (R,G,B means)                                      │
│  • Entropy (bit-length based)                               │
│  • Edges (Sobel-like, 4 directions)                         │
│  • Frequency (integer DCT)                                  │
│  • Texture/Shape (hash-based features)                      │
└────────────────┬────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  Integer k-NN Search (squared distance, no sqrt)            │
│  • Find 50 nearest priors in hyperdimensional space         │
│  • Cached neighborhoods for repeated queries                │
└────────────────┬────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  Modular MAP Inference Loop                                 │
│  for depth in 0..7:                                         │
│    1. Compute gradient (data fidelity + prior pull)         │
│    2. Apply φ-recursive scheduler: λ = 1/φ^depth            │
│    3. Update hypothesis: h ← (h + λ·∇) mod M                │
│    4. Lyapunov stability check                              │
│    5. Geometric correction (every 3 iterations)             │
└────────────────┬────────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  Enhanced Image (uint8 pixels)                              │
│  + Provenance Metadata (timing, stability, cache stats)     │
└─────────────────────────────────────────────────────────────┘
```

### 2.1 Mathematical Foundation: Modular Spaces

All operations within ENHANCE! v4.0 are conducted within specific modular spaces, each with a distinct modulus optimized for its computational domain:

| Modular Space       | Modulus (i64)          | Purpose                                        |
| :------------------ | :--------------------- | :--------------------------------------------- |
| `M_energy`          | `2^61 - 1` (Mersenne prime) | Pixel-level operations, gradient computations  |
| `M_information`     | `2^31 - 1` (Mersenne prime) | Membrane encoding, feature extraction          |
| `M_consciousness`   | `φ³ × 10^9` (golden ratio cubed) | Scheduling, similarity metrics, φ-recursive operations |
| `M_spacetime`       | `2^19 - 1` (Mersenne prime) | Geometric transformations, plane fitting       |

## 3. User Guide

This section provides instructions on how to use the ENHANCE! v4.0 system to enhance images.

### 3.1 Basic Usage

To use the `enhance_complete` function, you need a low-resolution image, a database of pre-encoded membranes, and a set of corresponding prior images. You can also configure the enhancement process using `EnhanceConfig`.

#### Example:

```rust
use enhance_v4_complete::{ImageData, EnhanceConfig, enhance_complete, encode_image_to_membrane};

// 1. Prepare your low-resolution image
let low_res = ImageData::from_gray(64, 64, 128); // Example: 64x64 grayscale image with pixel value 128

// 2. Prepare your training data (database of membranes and corresponding prior images)
//    In a real application, 'training_images' would be a collection of high-resolution images
//    from which 'database' (encoded membranes) is built.
let training_images: Vec<ImageData> = (0..10).map(|i| {
    ImageData::from_gray(64, 64, (100 + i * 5) as u8)
}).collect();

let spaces = ModularSpaces::default(); // Access modular spaces for encoding
let database: Vec<[i64; 1024]> = training_images.iter()
    .map(|img| encode_image_to_membrane(img, spaces.m_information))
    .collect();

// 3. Configure the enhancement process (optional, default configuration is often sufficient)
let config = EnhanceConfig::default();

// 4. Run the enhancement pipeline
let result = enhance_complete(&low_res, &database, &training_images, config);

// 5. Access the enhanced image and metadata
println!("Enhanced Image Metadata:\n{}", result.metadata);
// You can now use result.enhanced.pixels to access the enhanced image data
```

### 3.2 Configuration Options (`EnhanceConfig`)

The `EnhanceConfig` struct allows users to fine-tune the enhancement process:

| Field                 | Type      | Default Value | Description                                                              |
| :-------------------- | :-------- | :------------ | :----------------------------------------------------------------------- |
| `k_neighbors`         | `usize`   | `50`          | Number of nearest neighbors to retrieve during k-NN search.              |
| `max_depth`           | `usize`   | `7`           | Maximum iterations for the MAP inference loop.                           |
| `prior_weight`        | `i64`     | `2`           | Weight applied to the prior pull during gradient computation. Higher values increase prior influence. |
| `stability_threshold` | `i64`     | `1000`        | Lyapunov exponent threshold for detecting divergence. If exceeded, a regenerative bridge is used. |
| `enable_geometric`    | `bool`    | `true`        | Enables or disables geometric corrections for reflections and distortions. |

### 3.3 Interpreting `EnhanceMetadata`

After enhancement, the `EnhanceResult` contains `EnhanceMetadata`, providing insights into the process and performance:

| Field             | Type      | Description                                                |
| :---------------- | :-------- | :--------------------------------------------------------- |
| `total_time_us`   | `u64`     | Total processing time in microseconds.                     |
| `encode_time_us`  | `u64`     | Time taken for hypermembrane encoding in microseconds.     |
| `knn_time_us`     | `u64`     | Time taken for k-NN search in microseconds.                |
| `map_time_us`     | `u64`     | Time taken for MAP inference in microseconds.              |
| `iterations`      | `usize`   | Number of MAP iterations performed.                        |
| `neighbors_used`  | `usize`   | Number of neighbors utilized in the process.               |
| `cache_hit_rate`  | `f64`     | Overall cache hit rate (0.0 to 1.0).                       |
| `final_lyapunov`  | `i64`     | The final Lyapunov exponent, indicating trajectory stability. |
| `bridge_used`     | `bool`    | `true` if the regenerative memory bridge was activated.    |

## 4. Developer Guide

This section delves into the internal workings of ENHANCE! v4.0, providing details for developers interested in extending, modifying, or understanding the system at a deeper level.

### 4.1 Modular Arithmetic Foundation

The core of ENHANCE! v4.0 relies on a set of modular arithmetic functions, ensuring all computations remain within integer domains and specified moduli. These functions are critical for maintaining numerical stability and determinism.

*   `add_mod(a: i64, b: i64, m: i64) -> i64`: Computes `(a + b) mod m` with overflow protection. Operands `a` and `b` must be in the range `[0, m)`. Performance is typically 2-3 cycles.
*   `sub_mod(a: i64, b: i64, m: i64) -> i64`: Computes `(a - b) mod m` with underflow protection. Operands `a` and `b` must be in the range `[0, m)`. Performance is typically 2-3 cycles.
*   `mul_mod(a: i64, b: i64, m: i64) -> i64`: Computes `(a × b) mod m` using 128-bit intermediate arithmetic to prevent overflow. Performance is typically 3-6 cycles on modern CPUs.
*   `inv_mod(a: i64, m: i64) -> Option<i64>`: Computes the modular multiplicative inverse `a^(-1) mod m` using the Extended Euclidean Algorithm. Returns `None` if `a` and `m` are not coprime. Typical performance is 50-100 iterations (~500ns).
*   `pow_mod(base: i64, exp: i64, m: i64) -> i64`: Computes `base^exp mod m` efficiently using binary exponentiation (repeated squaring). Performance is `O(log exp)` multiplications, typically ~5µs for large exponents.

### 4.2 Multi-Layer Caching Infrastructure

To meet stringent performance targets, ENHANCE! v4.0 employs a sophisticated multi-layer caching system managed by `CacheManager`.

*   **`InverseCache`**: Caches modular multiplicative inverses. Achieves a 10x speedup (from ~500ns to ~50ns) on cache hits. Uses a bulk eviction policy (removes oldest 25% of entries when capacity is exceeded).
*   **`PlaneCache`**: Stores integer plane coefficients for geometric corrections. Provides a 50x speedup (from ~50µs to ~1µs) on cache hits. Employs a simple FIFO eviction policy.
*   **`MembraneCache`**: Caches k-NN neighborhood results (membrane hash to list of nearest neighbor indices). Offers a 20x speedup (from ~100µs to ~5µs) on cache hits. Uses a simple FIFO eviction policy.

Developers can create a `CacheManager` with default capacities or specify custom capacities for each cache layer:

```rust
let mut caches = CacheManager::new(); // Default capacities
// OR
let mut custom_caches = CacheManager::with_capacities(20000, 2000, 1000); // Custom capacities
```

### 4.3 Image Data Structures (`ImageData`)

The `ImageData` struct represents an RGBA image with `u8` pixel data, stored in row-major order. It provides basic utilities for image manipulation.

*   `width: usize`: Image width in pixels.
*   `height: usize`: Image height in pixels.
*   `pixels: Vec<u8>`: Pixel data in RGBA format (4 bytes per pixel).

Key methods include:

*   `ImageData::new(width: usize, height: usize) -> Self`: Creates a new blank image.
*   `ImageData::from_gray(width: usize, height: usize, value: u8) -> Self`: Creates a grayscale image with a uniform pixel value.
*   `get_pixel(&self, x: usize, y: usize) -> Option<[u8; 4]>`: Retrieves the RGBA value of a pixel at `(x, y)`.
*   `set_pixel(&mut self, x: usize, y: usize, rgba: [u8; 4]) -> bool`: Sets the RGBA value of a pixel at `(x, y)`.

### 4.4 1024-Dimensional Hypermembrane Encoding

The `encode_image_to_membrane` function transforms an `ImageData` into a 1024-dimensional integer vector (hypermembrane). This encoding captures various image features using modular arithmetic.

**`pub const DIMS: usize = 1024;`**

Dimension allocation is as follows:

*   **0-2**: RGB color means.
*   **3**: Integer entropy approximation.
*   **4-7**: Edge density in 4 directions (horizontal, vertical, diagonal).
*   **8-11**: Low-frequency DCT coefficients (2×2 basis functions).
*   **12-15**: Gradient magnitudes (duplicates of edge features for weighting).
*   **16-31**: 16-bin color histogram.
*   **32-47**: Texture patterns (local variance in 4×4 blocks).
*   **48-1023**: Extended features derived from hash-based pixel mixing.

Developers can utilize `compute_integer_dct_features` for integer-based Discrete Cosine Transform and `hash_membrane` for fast membrane hashing.

### 4.5 Integer k-NN Search

The `find_k_nearest` function performs a k-nearest neighbor search in the hyperdimensional membrane space. It uses `similarity_int` to compute the inverse squared Euclidean distance between membranes, ensuring that higher values indicate greater similarity.

*   `similarity_int(query: &[i64; DIMS], membrane: &[i64; DIMS], modulus: i64) -> i64`: Computes the inverse squared Euclidean distance. Crucially, it avoids square roots as `d²` preserves ordering for ranking nearest neighbors.
*   `find_k_nearest(...)`: Leverages `MembraneCache` for performance. If a query's hash is cached, it returns the stored neighbors; otherwise, it computes similarities to all database entries and uses a partial sort to find the top-k, then caches the result.

### 4.6 Modular MAP Inference

The core enhancement logic resides in the `enhance_complete` function, which orchestrates the Modular Maximum A Posteriori (MAP) inference loop.

*   **Gradient Computation**: The gradient is computed based on data fidelity (difference between observation and hypothesis) and a prior pull from nearest neighbors. The `prior_weight` in `EnhanceConfig` controls the influence of the prior.
*   **φ-Recursive Scheduler (`phi_scheduler_int`)**: This function determines the learning rate `λ` for each iteration using `1/φ^depth`, where `φ` is the golden ratio. This provides a decaying learning rate, promoting stability as inference progresses.
*   **Hypothesis Update**: The hypothesis `h` is updated iteratively using `h ← (h + λ·∇) mod M`.
*   **Lyapunov Stability Check (`compute_lyapunov_int`)**: Measures the rate of divergence or convergence of the inference trajectory. If the computed Lyapunov exponent exceeds `stability_threshold`, it indicates divergence.
*   **Regenerative Memory Bridge (`construct_regenerative_bridge`)**: If divergence is detected, this mechanism blends the top-3 nearest priors to create a stable 

bridge, guiding the inference back to a plausible image space.
*   **Geometric Corrections (`integer_plane_fit`, `project_patch_to_surface`)**: Applied periodically (every 3 iterations) if `enable_geometric` is true. These functions fit an integer plane to sampled surface points and project the image patch onto this plane to correct for reflections and distortions.

### 4.7 Comprehensive Test Suite

The provided code includes a comprehensive test suite (`#[cfg(test)] mod tests`) to ensure the correctness and reliability of the ENHANCE! v4.0 system. Developers can run these tests to verify functionality after modifications or extensions.

Key tests cover:

*   **Modular Arithmetic**: Verifies `add_mod`, `sub_mod`, `mul_mod`, `inv_mod`, and `pow_mod` functions.
*   **Modular Spaces**: Checks default values and positivity of moduli.
*   **Membrane Encoding**: Asserts correct dimensionality, value ranges, and determinism of `encode_image_to_membrane`.
*   **Similarity Metric**: Confirms properties of `similarity_int`, including higher similarity for identical membranes and symmetry.
*   **Caching**: Tests `InverseCache` hit rate and `PlaneCache` caching behavior.
*   **k-NN Search**: Verifies `find_k_nearest` functionality.
*   **φ-Scheduler**: Checks the decaying behavior of `phi_scheduler_int`.
*   **Lyapunov Stability**: Demonstrates the computation of Lyapunov exponents for converging and diverging trajectories.
*   **No Floating-Point Contamination**: A compile-time check to ensure no `f64` types are used in core operations.
*   **Configuration Defaults**: Verifies `EnhanceConfig` default values.
*   **ImageData Operations**: Tests `ImageData` creation, pixel access, and modification.

### 4.8 Benchmark Harness

The `run_benchmark()` function provides a detailed performance analysis of the ENHANCE! v4.0 pipeline. It measures the speed of key components and estimates memory footprint, comparing them against predefined targets.

Benchmarks include:

*   **Hypermembrane Encoding Speed**: Measures average encoding time for a 64×64 block.
*   **k-NN Search Performance**: Evaluates search time for 50 neighbors in a 1000-element database.
*   **Full Enhancement Pipeline**: Measures the total time for `enhance_complete` on a 64×64 image with `depth=7`.
*   **Cache Performance**: Reports cache hit rates and sizes for `InverseCache`, `PlaneCache`, and `MembraneCache`.
*   **Memory Footprint Estimate**: Calculates memory usage for the database, prior images, and caches.

Developers can use this benchmark to assess the impact of changes and ensure the system continues to meet its performance targets.

## 5. Conclusion

ENHANCE! v4.0 stands as a testament to the power of pure integer arithmetic and modular design in complex image processing tasks. Its robust architecture, coupled with meticulous attention to performance and correctness, makes it a highly efficient and reliable system for image enhancement. Developers are encouraged to explore its modular components and extend its capabilities while adhering to the principles of integer-based computation.

## 6. References

[1] QMNF Enhancement Team. (2025). *ENHANCE! v4.0 - Pure Integer Image Enhancement System*. Proprietary Source Code Documentation.

