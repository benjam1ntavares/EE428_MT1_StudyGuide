# Image Formation and Representation

### 1. Mathematical Representation of Digital Images
- **Digital image:** 2D matrix I(row, col) or I(x, y)
- **Coordinate system:**
  - Matrix notation: I(row, col) - row increases downward
  - Image notation: I(x, y) - x is horizontal, y is vertical
  - MATLAB uses 1-based indexing: I(1,1) is top-left

### 2. Sampling and Quantization
**Sampling:** Converting continuous spatial domain -> discrete pixels
- Controls spatial resolution
- Higher sampling = more pixels = finer detail
- Aliasing occurs when sampling rate too low

**Quantization:** Converting continuous intensity -> discrete levels
- Controls intensity resolution (bit depth)
- 8-bit = 256 levels (0-255)
- Lower quantization = posterization effect

### 3. Pinhole Camera Model
**Geometry:**
```
3D World Point (X, Y, Z)
      |
  Pinhole (optical center)
      |
2D Image Point (x, y)
```

**Key properties:**
- No lens distortion
- Infinite depth of field
- Inverted image
- Smaller aperture -> sharper but darker

### 4. Perspective Projection Equations
**Basic equations:**
```
x = f * (X / Z)
y = f * (Y / Z)
```
Where:
- (X, Y, Z) = 3D world coordinates
- (x, y) = 2D image coordinates
- f = focal length
- Z = depth (distance from camera)

**Key insight:** Objects further away (larger Z) appear smaller

### 5. Homogeneous Coordinates
**Why use them?**
- Represent both points and lines uniformly
- Enable matrix operations for transformations
- Handle points at infinity

**Conversion:**
- Cartesian (x, y) -> Homogeneous [x, y, 1]
- Homogeneous [x, y, w] -> Cartesian (x/w, y/w)

**Projection Matrix:**
```
[x']   [f  0  0  0] [X]
[y'] = [0  f  0  0] [Y]
[z']   [0  0  1  0] [Z]
                     [1]

Then: x = x'/z', y = y'/z'
```

### 6. Effects of Perspective Projection
- **Parallel lines converge** to vanishing points
- **Size decreases** with distance
- **Shape distortion** for non-frontal planes
- **Depth ambiguity** - multiple 3D points project to same 2D point

### 7. Digital Image Types
**Binary images:**
- Pixels are 0 or 1
- Used for segmentation masks, text

**Grayscale images:**
- Typically 8-bit: 0 (black) to 255 (white)
- Single intensity value per pixel

**Color images:**
- RGB: 3 channels (Red, Green, Blue)
- Each channel typically 8-bit
- Other spaces: HSV, YCbCr, LAB

**Labeled images:**
- Each pixel has integer label
- Used for segmentation (different regions)

---

## Answers
1. x = 50 * (6/10) = **30mm**, y = 50 * (3/10) = **15mm**. Image coordinates: **(30, 15) mm**.
2. Matrix notation I(row, col) maps to image notation I(x, y) by swapping axes: row corresponds to y (vertical, increasing downward) and col corresponds to x (horizontal, increasing rightward). So matrix I(r, c) = image I(c, r). Example: matrix position (3, 5) = image coordinates (x=5, y=3).
3. Perspective projection maps 3D points via x = f(X/Z). Railroad tracks have constant separation in X but increasing Z with distance. As Z grows, the projected x-coordinates f(X/Z) for each rail get closer together, converging toward x = 0 at Z = infinity. That convergence point is the **vanishing point**.

# Image Filtering

### 1. Linear Filtering (Convolution)
**CRITICAL: Must be able to do by hand!**

**Process:**
1. Place filter mask over image region
2. Multiply corresponding elements
3. Sum all products
4. Result is output pixel value

**Example: 3x3 Sobel filter**
```
Filter Gx:          Image patch:
[-1  0  1]          [10  20  30]
[-2  0  2]    *     [15  25  35]
[-1  0  1]          [20  30  40]

Result = (-1x10)+(0x20)+(1x30)+(-2x15)+(0x25)+(2x35)+(-1x20)+(0x30)+(1x40)
       = -10 + 0 + 30 - 30 + 0 + 70 - 20 + 0 + 40
       = 80
```

**Boundary handling:**
- Zero padding
- Replicate edges
- Symmetric/mirror

### 2. Median Filtering
**Non-linear filter - replaces with median value**

**Process:**
1. Extract neighborhood (e.g., 3x3)
2. Sort pixel values
3. Take middle value

**Example:**
```
Neighborhood:  [10, 15, 20, 25, 30, 100, 35, 40, 45]
Sorted:        [10, 15, 20, 25, 30, 35, 40, 45, 100]
Median:        30  (removes outlier 100!)
```

### 3. Noise Types

| Noise Type | Characteristics | Probability Distribution |
|------------|----------------|-------------------------|
| **Gaussian** | Additive, normally distributed | Bell curve, mean u, std sigma |
| **Salt & Pepper** | Random black/white pixels | Impulse noise at extremes |
| **Uniform** | Equal probability across range | Flat distribution |
| **Speckle** | Multiplicative noise | Common in ultrasound/radar |

### 4. Filter Type Comparison

| Filter | Construction | Best For | Edge Preservation | Speed |
|--------|-------------|----------|-------------------|-------|
| **Mean** | All weights = 1/N | Gaussian noise | Poor - blurs edges | Fast |
| **Gaussian** | Weights from Gaussian function | Gaussian noise | Better than mean | Fast |
| **Median** | Non-linear, sort values | Salt & pepper | Excellent | Slower |

### 5. Mean Filter
**Box filter - all weights equal**

3x3 Mean filter:
```
[1  1  1]
[1  1  1]  x (1/9)
[1  1  1]
```

**Properties:**
- Simple averaging
- Reduces Gaussian noise
- Blurs edges significantly

### 6. Gaussian Filter
**Weights based on 2D Gaussian function:**

```
G(x,y) = (1 / (2*pi*sigma^2)) x exp(-(x^2 + y^2) / (2*sigma^2))
```

**Example 3x3 (sigma=1, approximated):**
```
[1  2  1]
[2  4  2]  x (1/16)
[1  2  1]
```

**Properties:**
- Weighted smoothing (center pixel weighted more)
- sigma controls amount of smoothing
- Separable: 2D filter = 1D horizontal x 1D vertical
- Better edge preservation than box filter

### 7. Filter Effectiveness

**Gaussian Noise:**
- Mean filter: Good
- Gaussian filter: Better (weighted)
- Median filter: Poor (doesn't assume distribution)

**Salt & Pepper Noise:**
- Mean filter: Poor (spreads noise)
- Gaussian filter: Poor (spreads noise)
- Median filter: Excellent (removes outliers)

---

## Answers to Common Exam Questions

**1. "Apply this filter to this image patch"**
Multiply each filter weight by the corresponding image pixel, sum all products. For a 3x3 mean filter on patch [10 20 30; 40 50 60; 70 80 90]: sum all = 450, divide by 9 = **50**. Show every multiply-and-add step.

**2. "Which filter for salt & pepper noise?"**
**Median filter.** Salt & pepper creates isolated extreme-value pixels. Median sorts the neighborhood and picks the middle value, so the outlier pixels are ignored entirely. Mean/Gaussian filters would average the outlier in, spreading the noise.

**3. "Compare mean vs Gaussian filter"**
- Mean: all weights equal (1/N). Treats every neighbor the same. Blurs edges heavily.
- Gaussian: center-weighted (weights fall off with distance). Better edge preservation because distant pixels contribute less. Both reduce Gaussian noise; Gaussian filter is generally preferred.

**4. "Why does median preserve edges?"**
It is non-linear -- it selects an existing pixel value rather than averaging. At an edge, the majority of pixels in the window are on one side, so the median picks a value from that side. The edge stays sharp. Linear filters blur by averaging across the edge boundary.

# Edge Detection

### 1. Edge Detection Fundamentals
**What is an edge?**
- Significant local change in intensity
- Boundary between regions
- Indicates depth discontinuity, surface orientation change, or material property change

**Detection methods:**
- **First-order derivatives:** Gradient-based (Sobel, Prewitt, Roberts)
- **Second-order derivatives:** Laplacian-based (LoG, Marr-Hildreth)

### 2. Sobel Operator
**MEMORIZE THESE MASKS!**

```
Horizontal (Gx):        Vertical (Gy):
[-1  0  1]              [-1 -2 -1]
[-2  0  2]              [ 0  0  0]
[-1  0  1]              [ 1  2  1]
```

**Gradient magnitude:**
```
|G| = sqrt(Gx^2 + Gy^2)
```

**Gradient direction:**
```
theta = atan2(Gy, Gx)  or  theta = arctan(Gy / Gx)
```

**Hand calculation example:**
```
Image patch:
[50  50  50]
[50  50  100]
[50  50  100]

Gx = (-1x50)+(0x50)+(1x50)+(-2x50)+(0x50)+(2x100)+(-1x50)+(0x50)+(1x100)
   = -50 + 0 + 50 - 100 + 0 + 200 - 50 + 0 + 100 = 150

Gy = (-1x50)+(-2x50)+(-1x50)+(0x50)+(0x50)+(0x100)+(1x50)+(2x50)+(1x100)
   = -50 - 100 - 50 + 0 + 0 + 0 + 50 + 100 + 100 = 50

|G| = sqrt(150^2 + 50^2) = sqrt(22500 + 2500) = sqrt(25000) = 158
theta = atan2(50, 150) = 18.4 degrees
```

### 3. Prewitt Operator
**Similar to Sobel, different weights:**

```
Horizontal (Gx):        Vertical (Gy):
[-1  0  1]              [-1 -1 -1]
[-1  0  1]              [ 0  0  0]
[-1  0  1]              [ 1  1  1]
```

**Comparison with Sobel:**
- Prewitt: Uniform weighting
- Sobel: Center row/column weighted more (2x)
- Sobel typically better in practice

### 4. Laplacian Operator
**Second-order derivative detector:**

Common 3x3 Laplacian masks:
```
4-connected:          8-connected:
[ 0  1  0]            [ 1  1  1]
[ 1 -4  1]            [ 1 -8  1]
[ 0  1  0]            [ 1  1  1]
```

**Properties:**
- Isotropic (rotation invariant)
- Sensitive to noise
- Detects zero-crossings
- No direction information

### 5. Laplacian of Gaussian (LoG)
**Combines smoothing + edge detection:**

**Process:**
1. Smooth with Gaussian: G(x,y) = exp(-(x^2 + y^2)/(2*sigma^2))
2. Apply Laplacian

**Combined operator:**
```
LoG = (x^2 + y^2 - 2*sigma^2) / sigma^4 x exp(-(x^2 + y^2)/(2*sigma^2))
```

**Properties:**
- Reduces noise sensitivity
- sigma controls scale of edges detected
- Zero-crossings indicate edges

### 6. Canny Edge Detection
**Multi-stage optimal edge detector:**

**Algorithm steps:**
1. **Gaussian smoothing:** Reduce noise
2. **Gradient calculation:** Find magnitude and direction (Sobel)
3. **Non-maxima suppression:** Thin edges to 1 pixel width
4. **Double thresholding:** High & low thresholds
5. **Edge tracking by hysteresis:** Connect weak edges to strong

**Parameters:**
- **sigma:** Gaussian smoothing strength
  - Larger sigma -> smoother, fewer edges
  - Smaller sigma -> more edges, more noise
- **High threshold:** Strong edges (definite edges)
- **Low threshold:** Weak edges (candidate edges)
- **Threshold ratio:** Typically high/low = 2:1 or 3:1

**How many parameters?**
**3 parameters:** sigma, T_high, T_low

### 7. Non-Maxima Suppression (NMS)
**CRITICAL: Understand this process!**
**Purpose:** Thin thick edges to 1-pixel width

**Algorithm:**
1. For each pixel with gradient magnitude M and direction theta:
2. Interpolate gradient values of two neighbors along theta direction
3. If M is greater than both neighbors -> keep
4. Otherwise -> suppress (set to 0)

**Example:**
```
Gradient magnitude:   Gradient direction:
[10  20  30]          [->  ->  ->]
[15  40  25]          [->  ->  ->]
[10  20  30]          [->  ->  ->]

Center pixel (40):
- Check left neighbor (15) and right neighbor (25)
- 40 > 15 and 40 > 25 -> KEEP
- Result: Center stays 40, others may be suppressed
```

**4 quantized directions:**
- 0 degrees (->): Check left and right
- 45 degrees: Check diagonal neighbors
- 90 degrees: Check up and down
- 135 degrees: Check other diagonal

---

## Comparison Table

| Method | Order | Noise Sensitivity | Edge Localization | Direction Info |
|--------|-------|-------------------|-------------------|----------------|
| Sobel | 1st | Medium | Good | Yes |
| Prewitt | 1st | Medium | Good | Yes |
| Laplacian | 2nd | High | Good | No |
| LoG | 2nd | Low | Good | No |
| Canny | 1st | Low | Excellent | Yes |

## Answers to Common Exam Problems

**1. Apply Sobel to image patch**
Given a 3x3 patch, apply Gx and Gy masks separately. Multiply element-wise and sum. Example with patch [50 50 50; 50 50 100; 50 50 100]:
- `Gx = (-1)(50)+(0)(50)+(1)(50)+(-2)(50)+(0)(50)+(2)(100)+(-1)(50)+(0)(50)+(1)(100) = 150`
- `Gy = (-1)(50)+(-2)(50)+(-1)(50)+(0)(50)+(0)(50)+(0)(100)+(1)(50)+(2)(50)+(1)(100) = 50`

**2. Calculate gradient magnitude and direction**
From Gx and Gy above:
- `|G| = sqrt(150^2 + 50^2) = sqrt(25000) = 158.1`
- `theta = atan2(50, 150) = 18.4 degrees`

**3. How many Canny parameters and what do they do?**
**3 parameters:**
- `sigma` -- controls Gaussian smoothing. Larger = fewer edges, less noise.
- `T_high` -- strong edge threshold. Pixels above this are definite edges.
- `T_low` -- weak edge threshold. Pixels between T_low and T_high are edges only if connected to a strong edge (hysteresis).

**4. Explain non-maxima suppression**
Thins thick edges to 1-pixel width. For each pixel, look at the gradient direction theta, check the two neighbors along that direction. If the pixel's gradient magnitude is greater than both neighbors, keep it; otherwise suppress to 0. Directions are quantized to 4 bins: 0, 45, 90, 135 degrees.

**5. Compare first vs second-order methods**
- First-order (Sobel, Prewitt, Canny): use gradient (first derivative). Find edges where gradient magnitude is large. Provide edge direction. Less noise-sensitive.
- Second-order (Laplacian, LoG): use second derivative. Find edges at zero-crossings. No direction info. More noise-sensitive (Laplacian alone), but LoG mitigates this with Gaussian smoothing. Laplacian is isotropic (rotation invariant).

# Hough Transform

### 1. Objective of Hough Transform
**Purpose:**
- Detect parametric shapes (lines, circles, ellipses) in images
- Robust to noise and gaps in edge data
- Group individual edge pixels into higher-level features

**Why it works:**
- Transforms detection problem into peak-finding problem
- Collinear points vote for same parameters
- Outliers don't form peaks

### 2. Core Principle
**Image Space -> Parameter Space Transformation**

**For lines:**
- Image space: Points (x, y)
- Parameter space: Line parameters (m, b) or (rho, theta)

**Key insight:**
- One point in image space -> Curve in parameter space
- Collinear points -> Curves intersect at one point in parameter space
- Intersection point = line parameters

### 3. Line Representations

#### Slope-Intercept Form
```
y = mx + b
```
- m = slope
- b = y-intercept

**Problems:**
- Cannot represent vertical lines (m = infinity)
- Unbounded parameter space for steep lines
- Not used in Hough Transform

#### Normal (rho-theta) Form -- PREFERRED
```
rho = x*cos(theta) + y*sin(theta)
```

**Parameters:**
- rho: Perpendicular distance from origin to line
- theta: Angle of perpendicular (0 to 180 degrees)

**Advantages:**
- Can represent ALL lines (including vertical)
- Bounded parameter space
- rho in [-D, D] where D = diagonal of image
- theta in [0, 180)

**Conversion between forms:**
```
Given: y = mx + b
rho = b / sqrt(1 + m^2)
theta = arctan(-1/m)
```

### 4. Hough Transform Algorithm

**For each edge pixel (xi, yi):**

```
For theta = 0 to 180 (in small increments):
    rho = xi*cos(theta) + yi*sin(theta)
    Accumulator[rho, theta] += 1  // Vote for this line
```

**After processing all pixels:**
```
Find peaks in accumulator array
Each peak (rho*, theta*) represents a detected line
```

### 5. Accumulator Array
**2D array in parameter space:**
- Dimensions: [rho_bins x theta_bins]
- Each cell = **accumulator cell** = one bin in (rho, theta) space
- Value = number of edge pixels voting for that line

**Example dimensions:**
- theta: 180 bins (1 degree resolution)
- rho: 2D bins where D = image diagonal

### 6. Parameter Space Mapping

**Key transformations:**

**One point (x0, y0) ->**
```
rho = x0*cos(theta) + y0*sin(theta)
```
- For varying theta, traces sinusoidal curve in (rho, theta) space

**Two collinear points ->**
- Both curves intersect at same (rho, theta)
- Intersection = line containing both points

**Three collinear points ->**
- All three curves intersect at same (rho, theta)
- Strong peak in accumulator!

### Worked Examples
```
Point (3, 4):
  theta=0:   rho = 3*cos(0) + 4*sin(0)   = 3*1 + 4*0     = 3
  theta=30:  rho = 3*cos(30) + 4*sin(30)  = 2.598 + 2.0   = 4.6
  theta=60:  rho = 3*cos(60) + 4*sin(60)  = 1.5 + 3.464   = 5.0
  theta=90:  rho = 3*cos(90) + 4*sin(90)  = 0 + 4          = 4

Vertical line x=5: theta=0, rho=5
Horizontal line y=3: theta=90, rho=3

Collinear points (0,1),(1,2),(2,3) on y=x+1:
  All give rho=0.707 at theta=135 --> theta=135, rho=sqrt(2)/2

Convert y=2x+3: rho = 3/sqrt(5) = 1.342, theta = 180-arctan(2) = 153.4
```

---

## Answers to Common Exam Questions

**Q1: Why use rho-theta instead of slope-intercept?**
Slope-intercept y=mx+b cannot represent vertical lines (m=infinity) and has unbounded parameter space. rho-theta handles ALL line orientations with bounded parameters: rho in [-D, D], theta in [0, 180).

**Q2: What does the accumulator array represent?**
A 2D vote array in (rho, theta) space. Each cell counts edge pixels supporting that line. Peaks = detected lines. One edge point traces a sinusoid in the accumulator; collinear points' sinusoids intersect at the same cell.

**Q3: What is the objective of the Hough Transform?**
Detect parametric shapes (lines, circles) robustly despite noise and gaps. Transforms detection into peak-finding: collinear points all vote for the same (rho, theta), creating a peak. Outliers scatter votes and don't form peaks.

# Corner Detection (Harris)

### 1. Why Feature Points?
**Properties of good features:**
- **Distinctive:** easy to localize
- **Repeatable:** found again under different conditions
- **Invariant:** robust to rotation, scale, illumination
- **Sparse:** efficient representation
- **Informative:** encode local structure

Used for matching, recognition, 3D reconstruction, and tracking.

### 2. Second Moment Matrix M
```
M = SUM w(x,y) [Ix^2    Ix*Iy]  = [A  C]
    x,y        [Ix*Iy   Iy^2 ]    [C  B]

A = SUM Ix^2,  B = SUM Iy^2,  C = SUM Ix*Iy
det(M) = AB - C^2
trace(M) = A + B
```

M encodes local gradient structure. Eigenvalues describe intensity variation. Geometrically, M defines an ellipse whose axes correspond to eigenvalues/eigenvectors.

### 3. Error Function
```
E(u,v) = SUM w(x,y) [I(x+u, y+v) - I(x,y)]^2
```
**Taylor approx:** `I(x+u, y+v) ~ I(x,y) + Ix*u + Iy*v`
**Simplified:** `E(u,v) ~ [u v] M [u; v]`

### 4. Harris Corner Response
```
R = det(M) - k*(trace(M))^2
R = lambda1*lambda2 - k*(lambda1 + lambda2)^2
```
k = 0.04 to 0.06

### 5. Eigenvalue Interpretation

| lambda1 | lambda2 | Region | Harris R |
|---------|---------|--------|----------|
| Small | Small | **Flat** | \|R\| small |
| **Large** | Small | **Edge** | R < 0 |
| **Large** | **Large** | **Corner** | **R > threshold** |

### 6. Harris Corner Detection Algorithm
1. **Compute gradients** Ix, Iy (using Sobel or [-1 0 1])
2. **Compute products:** Ix^2, Iy^2, Ix*Iy
3. **Gaussian weighting** -- smooth each product over window
4. **Compute R** = det(M) - k*(trace(M))^2 at each pixel
5. **Threshold** R and apply **NMS**

### Worked Example
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

### Patch Comparison
- **SSD:** `SUM (I1(x,y) - I2(x,y))^2` -- **lower = more similar**
- **NCC:** `SUM(I1*I2) / sqrt(SUM(I1^2) * SUM(I2^2))` -- range [-1,1], **higher = more similar**

---

## Answers to Common Exam Questions

**Q1: Why are feature points useful?**
They are distinctive (easy to localize), repeatable (found again under different conditions), invariant (robust to rotation, scale, illumination), sparse (efficient), and informative (encode local structure). Used for matching, recognition, 3D reconstruction, and tracking.

**Q2: What does the second moment matrix M represent?**
M encodes the local gradient structure around a pixel. Its eigenvalues describe the intensity variation: both large = corner (intensity changes in all directions), one large = edge (change in one direction only), both small = flat region. Geometrically, M defines an ellipse whose axes correspond to the eigenvalues/eigenvectors.

**Q3: How to compute Harris R?**
`R = det(M) - k*(trace(M))^2`, where `det(M) = AB - C^2` and `trace(M) = A + B`, with `A = sum(Ix^2)`, `B = sum(Iy^2)`, `C = sum(Ix*Iy)`, and k typically 0.04-0.06. Equivalently `R = lambda1*lambda2 - k*(lambda1 + lambda2)^2`. R > threshold = corner, R < 0 = edge, |R| small = flat.

**Q4: What do eigenvalues of M tell us?**
- Both lambda1 and lambda2 large: **corner** -- intensity changes significantly in multiple directions
- One large, one small: **edge** -- intensity changes in one direction only
- Both small: **flat region** -- no significant intensity change

**Q5: Given gradients Ix, Iy at a point, compute M**
Example: if `Ix = 40, Iy = 30` at a pixel, then `M = [Ix^2, Ix*Iy; Ix*Iy, Iy^2] = [1600, 1200; 1200, 900]`. For a window of pixels, sum these contributions (with Gaussian weighting) across all pixels in the window.

# Image Pyramids

### 1. What are Image Pyramids?
**Definition:**
- Multi-scale representation of an image
- Series of images at progressively lower resolutions
- Each level is half the size of previous level

**Why use them?**
- Detect features at multiple scales
- Coarse-to-fine processing
- Efficient for hierarchical algorithms
- Handle scale invariance

### 2. Gaussian Pyramid
**Construction process:**

**Level 0 (original):**
```
Original image (size: M x N)
```

**Level 1:**
```
1. Apply Gaussian filter (smooth)
2. Subsample by factor of 2 (size: M/2 x N/2)
```

**Level k:**
```
Size: M/2^k x N/2^k
```

**Algorithm:**
```
For each level:
    1. Convolve with Gaussian kernel
    2. Downsample (keep every other pixel)
```

**Properties:**
- Each level is smoothed version of previous
- Removes high-frequency details progressively
- Used for: multi-scale analysis, image blending

### 3. Laplacian Pyramid
**Construction process:**

**Requires Gaussian pyramid first!**

For each level k:
```
L_k = G_k - expand(G_{k+1})
```

Where:
- G_k = Gaussian pyramid level k
- expand() = upsample and interpolate
- L_k = Laplacian level (difference image)

**What it captures:**
- Band-pass filtered information
- Details lost between pyramid levels
- Edge and texture information at each scale

**Reconstruction:**
```
G_k = L_k + expand(G_{k+1})
```
- Can reconstruct original from Laplacian pyramid
- Lossless representation (with proper expand)

### 4. Comparison: Gaussian vs Laplacian

| Property | Gaussian Pyramid | Laplacian Pyramid |
|----------|------------------|-------------------|
| Content | Smoothed images | Difference images (edges) |
| Reconstruction | Cannot reconstruct original | Can reconstruct original |
| Information | Low-pass filtered | Band-pass filtered |
| Use case | Feature detection | Image compression, blending |

### 5. Applications

**Gaussian Pyramid:**
- Multi-scale feature detection (SIFT)
- Fast image search
- Texture analysis

**Laplacian Pyramid:**
- Image compression
- Image blending (seam carving)
- Edge detection at multiple scales

---

## Visual Example

### Gaussian Pyramid (3 levels)
```
Level 0: [Original 512x512]
           | (smooth + downsample)
Level 1: [256x256]
           | (smooth + downsample)
Level 2: [128x128]
```

### Laplacian Pyramid
```
L0 = G0 - expand(G1)    [512x512 detail]
L1 = G1 - expand(G2)    [256x256 detail]
L2 = G2                  [128x128 residual]
```

---

## Answers to Common Exam Questions

**Q1: How is a Gaussian pyramid constructed?**
Start with the original image (level 0). For each subsequent level: (1) convolve with a Gaussian filter to smooth, (2) downsample by a factor of 2 (keep every other pixel in each dimension). Level k has size `M/2^k x N/2^k`. Each level is a blurrier, smaller version of the previous.

**Q2: How is the Laplacian pyramid different?**
The Laplacian pyramid stores the *difference* (detail) between consecutive Gaussian levels: `L_k = G_k - expand(G_{k+1})`. It captures band-pass information (edges and textures at each scale). Unlike the Gaussian pyramid, the Laplacian pyramid can perfectly reconstruct the original image.

**Q3: What is the size at level k?**
For an original image of size M x N, level k has size `M/2^k x N/2^k`. Example: 512x512 original -> level 1: 256x256 -> level 2: 128x128 -> level 3: 64x64.

**Q4: Why use image pyramids?**
- Multi-scale feature detection (objects/features appear at different sizes)
- Coarse-to-fine processing (fast approximate search at low res, refine at high res)
- Handle scale invariance in recognition tasks
- Efficient hierarchical algorithms (e.g., optical flow, template matching)

**Q5: Can you reconstruct from a Gaussian pyramid?**
**No.** Gaussian smoothing discards high-frequency information permanently. You cannot recover the original from a Gaussian pyramid. **Yes from a Laplacian pyramid:** `G_k = L_k + expand(G_{k+1})`, working from the coarsest level back up to full resolution.

# SIFT (Scale-Invariant Feature Transform)

### 1. What Does SIFT Do?
**Purpose:**
- Detect distinctive local features in images
- Match features between different images
- Robust to scale, rotation, illumination changes

**SIFT Pipeline:**
1. **Scale-space extrema detection**
2. **Keypoint localization**
3. **Orientation assignment**
4. **Descriptor generation**

### 2. Properties of SIFT Features
**Invariance:**
- **Scale invariant:** Detects features at their characteristic scale
- **Rotation invariant:** Orientation normalized
- **Illumination invariant:** Descriptor normalized
- **Partial viewpoint invariance**

**Robustness:**
- Handles occlusion (local features)
- Robust to noise
- Works with partial matches

**Distinctiveness:**
- 128-dimensional descriptor
- Highly discriminative
- Low false positive rate

### 3. Scale Space
**Concept:**
- Continuous function of scale sigma
- Represents image at multiple scales simultaneously
- Detects features at their "natural" scale

**Scale-space representation:**
```
L(x, y, sigma) = G(x, y, sigma) * I(x, y)
```

Where:
- G(x, y, sigma) = Gaussian kernel with scale sigma
- I(x, y) = input image
- sigma = scale parameter

**Difference of Gaussians (DoG):**
```
D(x, y, sigma) = L(x, y, k*sigma) - L(x, y, sigma)
```
- Approximates scale-normalized Laplacian
- Used to find scale-space extrema
- k = constant scale factor (typically sqrt(2))

### 4. Scale-Space Pyramid Structure
**Octaves:**
- Each octave = doubling of scale (sigma)
- Image progressively downsampled
- Typically 4-5 octaves

**Scales per octave:**
- Multiple Gaussian blurs at different sigma
- Typically 3-5 scales per octave
- DoG computed between adjacent scales

**Structure:**
```
Octave 1 (original size):
  G(sigma), G(k*sigma), G(k^2*sigma), ...
  DoG1, DoG2, DoG3, ...

Octave 2 (half size):
  G(2*sigma), G(2k*sigma), G(2k^2*sigma), ...
  DoG1, DoG2, DoG3, ...
```

### 5. Keypoint Detection
**Finding extrema in DoG:**
- Compare pixel to 26 neighbors
  - 8 in same scale
  - 9 in scale above
  - 9 in scale below
- If pixel is local maximum or minimum -> candidate keypoint

**Refinement:**
- Sub-pixel localization (Taylor expansion)
- Reject low-contrast points
- Reject edge responses (similar to Harris)

### 6. Orientation Assignment
**Purpose:**
- Achieve rotation invariance
- Assign consistent orientation to keypoint

**Method:**
1. Compute gradient magnitude and direction in neighborhood
2. Build orientation histogram (36 bins, 10 degrees each)
3. Peak in histogram = dominant orientation
4. Multiple peaks -> multiple keypoints with different orientations

### 7. SIFT Descriptor
**128-dimensional vector:**
- 4x4 grid of cells around keypoint
- 8-bin orientation histogram per cell
- 4x4x8 = 128 dimensions

**Properties:**
- Gradient-based (captures shape)
- Normalized (illumination invariant)
- Thresholded (reduce non-linear effects)

**Matching:**
- Compare descriptors using Euclidean distance
- Typically use ratio test: dist(best)/dist(second-best) < threshold

### SIFT vs Harris

| Property | Harris | SIFT |
|----------|--------|------|
| Scale invariant | **No** | **Yes** |
| Rotation invariant | Yes | Yes |
| Provides descriptor | No (detection only) | **Yes (128D)** |
| Multi-scale | No | **Yes** (DoG pyramid) |

---

## Answers to Common Exam Questions

**Q1: What does SIFT do?**
SIFT detects and describes local image features that are invariant to scale and rotation. It finds keypoints at their characteristic scale using Difference of Gaussians, assigns each a dominant orientation, and generates a 128-dimensional descriptor. These descriptors can be matched across images for recognition, stitching, and 3D reconstruction.

**Q2: What are properties of SIFT features?**
- **Scale invariant:** keypoints detected at their natural scale via DoG pyramid
- **Rotation invariant:** descriptor computed relative to dominant gradient orientation
- **Illumination invariant:** descriptor is normalized, reducing sensitivity to brightness changes
- **Partially viewpoint invariant:** local features tolerate moderate viewpoint changes
- **Robust to noise and occlusion** since features are local and sparse

**Q3: What is scale space?**
A continuous multi-scale representation: `L(x, y, sigma) = G(x, y, sigma) * I(x, y)`. The image is convolved with Gaussians of increasing sigma, representing it at progressively coarser scales. This allows detecting features at the scale where they are most prominent.

**Q4: What is DoG?**
Difference of Gaussians: `D(x, y, sigma) = L(x, y, k*sigma) - L(x, y, sigma)`. It approximates the scale-normalized Laplacian of Gaussian. Extrema (local maxima/minima) in DoG across both space and scale are candidate keypoints. Computed by simply subtracting adjacent Gaussian-blurred images -- very efficient.

**Q5: How is the scale-space pyramid organized?**
- Divided into **octaves** (each octave doubles the effective scale; image is halved in size)
- Within each octave, multiple Gaussian blurs at incrementally increasing sigma (typically 3-5 per octave)
- DoG images computed between adjacent Gaussian images within each octave
- Keypoints found by comparing each DoG pixel to its 26 neighbors (8 same scale + 9 above + 9 below)

**Q6: Why 128 dimensions in the SIFT descriptor?**
The region around each keypoint is divided into a 4x4 grid of cells. In each cell, an 8-bin gradient orientation histogram is computed. 4 x 4 x 8 = **128 dimensions**. This balances distinctiveness (enough bins to discriminate) with efficiency (not so many that matching is slow or overfitting occurs).

# RANSAC (Random Sample Consensus)

### 1. What is RANSAC?
**Purpose:**
- Robust parameter estimation in presence of outliers
- Fit models (lines, planes, transformations) to noisy data
- Ignore outliers automatically

**Core idea:**
- Randomly sample minimum points needed for model
- Fit model to sample
- Count how many points agree (inliers)
- Repeat many times
- Keep model with most inliers

### 2. Algorithm Overview

**Input:**
- Data points (some are outliers)
- Model to fit
- Distance threshold (inlier criterion)
- Number of iterations

**Output:**
- Best model parameters
- Set of inliers

**Basic Algorithm:**
```
best_model = null
best_inliers = 0

For k iterations:
    1. Randomly select minimum sample (e.g., 2 points for line)
    2. Fit model to sample
    3. Count inliers (points within threshold distance)
    4. If inliers > best_inliers:
         best_inliers = inliers
         best_model = model

Return best_model
```

### 3. Key Components

**Minimum sample size:**
- Line (2D): 2 points
- Plane (3D): 3 points
- Homography: 4 point pairs
- Fundamental matrix: 8 point pairs

**Distance threshold:**
- Defines what counts as inlier
- Too small -> few inliers
- Too large -> outliers accepted
- Typically: 1-3 pixels for image data

**Number of iterations:**
- More iterations -> higher probability of success
- Depends on outlier ratio
- Formula: k = log(1-p) / log(1-w^n)
  - p = desired probability of success (e.g., 0.99)
  - w = estimated inlier ratio (e.g., 0.5)
  - n = minimum sample size

### 4. Outlier Rejection Strategy

**Why traditional least squares fails:**
```
Least squares: minimize SUM(error^2)
Problem: Outliers have huge squared errors -> dominate fit
```

**RANSAC approach:**
```
Ignore outliers completely
Only fit to inliers
Robust to 50%+ outliers
```

### RANSAC vs Least Squares

| Property | Least Squares | RANSAC |
|----------|---------------|--------|
| Outlier handling | **Poor** (outliers dominate) | **Excellent** (ignores outliers) |
| Deterministic | Yes | No (random sampling) |
| >50% outliers | Fails | **Works** |
| Speed | Fast (one pass) | Slower (many iterations) |

### Worked Example
```
w = 0.7 (70% inliers), p = 0.99, n = 2 (line fitting)
k = log(1-0.99) / log(1-0.7^2)
  = log(0.01) / log(0.51)
  = -4.605 / -0.673
  = 7 iterations
```

---

## Answers to Common Exam Questions

**Q1: What does RANSAC do?**
RANSAC performs robust parameter estimation (e.g., fitting a line or homography) when the data contains outliers. Unlike least squares which lets outliers dominate the fit, RANSAC randomly samples minimal subsets, fits a model to each, and keeps the model that has the most inlier support.

**Q2: What is the basic algorithm?**
1. Randomly select the minimum number of points needed to fit the model (e.g., 2 for a line)
2. Fit the model to those points
3. Count inliers: all data points within a distance threshold of the model
4. If this inlier count is the best so far, save the model
5. Repeat for k iterations
6. (Optional) Refit the best model using all its inliers for improved accuracy

**Q3: How many points to fit a line?**
Minimum **2 points** define a line. For other models: 3 points for a plane, 4 point pairs for a homography, 8 point pairs for a fundamental matrix.

**Q4: What determines the number of iterations?**
`k = log(1 - p) / log(1 - w^n)` where p = desired success probability (e.g., 0.99), w = inlier ratio, n = minimum sample size. Higher outlier ratio (lower w) or larger sample size (n) requires exponentially more iterations. Example: w=0.5, n=2, p=0.99 gives k = log(0.01)/log(0.75) = 16 iterations.

**Q5: Why is RANSAC robust?**
Outliers never influence the model fit because models are fit only to small random samples. An outlier can only affect a trial if it is selected in the random sample, and such trials will have few inliers and be discarded. RANSAC can handle over 50% outliers.

**Q6: What is the consensus set?**
The set of all data points that are inliers for a given model -- i.e., points whose distance to the model is below the threshold. The model with the largest consensus set is selected as the best fit.

# Formula Cheat Sheet

## Perspective Projection
```
x = f * (X / Z)
y = f * (Y / Z)
```

## Convolution
```
Output(i,j) = SUM SUM Image(i+m, j+n) x Kernel(m,n)
```

## Gradient Magnitude & Direction
```
|G| = sqrt(Gx^2 + Gy^2)
theta = atan2(Gy, Gx)
```

## Sobel Masks
```
Gx = [-1  0  1]     Gy = [-1 -2 -1]
     [-2  0  2]          [ 0  0  0]
     [-1  0  1]          [ 1  2  1]
```

## Hough Transform (rho-theta form)
```
rho = x*cos(theta) + y*sin(theta)
```

## Harris Corner Response
```
R = det(M) - k*(trace(M))^2
R = lambda1*lambda2 - k*(lambda1 + lambda2)^2

M = [Ix^2    Ix*Iy]
    [Ix*Iy   Iy^2 ]
```

## RANSAC Iterations
```
k = log(1-p) / log(1-w^n)
p = desired success probability
w = inlier ratio
n = minimum sample size
```

## Gaussian Filter
```
G(x,y) = (1 / (2*pi*sigma^2)) x exp(-(x^2 + y^2) / (2*sigma^2))
```

## Second Moment Matrix
```
M = SUM w(x,y) [Ix^2    Ix*Iy]
    x,y        [Ix*Iy   Iy^2 ]
```

## Patch Comparison
```
SSD = SUM (I1(x,y) - I2(x,y))^2          (lower = more similar)
NCC = SUM(I1*I2) / sqrt(SUM(I1^2)*SUM(I2^2))  (higher = more similar)
```

# Algorithm Cheat Sheet

## Canny Edge Detection
1. Gaussian smoothing
2. Gradient calculation (Sobel)
3. Non-maxima suppression
4. Double thresholding
5. Edge tracking by hysteresis

## Harris Corner Detection
1. Compute image gradients (Ix, Iy)
2. Compute products (Ix^2, Iy^2, Ix*Iy)
3. Apply Gaussian weighting
4. Compute R = det(M) - k*(trace(M))^2
5. Threshold and NMS

## Hough Transform
1. Edge detection
2. For each edge pixel (x,y):
   - For theta = 0 to 180:
     - rho = x*cos(theta) + y*sin(theta)
     - Vote in accumulator[rho, theta]
3. Find peaks in accumulator
4. Extract lines from peaks

## RANSAC
1. Randomly sample minimum points
2. Fit model to sample
3. Count inliers (dist < threshold)
4. If inliers > best: save model
5. Repeat k times
6. Return best model

## Non-Maxima Suppression
1. For each pixel with gradient M, theta:
2. Interpolate neighbors along theta
3. If M > both neighbors: keep
4. Else: suppress (set to 0)

## SIFT (High-Level)
1. Scale-space extrema detection (DoG)
2. Keypoint localization & refinement
3. Orientation assignment
4. Descriptor generation (128D)

## Gaussian Pyramid
```
For each level k:
  1. Smooth with Gaussian
  2. Downsample by 2
  Size_k = Size_0 / 2^k
```

## Laplacian Pyramid
```
For each level k:
  L_k = G_k - expand(G_{k+1})
```
