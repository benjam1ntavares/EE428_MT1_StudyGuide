# Variable Reference

## Image and Coordinates

| Symbol | Meaning | Context |
|---|---|---|
| **I(x, y)** | Image intensity at pixel (x, y) | Everywhere |
| **I(row, col)** | Image intensity in matrix notation | row = y, col = x |
| **(X, Y, Z)** | 3D world coordinates | Projection |
| **(x, y)** | 2D image coordinates | Projection |
| **f** | Focal length (pinhole to image plane) | Projection |
| **[x, y, w]** | Homogeneous coordinates; divide by w to get Cartesian | Projection |

## Filtering and Gradients

| Symbol | Meaning | Context |
|---|---|---|
| **Gx, Gy** | Gradient in x and y direction (from Sobel/Prewitt) | Edge detection, Harris |
| **\|G\|** | Gradient magnitude = sqrt(Gx^2 + Gy^2) | Edge detection |
| **theta** | Gradient direction = atan2(Gy, Gx) | Edge detection, NMS |
| **Ix, Iy** | Partial derivatives of image (same as Gx, Gy) | Harris |
| **sigma** | Gaussian standard deviation (controls blur width) | Filtering, Canny, SIFT |
| **G(x, y, sigma)** | 2D Gaussian kernel with width sigma | Filtering, SIFT |
| **w(x, y)** | Window/weighting function (usually Gaussian) | Harris |
| **\*** | Convolution (not multiplication) when between functions | Filtering, SIFT |

## Harris Corner Detection

| Symbol | Meaning | Context |
|---|---|---|
| **M** | Second moment matrix (2x2) encoding local gradient structure | Harris |
| **A, B, C** | A = SUM Ix^2, B = SUM Iy^2, C = SUM Ix*Iy | Harris shorthand for M |
| **lambda1, lambda2** | Eigenvalues of M (intensity variation along principal axes) | Harris |
| **R** | Corner response = det(M) - k*(trace(M))^2 | Harris |
| **k** | Empirical constant, typically **0.04 - 0.06** | Harris |
| **det(M)** | Determinant = AB - C^2 = lambda1 * lambda2 | Harris |
| **trace(M)** | Trace = A + B = lambda1 + lambda2 | Harris |
| **E(u, v)** | Error function measuring intensity change for shift (u, v) | Harris |

## Hough Transform

| Symbol | Meaning | Context |
|---|---|---|
| **rho** | Perpendicular distance from origin to detected line | Hough |
| **theta** | Angle of the perpendicular to the line, range [0, 180) | Hough |
| **m, b** | Slope and intercept (y = mx + b) -- NOT used in Hough | Line representation |
| **Accumulator[rho, theta]** | 2D vote array; each cell counts edge pixels supporting that line | Hough |
| **D** | Image diagonal length; rho ranges from -D to D | Hough |

## SIFT

| Symbol | Meaning | Context |
|---|---|---|
| **L(x, y, sigma)** | Scale-space representation = G(sigma) convolved with I | SIFT |
| **G(x, y, sigma)** | Gaussian kernel (same as filtering) -- blurs image to scale sigma | SIFT |
| **D(x, y, sigma)** | Difference of Gaussians = L(k*sigma) - L(sigma) | SIFT |
| **k** | Scale factor between adjacent Gaussian blurs (typically sqrt(2)) | SIFT |
| **Octave** | One level of the pyramid; image halved in size per octave | SIFT |
| **26 neighbors** | 8 same scale + 9 above + 9 below; used for extrema detection | SIFT |
| **128D** | Descriptor size: 4x4 grid x 8 orientation bins | SIFT |

## RANSAC

| Symbol | Meaning | Context |
|---|---|---|
| **k** | Number of iterations needed | RANSAC |
| **p** | Desired probability of finding correct model (e.g. 0.99) | RANSAC |
| **w** | Estimated inlier ratio (fraction of good points) | RANSAC |
| **n** | Minimum sample size to fit model (2 for line, 4 for homography) | RANSAC |
| **Consensus set** | Set of all inliers for a given model trial | RANSAC |
| **Threshold** | Max distance from model to count as inlier | RANSAC |

## Image Pyramids

| Symbol | Meaning | Context |
|---|---|---|
| **G_k** | Gaussian pyramid level k | Pyramids |
| **L_k** | Laplacian pyramid level k = G_k - expand(G_{k+1}) | Pyramids |
| **expand()** | Upsample and interpolate (double the size) | Pyramids |
| **2^k** | Downsampling factor at level k; size = M/2^k x N/2^k | Pyramids |

## Canny Edge Detection

| Symbol | Meaning | Context |
|---|---|---|
| **sigma** | Gaussian smoothing strength; larger = fewer edges | Canny |
| **T_high** | Strong edge threshold; pixels above = definite edges | Canny |
| **T_low** | Weak edge threshold; pixels between T_low and T_high = maybe edges | Canny |
| **Hysteresis** | Keep weak edges only if connected to strong edges | Canny |

## Patch Comparison

| Symbol | Meaning | Context |
|---|---|---|
| **SSD** | Sum of Squared Differences; lower = more similar | Matching |
| **NCC** | Normalized Cross-Correlation; range [-1,1], higher = more similar | Matching |
