# Image Formation and Representation - Overview

## Key Concepts to Master

### 1. Mathematical Representation of Digital Images
- **Digital image:** 2D matrix I(row, col) or I(x, y)
- **Coordinate system:**
  - Matrix notation: I(row, col) - row increases downward
  - Image notation: I(x, y) - x is horizontal, y is vertical
  - MATLAB uses 1-based indexing: I(1,1) is top-left

### 2. Sampling and Quantization
**Sampling:** Converting continuous spatial domain → discrete pixels
- Controls spatial resolution
- Higher sampling = more pixels = finer detail
- Aliasing occurs when sampling rate too low

**Quantization:** Converting continuous intensity → discrete levels
- Controls intensity resolution (bit depth)
- 8-bit = 256 levels (0-255)
- Lower quantization = posterization effect

### 3. Pinhole Camera Model
**Geometry:**
```
3D World Point (X, Y, Z)
      ↓
  Pinhole (optical center)
      ↓
2D Image Point (x, y)
```

**Key properties:**
- No lens distortion
- Infinite depth of field
- Inverted image
- Smaller aperture → sharper but darker

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
- Cartesian (x, y) → Homogeneous [x, y, 1]ᵀ
- Homogeneous [x, y, w]ᵀ → Cartesian (x/w, y/w)

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

## MATLAB Quick Reference

```matlab
% Load and display image
img = imread('image.jpg');
imshow(img);

% Get image size
[rows, cols, channels] = size(img);

% Access pixel value
pixel_value = img(row, col);  % Grayscale
pixel_rgb = img(row, col, :);  % Color

% Convert to grayscale
gray_img = rgb2gray(img);

% Image type conversion
img_double = im2double(img);  % [0, 1]
img_uint8 = im2uint8(img);     % [0, 255]

% Perspective projection example
X = 10; Y = 5; Z = 20; f = 50;
x = f * (X / Z);
y = f * (Y / Z);
```

---

## Study Checklist
- [x] Can convert between coordinate systems
- [x] Can perform perspective projection calculation by hand
- [x] Understand homogeneous coordinates representation
- [x] Know projection matrix form
- [x] Can explain effects of sampling and quantization
- [x] Can describe effects of perspective projection

## Practice Problems
1. Given 3D point (6, 3, 10) and f=50mm, find image coordinates
2. Convert between matrix and image coordinate systems
3. Explain why parallel railroad tracks appear to converge

## Answers
1. x = 50 * (6/10) = **30mm**, y = 50 * (3/10) = **15mm**. Image coordinates: **(30, 15) mm**.
2. Matrix notation I(row, col) maps to image notation I(x, y) by swapping axes: row corresponds to y (vertical, increasing downward) and col corresponds to x (horizontal, increasing rightward). So matrix I(r, c) = image I(c, r). Example: matrix position (3, 5) = image coordinates (x=5, y=3).
3. Perspective projection maps 3D points via x = f(X/Z). Railroad tracks have constant separation in X but increasing Z with distance. As Z grows, the projected x-coordinates f(X/Z) for each rail get closer together, converging toward x = 0 at Z = infinity. That convergence point is the **vanishing point**.

## Related Topics
- Lecture 2: Image Formation
- Project 1: Image basics

# Image Filtering - Overview

## Key Concepts to Master

### 1. Linear Filtering (Convolution)
**CRITICAL: Must be able to do by hand!**

**Process:**
1. Place filter mask over image region
2. Multiply corresponding elements
3. Sum all products
4. Result is output pixel value

**Example: 3×3 Sobel filter**
```
Filter Gx:          Image patch:
[-1  0  1]          [10  20  30]
[-2  0  2]    *     [15  25  35]
[-1  0  1]          [20  30  40]

Result = (-1×10)+(0×20)+(1×30)+(-2×15)+(0×25)+(2×35)+(-1×20)+(0×30)+(1×40)
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
1. Extract neighborhood (e.g., 3×3)
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
| **Gaussian** | Additive, normally distributed | Bell curve, mean μ, std σ |
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

3×3 Mean filter:
```
[1  1  1]
[1  1  1]  × (1/9)
[1  1  1]
```

**Properties:**
- Simple averaging
- Reduces Gaussian noise
- Blurs edges significantly

### 6. Gaussian Filter
**Weights based on 2D Gaussian function:**

```
G(x,y) = (1 / (2πσ²)) × exp(-(x² + y²) / (2σ²))
```

**Example 3×3 (σ=1, approximated):**
```
[1  2  1]
[2  4  2]  × (1/16)
[1  2  1]
```

**Properties:**
- Weighted smoothing (center pixel weighted more)
- σ controls amount of smoothing
- Separable: 2D filter = 1D horizontal × 1D vertical
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

## MATLAB Quick Reference

```matlab
% Linear filtering (convolution)
h = ones(3,3) / 9;  % Mean filter
filtered = imfilter(img, h);

% Median filtering
filtered = medfilt2(img, [3 3]);

% Create Gaussian filter
h = fspecial('gaussian', [5 5], sigma);
filtered = imfilter(img, h);

% Add noise
noisy = imnoise(img, 'gaussian', 0, 0.01);
noisy = imnoise(img, 'salt & pepper', 0.05);

% Correlation (similar to convolution)
result = imfilter(img, h, 'corr');
```

---

## Hand Calculation Practice

**Problem 1: Mean filtering**
```
Image patch:        3×3 Mean filter:
[10  20  30]       [1  1  1]
[40  50  60]   *   [1  1  1]  × (1/9)
[70  80  90]       [1  1  1]

Result = (10+20+30+40+50+60+70+80+90) / 9 = 50
```

**Problem 2: Gaussian approximation**
```
Image patch:        Gaussian filter:
[10  20  30]       [1  2  1]
[40  50  60]   *   [2  4  2]  × (1/16)
[70  80  90]       [1  2  1]

Result = (1×10+2×20+1×30+2×40+4×50+2×60+1×70+2×80+1×90) / 16
       = (10+40+30+80+200+120+70+160+90) / 16
       = 800 / 16 = 50
```

---

## Study Checklist
- [x] Can perform 3×3 convolution by hand
- [x] Can perform median filtering by hand
- [x] Know construction of mean filter
- [x] Know construction of Gaussian filter
- [x] Can match filter type to noise type
- [x] Understand why median preserves edges
- [x] Know properties of each filter

## Common Exam Questions
1. "Apply this filter to this image patch" → Show work!
2. "Which filter for salt & pepper noise?" → Median
3. "Compare mean vs Gaussian filter" → Weighted vs uniform
4. "Why does median preserve edges?" → Non-linear, removes outliers

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

## Related Topics
- Lecture 3: Image Filtering
- Project 2: Image Filtering
- Edge Detection (uses filtering)

# Edge Detection - Overview

## Key Concepts to Master

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
|G| = √(Gx² + Gy²)
```

**Gradient direction:**
```
θ = atan2(Gy, Gx)  or  θ = arctan(Gy / Gx)
```

**Hand calculation example:**
```
Image patch:
[50  50  50]
[50  50  100]
[50  50  100]

Gx = (-1×50)+(0×50)+(1×50)+(-2×50)+(0×50)+(2×100)+(-1×50)+(0×50)+(1×100)
   = -50 + 0 + 50 - 100 + 0 + 200 - 50 + 0 + 100 = 150

Gy = (-1×50)+(-2×50)+(-1×50)+(0×50)+(0×50)+(0×100)+(1×50)+(2×50)+(1×100)
   = -50 - 100 - 50 + 0 + 0 + 0 + 50 + 100 + 100 = 50

|G| = √(150² + 50²) = √(22500 + 2500) = √25000 ≈ 158
θ = atan2(50, 150) ≈ 18.4°
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
- Sobel: Center row/column weighted more (2×)
- Sobel typically better in practice

### 4. Laplacian Operator
**Second-order derivative detector:**

Common 3×3 Laplacian masks:
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
1. Smooth with Gaussian: G(x,y) = exp(-(x² + y²)/(2σ²))
2. Apply Laplacian: ∇²

**Combined operator:**
```
LoG = ∇²G = (x² + y² - 2σ²) / σ⁴ × exp(-(x² + y²)/(2σ²))
```

**Properties:**
- Reduces noise sensitivity
- σ controls scale of edges detected
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
- **σ (sigma):** Gaussian smoothing strength
  - Larger σ → smoother, fewer edges
  - Smaller σ → more edges, more noise
- **High threshold:** Strong edges (definite edges)
- **Low threshold:** Weak edges (candidate edges)
- **Threshold ratio:** Typically high/low = 2:1 or 3:1

**How many parameters?** 
**3 parameters:** σ, T_high, T_low

### 7. Non-Maxima Suppression (NMS)
**CRITICAL: Understand this process!**
**Purpose:** Thin thick edges to 1-pixel width

**Algorithm:**
1. For each pixel with gradient magnitude M and direction θ:
2. Interpolate gradient values of two neighbors along θ direction
3. If M is greater than both neighbors → keep
4. Otherwise → suppress (set to 0)

**Example:**
```
Gradient magnitude:   Gradient direction:
[10  20  30]          [→  →  →]
[15  40  25]          [→  →  →]
[10  20  30]          [→  →  →]

Center pixel (40):
- Check left neighbor (15) and right neighbor (25)
- 40 > 15 and 40 > 25 → KEEP
- Result: Center stays 40, others may be suppressed
```

**4 quantized directions:**
- 0° (→): Check left and right
- 45° (↗): Check diagonal neighbors
- 90° (↑): Check up and down
- 135° (↖): Check other diagonal

---

## MATLAB Quick Reference

```matlab
% Sobel edge detection
[Gx, Gy] = imgradientxy(img, 'sobel');
[Gmag, Gdir] = imgradient(Gx, Gy);

% Or use edge function
edges = edge(img, 'sobel', threshold);

% Prewitt
[Gx, Gy] = imgradientxy(img, 'prewitt');

% Laplacian
h = fspecial('laplacian');
edges = imfilter(img, h);

% LoG
h = fspecial('log', hsize, sigma);
edges = imfilter(img, h);

% Canny (most common)
edges = edge(img, 'canny', [low_thresh high_thresh], sigma);
% Example: edge(img, 'canny', [0.1 0.3], 1.5);
```

---

## Study Checklist
- [ ] Sobel masks memorized
- [ ] Can apply Sobel/Prewitt by hand
- [ ] Can calculate gradient magnitude and direction
- [ ] Understand Laplacian operation
- [ ] Know LoG advantages over Laplacian
- [ ] Know all 5 Canny steps
- [ ] Understand effect of each Canny parameter
- [ ] Can perform non-maxima suppression
- [ ] Know difference between 1st and 2nd order methods

## Common Exam Problems
1. **Apply Sobel to image patch** → Show all calculations
2. **Calculate gradient magnitude and direction**
3. **How many Canny parameters and what do they do?** → 3: σ, T_high, T_low
4. **Explain non-maxima suppression** → Thin edges by comparing along gradient direction
5. **Compare first vs second-order methods**

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

## Related Topics
- Lecture 4: Edge Detection
- Project 2: Edge Detection
- Links to filtering (preprocessing)

﻿# Hough Transform - Overview

## Key Concepts to Master

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
**Image Space → Parameter Space Transformation**

**For lines:**
- Image space: Points (x, y)
- Parameter space: Line parameters (m, b) or (ρ, θ)

**Key insight:**
- One point in image space → Curve in parameter space
- Collinear points → Curves intersect at one point in parameter space
- Intersection point = line parameters

### 3. Line Representations

#### Slope-Intercept Form
```
y = mx + b
```
- m = slope
- b = y-intercept

**Problems:**
- Cannot represent vertical lines (m = ∞)
- Unbounded parameter space for steep lines
- Not used in Hough Transform

#### Normal (ρ-θ) Form ★ PREFERRED
```
ρ = x·cos(θ) + y·sin(θ)
```

**Parameters:**
- ρ (rho): Perpendicular distance from origin to line
- θ (theta): Angle of perpendicular (0° to 180°)

**Advantages:**
- Can represent ALL lines (including vertical)
- Bounded parameter space
- ρ ∈ [-D, D] where D = diagonal of image
- θ ∈ [0°, 180°]

**Conversion between forms:**
```
Given: y = mx + b
ρ = b / √(1 + m²)
θ = arctan(-1/m)
```

### 4. Hough Transform Algorithm

**For each edge pixel (xi, yi):**

```
For θ = 0° to 180° (in small increments):
    ρ = xi·cos(θ) + yi·sin(θ)
    Accumulator[ρ, θ] += 1  // Vote for this line
```

**After processing all pixels:**
```
Find peaks in accumulator array
Each peak (ρ*, θ*) represents a detected line
```

### 5. Accumulator Array
**2D array in parameter space:**
- Dimensions: [ρ_bins × θ_bins]
- Each cell = **accumulator cell** = one bin in (ρ, θ) space
- Value = number of edge pixels voting for that line

**Example dimensions:**
- θ: 180 bins (1° resolution)
- ρ: 2D bins where D = image diagonal

### 6. Parameter Space Mapping

**Key transformations:**

**One point (x₀, y₀) →**
```
ρ = x₀·cos(θ) + y₀·sin(θ)
```
- For varying θ, traces sinusoidal curve in (ρ, θ) space

**Two collinear points →**
- Both curves intersect at same (ρ, θ)
- Intersection = line containing both points

**Three collinear points →**
- All three curves intersect at same (ρ, θ)
- Strong peak in accumulator!

---

## Hand Calculation Examples

### Example 1: Simple point
**Given:** Point (2, 3), find ρ for θ = 0°, 45°, 90°

**θ = 0°:**
```
ρ = 2·cos(0°) + 3·sin(0°)
ρ = 2·(1) + 3·(0) = 2
```

**θ = 45°:**
```
ρ = 2·cos(45°) + 3·sin(45°)
ρ = 2·(√2/2) + 3·(√2/2)
ρ = √2 + 1.5√2 = 2.5√2 ≈ 3.54
```

**θ = 90°:**
```
ρ = 2·cos(90°) + 3·sin(90°)
ρ = 2·(0) + 3·(1) = 3
```

### Example 2: Collinear points
**Given:** Points (0,0), (1,1), (2,2) - clearly on line y=x

**Expected line parameters:**
- Line y = x has slope m = 1
- In normal form: 45° line through origin
- Should get θ ≈ 45°, ρ ≈ 0

**Check point (1,1) at θ = 45°:**
```
ρ = 1·cos(45°) + 1·sin(45°)
ρ = √2/2 + √2/2 = √2 ≈ 1.41
```

*Note: ρ ≠ 0 because our origin is at (0,0), not at perpendicular*

### Example 3: Vertical line
**Given:** Points (5,0), (5,1), (5,2) - vertical line at x=5

**Expected:** θ = 0°, ρ = 5

**Check point (5,1) at θ = 0°:**
```
ρ = 5·cos(0°) + 1·sin(0°)
ρ = 5·(1) + 1·(0) = 5  +
```

### Example 4: Horizontal line
**Given:** Points (0,3), (1,3), (2,3) - horizontal line at y=3

**Expected:** θ = 90°, ρ = 3

**Check point (1,3) at θ = 90°:**
```
ρ = 1·cos(90°) + 3·sin(90°)
ρ = 1·(0) + 3·(1) = 3  +
```

---

## Visualization

### Image Space vs Parameter Space

```
Image Space (x, y):          Parameter Space (ρ, θ):
                             
·   ·   ·                    \    /
    ·                         \  /
        ·                      \/  ← intersection = line
            ·                  /\
                ·             /  \
                              
Single line                  Sinusoids from each point
                             intersect at line params
```

---

## MATLAB Quick Reference

```matlab
% Basic Hough Transform workflow

% 1. Get edge image
edges = edge(img, 'canny');

% 2. Compute Hough Transform
[H, theta, rho] = hough(edges);
% H = accumulator array
% theta = θ values (degrees)
% rho = ρ values (pixels)

% 3. Visualize accumulator
imshow(H, [], 'XData', theta, 'YData', rho);
xlabel('\theta (degrees)');
ylabel('\rho (pixels)');

% 4. Find peaks
peaks = houghpeaks(H, numpeaks);

% 5. Extract lines
lines = houghlines(edges, theta, rho, peaks, ...
    'FillGap', 20, 'MinLength', 30);

% 6. Display detected lines
imshow(img); hold on;
for k = 1:length(lines)
    xy = [lines(k).point1; lines(k).point2];
    plot(xy(:,1), xy(:,2), 'LineWidth', 2, 'Color', 'green');
end
```

---

## Study Checklist
- [ ] Understand objective: detect lines robustly
- [ ] Know both line representations
- [ ] Prefer ρ-θ form (can handle all lines)
- [ ] Can calculate ρ for given (x, y, θ)
- [ ] Understand voting mechanism
- [ ] Know what accumulator cell represents
- [ ] Can explain image→parameter space mapping
- [ ] Know why collinear points intersect
- [ ] Can sketch parameter space curves

## Common Exam Questions

**Q1:** What is the objective of Hough Transform?
- Detect parametric shapes (lines) robustly despite noise/gaps

**Q2:** Why use ρ-θ instead of slope-intercept?
- Can represent vertical lines
- Bounded parameter space

**Q3:** What is an accumulator cell?
- Single bin in parameter space
- Counts votes for that particular line

**Q4:** Given point (x,y) and angle θ, find ρ
- ρ = x·cos(θ) + y·sin(θ)

**Q5:** Why do collinear points create a peak?
- Each point creates sinusoid in parameter space
- All sinusoids intersect at line's (ρ, θ)

## Practice Problems

1. Calculate ρ for point (3, 4) at θ = 0°, 30°, 60°, 90°
2. Three points (0,1), (1,2), (2,3) are collinear. Find their line parameters.
3. Convert line y = 2x + 3 to ρ-θ form
4. Sketch parameter space curves for two collinear points

## Answers

### Common Exam Questions

**Q1: What is the objective of Hough Transform?**
Detect parametric shapes (especially lines) in images by transforming edge pixels into a parameter space and finding peaks. It is robust to noise, gaps in edges, and partial occlusions because collinear points independently vote for the same line parameters.

**Q2: Why use rho-theta instead of slope-intercept?**
Slope-intercept `y = mx + b` cannot represent vertical lines (m = infinity) and has unbounded parameter space. The normal form `rho = x*cos(theta) + y*sin(theta)` handles all line orientations with bounded parameters: `rho in [-D, D]`, `theta in [0, 180)`.

**Q3: What is an accumulator cell?**
One bin in the discretized (rho, theta) parameter space. Its value counts how many edge pixels voted for that particular line. A high count (peak) means many edge pixels are collinear along that line.

**Q4: Given point (x,y) and angle theta, find rho**
Direct substitution: `rho = x*cos(theta) + y*sin(theta)`.

**Q5: Why do collinear points create a peak?**
Each edge pixel (x,y) traces a sinusoidal curve in (rho, theta) space. If multiple pixels lie on the same line, their sinusoids all pass through the same (rho, theta) point, creating a peak in the accumulator.

### Practice Problems

**1. Calculate rho for point (3, 4) at theta = 0, 30, 60, 90 degrees**
- `theta=0: rho = 3*cos(0) + 4*sin(0) = 3*1 + 4*0 = 3`
- `theta=30: rho = 3*cos(30) + 4*sin(30) = 3*(0.866) + 4*(0.5) = 2.598 + 2.0 = 4.598`
- `theta=60: rho = 3*cos(60) + 4*sin(60) = 3*(0.5) + 4*(0.866) = 1.5 + 3.464 = 4.964`
- `theta=90: rho = 3*cos(90) + 4*sin(90) = 3*0 + 4*1 = 4`

**2. Points (0,1), (1,2), (2,3) are collinear. Find line parameters.**
The line is y = x + 1 (slope 1, intercept 1). To find (rho, theta): all three points should give the same rho at the correct theta.
Using `rho = x*cos(theta) + y*sin(theta)` and checking theta = 135 degrees (perpendicular to slope-1 line):
- `(0,1): rho = 0*cos(135) + 1*sin(135) = 0.707`
- `(1,2): rho = 1*(-0.707) + 2*(0.707) = -0.707 + 1.414 = 0.707`
- `(2,3): rho = 2*(-0.707) + 3*(0.707) = -1.414 + 2.121 = 0.707`
All agree: **theta = 135 degrees, rho = sqrt(2)/2 = 0.707**.

**3. Convert line y = 2x + 3 to rho-theta form**
Using formulas: `rho = b / sqrt(1 + m^2) = 3 / sqrt(1 + 4) = 3/sqrt(5) = 1.342`.
`theta = arctan(-1/m) = arctan(-0.5) = 180 - 26.57 = 153.43 degrees` (adjusting to [0,180) range since the line has positive intercept).

**4. Sketch parameter space curves for two collinear points**
Each point traces a sinusoid `rho = x*cos(theta) + y*sin(theta)` in (rho, theta) space. Two collinear points produce two sinusoids that intersect at exactly one point -- this intersection is the (rho, theta) of the line through both points. The sketch shows two sine-like curves crossing at a single peak location.

## Related Topics
- Lecture 5: Hough Transform
- Edge Detection (preprocessing step)
- Feature Detection (alternative approach)

﻿# Corner Detection - Overview

## Key Concepts to Master

### 1. Why Feature Points (Keypoints)?
**Purpose:**
- **Distinctive:** Easy to localize precisely
- **Repeatable:** Can find same point under different conditions
- **Invariant:** Robust to rotation, scale, illumination changes
- **Sparse:** Efficient computation and matching
- **Informative:** Encode local structure

**Applications:**
- Image matching and alignment
- Object recognition
- 3D reconstruction
- Motion tracking

### 2. What is a Corner?
**Definition:**
- Point where intensity changes significantly in multiple directions
- Intersection of two or more edges
- High gradient in both x and y directions

**Why corners are good features:**
- Unique and easy to localize
- More stable than edges alone
- Provide 2D constraints (edges only 1D)

### 3. Image Patch Comparison
**How to compare two patches?**

**Sum of Squared Differences (SSD):**
```
SSD = Σ(I₁(x,y) - I₂(x,y))²
```
- Lower SSD = more similar
- Simple but sensitive to intensity changes

**Normalized Cross-Correlation (NCC):**
```
NCC = Σ(I₁·I₂) / √(Σ I₁² · Σ I₂²)
```
- Range: [-1, 1], higher = more similar
- Invariant to linear intensity changes

### 4. Error Function E(u,v)
**Measures intensity change when shifting window by (u,v):**

```
E(u,v) = Σ w(x,y) [I(x+u, y+v) - I(x,y)]²
        x,y
```

Where:
- w(x,y) = window function (often Gaussian)
- I(x,y) = image intensity
- (u,v) = shift/displacement

**Taylor approximation:**
```
I(x+u, y+v) ≈ I(x,y) + Iₓ·u + Iᵧ·v
```

**Simplified E(u,v):**
```
E(u,v) ≈ [u v] M [u]
                  [v]
```

### 5. Second Moment Matrix M
**Definition:**
```
M = Σ w(x,y) [Iₓ²    IₓIᵧ]
    x,y      [IₓIᵧ   Iᵧ² ]
```

Where:
- Iₓ = ∂I/∂x (gradient in x direction)
- Iᵧ = ∂I/∂y (gradient in y direction)

**Alternative notation:**
```
M = [A  C]
    [C  B]

Where:
A = Σ Iₓ²
B = Σ Iᵧ²
C = Σ IₓIᵧ
```

### 6. Eigenvalue Interpretation of M
**Eigenvalues λ₁, λ₂ describe local structure:**

| λ₁ | λ₂ | Interpretation |
|----|----|-|
| Small | Small | Flat region (no edges) |
| Large | Small | Edge (gradient in one direction) |
| Large | Large | **Corner** (gradients in multiple directions) |

**Geometric interpretation:**
- M describes ellipse of intensity changes
- Eigenvalues = lengths of ellipse axes
- Eigenvectors = directions of ellipse axes

**Corner condition:**
- Both λ₁ and λ₂ must be large
- Indicates significant intensity variation in all directions

### 7. Harris Corner Response Function R
**Formula:**
```
R = det(M) - k·(trace(M))²
  = λ₁λ₂ - k(λ₁ + λ₂)²
```

Where:
- det(M) = λ₁λ₂ = AB - C²
- trace(M) = λ₁ + λ₂ = A + B
- k = empirical constant (typically 0.04 - 0.06)

**Decision criteria:**
- R > threshold → **Corner**
- R < 0 → Edge
- |R| small → Flat region

**Why this works:**
- Avoids expensive eigenvalue computation
- det(M) large when both eigenvalues large
- trace(M) penalizes if only one eigenvalue large

### 8. Harris Corner Detection Algorithm

**Step 1:** Compute gradients
```matlab
Ix = imfilter(img, [-1 0 1]);
Iy = imfilter(img, [-1; 0; 1]);
```

**Step 2:** Compute products of gradients
```matlab
Ix2 = Ix .^ 2;
Iy2 = Iy .^ 2;
Ixy = Ix .* Iy;
```

**Step 3:** Apply Gaussian weighting
```matlab
g = fspecial('gaussian', [9 9], sigma);
A = imfilter(Ix2, g);
B = imfilter(Iy2, g);
C = imfilter(Ixy, g);
```

**Step 4:** Compute corner response
```matlab
k = 0.04;
R = (A .* B - C.^2) - k * (A + B).^2;
```

**Step 5:** Threshold and non-maxima suppression
```matlab
corners = (R > threshold);
% Apply NMS to get local maxima
```

---

## Hand Calculation Example

**Given image patch (3×3):**
```
[10  10  10]
[10  50  50]
[10  50  50]
```

**Step 1: Compute gradients (using simple differences)**
```
Ix (horizontal):      Iy (vertical):
[ 0   0   0]          [ 0   0   0]
[ 0  40   0]          [ 0  40  40]
[ 0  40   0]          [ 0   0   0]
```

**Step 2: At center pixel (50):**
```
Iₓ² = 40² = 1600
Iᵧ² = 40² = 1600
IₓIᵧ = 40×40 = 1600
```

**Step 3: Second moment matrix (simplified, no Gaussian)**
```
M = [1600  1600]
    [1600  1600]
```

**Step 4: Harris response**
```
det(M) = 1600×1600 - 1600×1600 = 0
trace(M) = 1600 + 1600 = 3200
R = 0 - 0.04×(3200)² = -409600
```

**Conclusion:** R < 0 → This is an **edge**, not a corner!

---

## MATLAB Quick Reference

```matlab
% Harris corner detection (manual)
Ix = imfilter(double(img), [-1 0 1]);
Iy = imfilter(double(img), [-1; 0; 1]);

Ix2 = Ix .^ 2;
Iy2 = Iy .^ 2;
Ixy = Ix .* Iy;

g = fspecial('gaussian', [9 9], 1.5);
A = imfilter(Ix2, g);
B = imfilter(Iy2, g);
C = imfilter(Ixy, g);

k = 0.04;
R = (A .* B - C.^2) - k * (A + B).^2;

threshold = 0.01 * max(R(:));
corners = (R > threshold);

% Built-in function
corners = detectHarrisFeatures(img);
imshow(img); hold on;
plot(corners);
```

---

## Study Checklist
- [ ] Understand why corners are useful features
- [ ] Know how to compare image patches (SSD, NCC)
- [ ] Can explain error function E(u,v)
- [ ] Can compute second moment matrix M
- [ ] Understand eigenvalue interpretation
- [ ] Can compute Harris response R by hand
- [ ] Know Harris algorithm steps
- [ ] Understand role of k parameter

## Common Exam Questions

**Q1:** Why are feature points useful?
- Distinctive, repeatable, invariant, sparse, informative

**Q2:** What does the second moment matrix M represent?
- Local intensity gradient structure
- Eigenvalues indicate edge/corner/flat

**Q3:** How to compute Harris R?
- R = det(M) - k·(trace(M))²
- R = λ₁λ₂ - k(λ₁ + λ₂)²

**Q4:** What do eigenvalues of M tell us?
- Both large → corner
- One large → edge
- Both small → flat

**Q5:** Given gradients Iₓ, Iᵧ at a point, compute M
- M = [Iₓ²  IₓIᵧ]
      [IₓIᵧ Iᵧ²]

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

## Related Topics
- Lecture 6: Corner Detection
- SIFT uses multi-scale corner detection
- Feature matching for image alignment

﻿# Image Pyramids - Overview

## Key Concepts to Master

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
Original image (size: M × N)
```

**Level 1:**
```
1. Apply Gaussian filter (smooth)
2. Subsample by factor of 2 (size: M/2 × N/2)
```

**Level k:**
```
Size: M/2^k × N/2^k
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
Level 0: [Original 512×512]
           ↓ (smooth + downsample)
Level 1: [256×256]
           ↓ (smooth + downsample)
Level 2: [128×128]
```

### Laplacian Pyramid
```
L0 = G0 - expand(G1)    [512×512 detail]
L1 = G1 - expand(G2)    [256×256 detail]
L2 = G2                  [128×128 residual]
```

---

## MATLAB Quick Reference

```matlab
% Create Gaussian pyramid
impyramid(img, 'reduce');  % One level down
impyramid(img, 'expand');  % One level up (upsample)

% Manual Gaussian pyramid
function pyr = gaussian_pyramid(img, levels)
    pyr{1} = img;
    for i = 2:levels
        g = fspecial('gaussian', [5 5], 1);
        smoothed = imfilter(pyr{i-1}, g);
        pyr{i} = smoothed(1:2:end, 1:2:end);  % Downsample
    end
end

% Laplacian pyramid
function lpyr = laplacian_pyramid(gpyr)
    for i = 1:length(gpyr)-1
        expanded = imresize(gpyr{i+1}, size(gpyr{i}));
        lpyr{i} = gpyr{i} - expanded;
    end
    lpyr{end} = gpyr{end};  % Residual
end

% Reconstruct from Laplacian
function img = reconstruct(lpyr)
    img = lpyr{end};
    for i = length(lpyr)-1:-1:1
        img = imresize(img, size(lpyr{i}));
        img = img + lpyr{i};
    end
end
```

---

## Study Checklist
- [ ] Understand purpose of image pyramids
- [ ] Know Gaussian pyramid construction
- [ ] Know Laplacian pyramid construction
- [ ] Can explain difference between them
- [ ] Understand expand/downsample operations
- [ ] Know reconstruction process
- [ ] Understand multi-scale representation concept

## Common Exam Questions

**Q1:** How is Gaussian pyramid constructed?
- Smooth with Gaussian, then downsample by 2

**Q2:** How is Laplacian pyramid different?
- Stores differences (details) between levels
- Can reconstruct original image

**Q3:** What is the size at level k?
- Original: M × N
- Level k: M/2^k × N/2^k

**Q4:** Why use image pyramids?
- Multi-scale feature detection
- Handle different object sizes
- Efficient coarse-to-fine processing

**Q5:** Can you reconstruct from Gaussian pyramid?
- No (information lost in smoothing)
- Yes from Laplacian pyramid

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

## Related Topics
- Lecture 7: Image Pyramids
- SIFT uses scale-space (similar concept)
- Multi-scale edge detection

﻿# SIFT (Scale-Invariant Feature Transform) - Overview

## Key Concepts to Master

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
- + **Scale invariant:** Detects features at their characteristic scale
- + **Rotation invariant:** Orientation normalized
- + **Illumination invariant:** Descriptor normalized
- + **Partial viewpoint invariance**

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
- Continuous function of scale σ
- Represents image at multiple scales simultaneously
- Detects features at their "natural" scale

**Scale-space representation:**
```
L(x, y, σ) = G(x, y, σ) * I(x, y)
```

Where:
- G(x, y, σ) = Gaussian kernel with scale σ
- I(x, y) = input image
- σ = scale parameter

**Difference of Gaussians (DoG):**
```
D(x, y, σ) = L(x, y, kσ) - L(x, y, σ)
```
- Approximates scale-normalized Laplacian
- Used to find scale-space extrema
- k = constant scale factor (typically √2)

### 4. Scale-Space Pyramid Structure
**Octaves:**
- Each octave = doubling of scale (σ)
- Image progressively downsampled
- Typically 4-5 octaves

**Scales per octave:**
- Multiple Gaussian blurs at different σ
- Typically 3-5 scales per octave
- DoG computed between adjacent scales

**Structure:**
```
Octave 1 (original size):
  G(σ), G(kσ), G(k²σ), ...
  DoG₁, DoG₂, DoG₃, ...

Octave 2 (half size):
  G(2σ), G(2kσ), G(2k²σ), ...
  DoG₁, DoG₂, DoG₃, ...
```

### 5. Keypoint Detection
**Finding extrema in DoG:**
- Compare pixel to 26 neighbors
  - 8 in same scale
  - 9 in scale above
  - 9 in scale below
- If pixel is local maximum or minimum → candidate keypoint

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
2. Build orientation histogram (36 bins, 10° each)
3. Peak in histogram = dominant orientation
4. Multiple peaks → multiple keypoints with different orientations

### 7. SIFT Descriptor
**128-dimensional vector:**
- 4×4 grid of cells around keypoint
- 8-bin orientation histogram per cell
- 4×4×8 = 128 dimensions

**Properties:**
- Gradient-based (captures shape)
- Normalized (illumination invariant)
- Thresholded (reduce non-linear effects)

**Matching:**
- Compare descriptors using Euclidean distance
- Typically use ratio test: dist(best)/dist(second-best) < threshold

---

## Conceptual Understanding

### Why Scale Space?
**Problem:** Features appear at different scales
- Small σ → detects fine details
- Large σ → detects coarse structures

**Solution:** Search for features across all scales
- True scale-invariant detection

### Why DoG?
**Efficient approximation:**
- DoG ≈ scale-normalized Laplacian
- Much faster to compute
- Good for finding blobs

### Why 128 dimensions?
**Balance:**
- Distinctive enough (high discrimination)
- Not too large (efficient matching)
- 4×4 spatial bins captures local structure
- 8 orientation bins captures gradient distribution

---

## MATLAB Quick Reference

```matlab
% SIFT detection (using VLFeat or Computer Vision Toolbox)

% Detect SIFT features
points = detectSIFTFeatures(img);

% Extract SIFT descriptors
[features, valid_points] = extractFeatures(img, points);

% Match features between two images
indexPairs = matchFeatures(features1, features2);

% Visualize
figure;
imshow(img); hold on;
plot(points.selectStrongest(50));

% Manual scale-space (conceptual)
sigmas = [1.6, 2.0, 2.5, 3.2, 4.0];
for i = 1:length(sigmas)
    g = fspecial('gaussian', [21 21], sigmas(i));
    L{i} = imfilter(img, g);
end

% DoG
for i = 1:length(sigmas)-1
    DoG{i} = L{i+1} - L{i};
end
```

---

## Study Checklist
- [ ] Know what SIFT does (feature detection + description)
- [ ] Understand SIFT invariance properties
- [ ] Know concept of scale space
- [ ] Understand DoG (Difference of Gaussians)
- [ ] Know scale-space pyramid structure (octaves)
- [ ] Understand keypoint detection in DoG
- [ ] Know purpose of orientation assignment
- [ ] Understand SIFT descriptor (128D)

## Common Exam Questions

**Q1:** What does SIFT do?
- Detects and describes local features
- Scale and rotation invariant

**Q2:** What are properties of SIFT features?
- Scale invariant, rotation invariant, illumination invariant

**Q3:** What is scale space?
- Multi-scale representation: L(x, y, σ) = G(σ) * I
- Allows detection at characteristic scale

**Q4:** What is DoG?
- Difference of Gaussians: D = G(kσ) - G(σ)
- Approximates Laplacian
- Used for keypoint detection

**Q5:** How is scale-space pyramid organized?
- Multiple octaves (doubling scale)
- Multiple scales per octave
- DoG between adjacent scales

**Q6:** Why 128 dimensions in SIFT descriptor?
- 4×4 spatial grid × 8 orientation bins
- Balance between distinctiveness and efficiency

## Key Advantages Over Harris
- Scale invariant (Harris is not)
- Includes descriptor (Harris just detects)
- More robust to transformations

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

## Related Topics
- Lecture 8: SIFT
- Image Pyramids (similar multi-scale concept)
- Corner Detection (Harris - single scale version)
- Feature Matching

﻿# RANSAC (Random Sample Consensus) - Overview

## Key Concepts to Master

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
- Too small → few inliers
- Too large → outliers accepted
- Typically: 1-3 pixels for image data

**Number of iterations:**
- More iterations → higher probability of success
- Depends on outlier ratio
- Formula: k = log(1-p) / log(1-w^n)
  - p = desired probability of success (e.g., 0.99)
  - w = estimated inlier ratio (e.g., 0.5)
  - n = minimum sample size

### 4. Outlier Rejection Strategy

**Why traditional least squares fails:**
```
Least squares: minimize Σ(error²)
Problem: Outliers have huge squared errors → dominate fit
```

**RANSAC approach:**
```
Ignore outliers completely
Only fit to inliers
Robust to 50%+ outliers
```

### 5. Model Fitting Example: Line

**Traditional least squares (sensitive to outliers):**
```
All points influence fit equally
Outliers pull line away from true solution
```

**RANSAC (robust):**
```
Iteration 1: Sample points {A, B}
  → Fit line L₁
  → Count inliers = 10

Iteration 2: Sample points {C, outlier}
  → Fit line L₂  
  → Count inliers = 2

...

Best line from iteration with most inliers
```

---

## Detailed Algorithm Steps

### Step 1: Random Sampling
```
Select n random points (n = minimum for model)
Example for line: randomly pick 2 points
```

### Step 2: Model Fitting
```
Fit model to selected points
For line: y = mx + b computed from 2 points
```

### Step 3: Consensus Set
```
For each data point:
    distance = perpendicular distance to model
    if distance < threshold:
        point is inlier
```

### Step 4: Evaluation
```
Count number of inliers
If count > best_so_far:
    Save this model as best
```

### Step 5: Refinement (optional)
```
Refit model using all inliers from best model
Improves accuracy
```

---

## Example Calculation

**Problem:** Fit line to 10 points, 3 are outliers

**Given:**
- Inlier ratio w = 0.7 (70% inliers)
- Desired success probability p = 0.99
- Minimum sample n = 2

**Calculate iterations needed:**
```
k = log(1-p) / log(1-w^n)
k = log(1-0.99) / log(1-0.7²)
k = log(0.01) / log(0.51)
k = -4.605 / -0.673
k ≈ 7 iterations
```

---

## MATLAB Quick Reference

```matlab
% RANSAC for line fitting
function [best_model, inliers] = ransac_line(points, ...
                                  threshold, k)
    best_inliers = [];
    best_model = [];
    
    for i = 1:k
        % 1. Random sample
        idx = randperm(size(points,1), 2);
        sample = points(idx, :);
        
        % 2. Fit line
        model = fit_line(sample);
        
        % 3. Find inliers
        distances = point_to_line_dist(points, model);
        inliers = find(distances < threshold);
        
        % 4. Evaluate
        if length(inliers) > length(best_inliers)
            best_inliers = inliers;
            best_model = model;
        end
    end
    
    % 5. Refinement
    best_model = fit_line(points(best_inliers, :));
end

% Built-in MATLAB RANSAC (for various models)
[model, inliers] = fitgeotform2d(matchedPoints1, ...
    matchedPoints2, 'affine', 'MaxDistance', threshold);
```

---

## Study Checklist
- [ ] Understand purpose: robust fitting with outliers
- [ ] Know basic RANSAC algorithm
- [ ] Understand minimum sample sizes
- [ ] Can calculate number of iterations needed
- [ ] Know role of distance threshold
- [ ] Understand why it's robust
- [ ] Can explain difference from least squares

## Common Exam Questions

**Q1:** What does RANSAC do?
- Robust parameter estimation ignoring outliers

**Q2:** What is the basic algorithm?
1. Random sample minimum points
2. Fit model
3. Count inliers
4. Repeat, keep best

**Q3:** How many points to fit a line?
- Minimum 2 points

**Q4:** What determines number of iterations?
- Outlier ratio
- Desired success probability
- k = log(1-p) / log(1-w^n)

**Q5:** Why is RANSAC robust?
- Outliers don't influence model fit
- Only uses inliers
- Can handle 50%+ outliers

**Q6:** What is consensus set?
- Set of inliers for a model
- Points within threshold distance

## Advantages
- Robust to high outlier ratios (>50%)
- General purpose (works for any model)
- Simple to implement

## Disadvantages
- Non-deterministic (random sampling)
- Requires threshold parameter
- Computational cost (many iterations)

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

## Related Topics
- Lecture 9: RANSAC
- Feature matching (generates data with outliers)
- Hough Transform (alternative robust method)
- Homography estimation

# Formula Cheat Sheet

## Perspective Projection
```
x = f * (X / Z)
y = f * (Y / Z)
```

## Convolution
```
Output(i,j) = Σ Σ Image(i+m, j+n) × Kernel(m,n)
```

## Gradient Magnitude & Direction
```
|G| = √(Gx² + Gy²)
θ = atan2(Gy, Gx)
```

## Sobel Masks
```
Gx = [-1  0  1]     Gy = [-1 -2 -1]
     [-2  0  2]          [ 0  0  0]
     [-1  0  1]          [ 1  2  1]
```

## Hough Transform (ρ-θ form)
```
ρ = x·cos(θ) + y·sin(θ)
```

## Harris Corner Response
```
R = det(M) - k·(trace(M))²
R = λ₁λ₂ - k(λ₁ + λ₂)²

M = [Iₓ²    IₓIᵧ]
    [IₓIᵧ   Iᵧ² ]
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
G(x,y) = (1 / 2πσ²) × exp(-(x² + y²) / (2σ²))
```

## Second Moment Matrix
```
M = Σ w(x,y) [Iₓ²    IₓIᵧ]
    x,y      [IₓIᵧ   Iᵧ² ]
```

# Algorithm Cheat Sheet

## Canny Edge Detection
1. Gaussian smoothing
2. Gradient calculation (Sobel)
3. Non-maxima suppression
4. Double thresholding
5. Edge tracking by hysteresis

## Harris Corner Detection
1. Compute image gradients (Iₓ, Iᵧ)
2. Compute products (Iₓ², Iᵧ², IₓIᵧ)
3. Apply Gaussian weighting
4. Compute R = det(M) - k·(trace(M))²
5. Threshold and NMS

## Hough Transform
1. Edge detection
2. For each edge pixel (x,y):
   - For θ = 0° to 180°:
     - ρ = x·cos(θ) + y·sin(θ)
     - Vote in accumulator[ρ,θ]
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
1. For each pixel with gradient M, θ:
2. Interpolate neighbors along θ
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

