# Comparisons and Tables

## Filter vs Noise Type

| | **Gaussian Noise** | **Salt & Pepper** |
|---|---|---|
| **Mean** | Good | Poor (spreads noise) |
| **Gaussian** | Better (weighted) | Poor (spreads noise) |
| **Median** | Poor | **Excellent** (removes outliers) |

## Filter Properties

| Filter | Type | Construction | Edge Preservation |
|---|---|---|---|
| **Mean** | Linear | All weights = 1/N | Poor -- blurs edges |
| **Gaussian** | Linear | Center-weighted, Gaussian function | Better than mean |
| **Median** | **Non-linear** | Sort neighborhood, pick middle | **Excellent** -- picks existing value |

**Why median preserves edges:** non-linear, selects an existing pixel value. At an edge, majority of window is on one side, so median picks that side's value. Linear filters blur by averaging across the boundary.

## Edge Detector Comparison

| Method | Order | Noise Sensitivity | Edge Localization | Direction Info |
|---|---|---|---|---|
| **Sobel** | 1st | Medium | Good | Yes |
| **Prewitt** | 1st | Medium | Good | Yes |
| **Laplacian** | 2nd | **High** | Good | **No** |
| **LoG** | 2nd | Low | Good | **No** |
| **Canny** | 1st | **Low** | **Excellent** | Yes |

**1st order:** gradient-based, edges where **magnitude is large**, provides direction.
**2nd order:** Laplacian-based, edges at **zero-crossings**. **Isotropic** (rotation invariant). No direction.

## Eigenvalue Interpretation (Harris)

| lambda1 | lambda2 | Region | Harris R |
|---|---|---|---|
| Small | Small | **Flat** | \|R\| small |
| **Large** | Small | **Edge** | R < 0 |
| **Large** | **Large** | **Corner** | **R > threshold** |

**M describes an ellipse:** eigenvalues = axis lengths, eigenvectors = axis directions.

## Gaussian vs Laplacian Pyramid

| Property | **Gaussian** | **Laplacian** |
|---|---|---|
| Content | Smoothed images | Difference/detail images |
| Filtering | Low-pass | Band-pass |
| Reconstruct original? | **No** (info lost in smoothing) | **Yes** (lossless) |
| Use case | Feature detection, SIFT | Compression, blending |

## Noise Types

| Noise | Characteristics | Best Filter |
|---|---|---|
| **Gaussian** | Additive, bell curve | Gaussian or mean filter |
| **Salt & Pepper** | Random black/white pixels | **Median filter** |
| **Uniform** | Equal probability across range | Mean filter |
| **Speckle** | Multiplicative | Specialized filters |

## SIFT vs Harris

| Property | **Harris** | **SIFT** |
|---|---|---|
| Scale invariant | **No** | **Yes** |
| Rotation invariant | Yes | Yes |
| Provides descriptor | No (detection only) | **Yes (128D)** |
| Multi-scale | No | **Yes** (DoG pyramid) |

## RANSAC vs Least Squares

| Property | **Least Squares** | **RANSAC** |
|---|---|---|
| Outlier handling | **Poor** (outliers dominate) | **Excellent** (ignores outliers) |
| Deterministic | Yes | No (random sampling) |
| >50% outliers | Fails | **Works** |
| Speed | Fast (one pass) | Slower (many iterations) |

## Hough: rho-theta vs Slope-Intercept

| Property | **y = mx + b** | **rho = x*cos(theta) + y*sin(theta)** |
|---|---|---|
| Vertical lines | **Cannot represent** (m = inf) | **Yes** |
| Parameter space | Unbounded | **Bounded:** rho in [-D,D], theta in [0,180) |
| Used in Hough? | No | **Yes** |
