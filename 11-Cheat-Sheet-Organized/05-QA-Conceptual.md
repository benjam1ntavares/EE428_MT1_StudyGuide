# Q&A and Conceptual Answers

## Image Formation

**Perspective projection:** `x = fX/Z, y = fY/Z`. **Objects further away (larger Z) appear smaller.**

**Why parallel lines converge:** constant separation in X but increasing Z. As Z grows, projected positions fX/Z converge toward 0. Convergence point = **vanishing point**.

**Homogeneous coordinates:** turn nonlinear division-by-Z into linear matrix multiply. Useful for **chaining transforms** (rotation + translation + projection).

**Coordinate systems:** matrix I(row, col) maps to image I(x, y) by swapping: **row = y, col = x.** So matrix (3,5) = image (x=5, y=3).

**Image types:** **binary** (0/1), **grayscale** (0-255, 8-bit), **color** (RGB, 3 channels x 8-bit), **labeled** (integer per region).

**Sampling vs Quantization:**
- **Sampling:** continuous space -> discrete pixels. Too low = **aliasing**
- **Quantization:** continuous intensity -> discrete levels. Too low = **posterization**

**Pinhole camera properties:** no lens distortion, infinite depth of field, inverted image, smaller aperture = sharper but darker.

---

## Filtering

**Apply filter to patch:** multiply each kernel weight by corresponding pixel, sum all products.

**Worked example (Sobel Gx):**
```
Image patch:       Gx mask:
[10  20  30]       [-1  0  1]
[15  25  35]  x    [-2  0  2]
[20  30  40]       [-1  0  1]

= (-1)(10)+(0)(20)+(1)(30)+(-2)(15)+(0)(25)+(2)(35)+(-1)(20)+(0)(30)+(1)(40)
= -10+0+30-30+0+70-20+0+40 = 80
```

**Salt & pepper noise:** use **median filter**. Sorts neighborhood, picks middle value, ignoring extreme outliers. Mean/Gaussian would average the outlier in.

**Mean vs Gaussian:** mean treats all neighbors equally (blurs edges). Gaussian weights center more (**better edge preservation**). Both good for Gaussian noise.

**Why median preserves edges:** **non-linear** -- picks an existing pixel value. At an edge, majority of window is on one side, so median selects that side. Linear filters blur by averaging across the boundary.

---

## Edge Detection

**Sobel hand calculation:**
```
Patch: [50 50 50; 50 50 100; 50 50 100]

Gx = (-1)(50)+(0)(50)+(1)(50)+(-2)(50)+(0)(50)+(2)(100)+(-1)(50)+(0)(50)+(1)(100)
   = -50+0+50-100+0+200-50+0+100 = 150

Gy = (-1)(50)+(-2)(50)+(-1)(50)+(0)(50)+(0)(50)+(0)(100)+(1)(50)+(2)(50)+(1)(100)
   = -50-100-50+0+0+0+50+100+100 = 50

|G| = sqrt(150^2 + 50^2) = sqrt(25000) = 158.1
theta = atan2(50, 150) = 18.4 degrees
```

**Canny parameters:** **3 parameters.** **sigma** (smoothing strength -- larger = fewer edges, less noise), **T_high** (strong edge threshold), **T_low** (weak edge threshold). Weak edges kept only if connected to strong edges.

**NMS:** thins edges to 1 pixel. Check two neighbors along gradient direction. **Keep pixel only if it's the local maximum.**

**1st vs 2nd order:** 1st order (Sobel, Canny) finds edges where **gradient is large**, gives direction. 2nd order (Laplacian, LoG) finds edges at **zero-crossings**, no direction, isotropic. LoG adds Gaussian smoothing to reduce noise sensitivity.

---

## Hough Transform

**Objective:** detect lines **robustly despite noise/gaps**. Transform edge pixels to parameter space, find peaks.

**rho-theta preferred:** slope-intercept can't handle vertical lines, unbounded parameter space. rho-theta handles **all line orientations** with bounded params.

**Accumulator cell:** one bin in (rho, theta) space. Value = number of votes. **Peak = detected line.**

**Collinear points create peaks:** each point traces a **sinusoid** in (rho, theta) space. Collinear points' sinusoids all **intersect at the same point**.

**Worked examples:**
```
Point (3, 4):
  theta=0:  rho = 3*cos(0) + 4*sin(0)   = 3*1 + 4*0     = 3
  theta=30: rho = 3*cos(30) + 4*sin(30)  = 2.598 + 2.0   = 4.6
  theta=60: rho = 3*cos(60) + 4*sin(60)  = 1.5 + 3.464   = 5.0
  theta=90: rho = 3*cos(90) + 4*sin(90)  = 0 + 4          = 4

Vertical line x=5: theta=0, rho=5
Horizontal line y=3: theta=90, rho=3

Collinear points (0,1),(1,2),(2,3) on y=x+1:
  All give rho=0.707 at theta=135 --> theta=135, rho=sqrt(2)/2

Convert y=2x+3: rho = 3/sqrt(5) = 1.342, theta = 180-arctan(2) = 153.4
```

---

## Corner Detection (Harris)

**Feature points are useful:** **distinctive, repeatable, invariant, sparse, informative**. Used for matching, recognition, 3D reconstruction.

**M matrix:** encodes local gradient structure. **Eigenvalues describe intensity variation.** Both large = corner, one large = edge, both small = flat.

**Compute R:** `R = det(M) - k*trace(M)^2`. **det(M) = AB - C^2, trace(M) = A + B.** No need to compute actual eigenvalues.

**Worked example:**
```
Patch: [10 10 10; 10 50 50; 10 50 50]
Gradients at center: Ix = 40, Iy = 40
M = [1600  1600]
    [1600  1600]

det(M) = 1600*1600 - 1600*1600 = 0
trace(M) = 3200
R = 0 - 0.04*(3200)^2 = -409,600
R < 0 --> EDGE (not a corner!)
```

---

## Image Pyramids

**Gaussian pyramid:** smooth with Gaussian, **downsample by 2**. Level k = M/2^k x N/2^k. **Cannot reconstruct** original (high-freq info lost).

**Laplacian pyramid:** L_k = G_k - expand(G_{k+1}). Stores **detail/differences**. **CAN reconstruct:** G_k = L_k + expand(G_{k+1}).

**Why pyramids:** multi-scale feature detection, **coarse-to-fine** processing, handle objects at different sizes efficiently.

---

## SIFT

**What it does:** detects and describes local features. **Scale + rotation + illumination invariant.** 128D descriptor matched via **Euclidean distance**.

**Scale space:** L(x,y,sigma) = G(sigma) * I. Image at multiple scales. Features found at their **"natural" scale**.

**DoG:** D = L(k*sigma) - L(sigma). **Approximates scale-normalized Laplacian.** Extrema in DoG = candidate keypoints.

**Pyramid structure:** multiple **octaves** (image halved each time), multiple scales per octave, DoG between adjacent. Keypoint = **extremum among 26 neighbors** (8 same + 9 above + 9 below).

**128 dimensions:** 4x4 spatial grid, 8 orientation bins per cell. Balances **distinctiveness with efficiency**.

**Orientation assignment:** 36-bin histogram of gradient orientations around keypoint. Dominant peak = assigned orientation. Descriptor computed **relative to this orientation** --> rotation invariance.

---

## RANSAC

**What it does:** **robust model fitting ignoring outliers.** Random sample, fit, count inliers, keep best.

**Algorithm:** (1) sample n minimum points, (2) fit model, (3) count inliers within threshold, (4) save if best, (5) repeat k times, (6) optionally refit with all inliers.

**Sample sizes:** line = 2, plane = 3, homography = 4 pairs, fundamental matrix = 8 pairs.

**Iterations:** `k = log(1-p)/log(1-w^n)`. More outliers or larger sample = exponentially more iterations.

**Worked example:**
```
w = 0.7 (70% inliers), p = 0.99, n = 2 (line fitting)
k = log(1-0.99) / log(1-0.7^2)
  = log(0.01) / log(0.51)
  = -4.605 / -0.673
  = 7 iterations
```

**Why robust:** outliers can't influence the model -- only random samples of n points determine each trial. **Bad trials get few inliers and are discarded.**

**Consensus set:** the set of all inliers for a given model. **Best model = largest consensus set.**
