# Algorithms

## Canny Edge Detection (5 steps)
1. **Gaussian smoothing** -- reduce noise
2. **Gradient calculation** -- Sobel for magnitude and direction
3. **Non-maxima suppression** -- thin edges to 1 pixel width
4. **Double thresholding** -- classify **strong** (> T_high) and **weak** (> T_low) edges
5. **Edge tracking by hysteresis** -- keep weak edges **only if connected to strong edges**

**3 parameters:** **sigma** (smoothing), **T_high**, **T_low**. Typical ratio T_high/T_low = 2:1 or 3:1. Larger sigma = fewer edges, less noise.

## Non-Maxima Suppression (NMS)
1. For each pixel with gradient magnitude **M** and direction **theta**
2. **Quantize theta** to 4 directions: **0** (left/right), **45** (diagonal), **90** (up/down), **135** (other diagonal)
3. Check **two neighbors** along theta direction
4. If **M > both neighbors: keep.** Otherwise: **suppress to 0**

**Example:**
```
Gradient magnitude:    Gradient direction: all 0 (horizontal)
[10  20  30]
[15  40  25]           Center pixel (40): check left (15) and right (25)
[10  20  30]           40 > 15 and 40 > 25 --> KEEP (local max)
```

## Harris Corner Detection
1. **Compute gradients** Ix, Iy (using Sobel or [-1 0 1])
2. **Compute products:** Ix^2, Iy^2, Ix*Iy
3. **Gaussian weighting** -- smooth each product over window
4. **Compute R** = det(M) - k*(trace(M))^2 at each pixel
5. **Threshold** R and apply **NMS**

## Hough Transform (Line Detection)
1. **Edge detection** (e.g. Canny)
2. For each edge pixel **(x, y):**
   - For **theta = 0 to 180** degrees (in increments):
     - **rho = x*cos(theta) + y*sin(theta)**
     - **Accumulator[rho, theta] += 1** (cast vote)
3. **Find peaks** in accumulator array
4. Each peak **(rho\*, theta\*)** = one detected line

**Accumulator:** 2D array [rho_bins x theta_bins]. Each cell counts votes. **Peak = detected line.**

## RANSAC
1. **Randomly select** minimum sample (**n** points)
2. **Fit model** to sample
3. **Count inliers** (points within distance threshold of model)
4. If inlier count > best so far: **save model**
5. **Repeat** for k iterations
6. (Optional) **Refit** best model using **all its inliers**

**Minimum sample sizes:**

| Model | Min Points |
|---|---|
| **Line** | 2 |
| **Plane** | 3 |
| **Homography** | 4 pairs |
| **Fundamental matrix** | 8 pairs |

## SIFT Pipeline (4 stages)
1. **Scale-space extrema detection** -- build DoG pyramid, find local max/min across **26 neighbors** (8 same scale + 9 above + 9 below)
2. **Keypoint localization** -- sub-pixel refinement (Taylor expansion), reject low-contrast and edge responses
3. **Orientation assignment** -- gradient orientation histogram (**36 bins, 10 deg each**), dominant peak = keypoint orientation. Multiple peaks = multiple keypoints.
4. **Descriptor generation** -- **4x4 grid**, **8-bin** orientation histogram per cell = **128D vector**, normalized for illumination invariance

**Matching:** Euclidean distance between descriptors. **Ratio test:** dist(best)/dist(second-best) < threshold.

## Gaussian Pyramid Construction
```
For each level k (starting from level 0 = original):
  1. Convolve with Gaussian kernel (smooth)
  2. Downsample by factor of 2 (keep every other pixel)
  Level k size: M/2^k x N/2^k
```
Each level is **blurrier and smaller**. **Low-pass filtered.** Cannot reconstruct original.

## Laplacian Pyramid Construction
```
Requires Gaussian pyramid first.
For each level k:
  L_k = G_k - expand(G_{k+1})
Top level: L_top = G_top (residual)
```
Stores **band-pass detail** (edges/textures at each scale). **CAN reconstruct original:** G_k = L_k + expand(G_{k+1}).

## Scale-Space Pyramid (SIFT)
```
Multiple octaves (each octave = image halved in size)
Within each octave: multiple Gaussian blurs at increasing sigma
DoG computed between adjacent Gaussian images
Typically 4-5 octaves, 3-5 scales per octave
```
