# EE428 Midterm - 2-DAY CRASH COURSE

**Exam Date:** February 11, 2026 (Wednesday) 10:10-11 AM  
**Time Available:** 2 days, ~4 hours per day = 8 hours total  
**Strategy:** Focus on HIGH-YIELD topics that appear on every exam

---

## DAY 1: Core Calculations (4 hours)

### Session 1: Morning (2 hours)
**PRIORITY 1: Edge Detection (Most testable)**
- [ ] **MEMORIZE Sobel masks** (write 10 times):
```
  Gx = [-1  0  1]    Gy = [-1 -2 -1]
       [-2  0  2]         [ 0  0  0]
       [-1  0  1]         [ 1  2  1]
```
- [ ] **Practice:** Apply Sobel to 5 image patches BY HAND
- [ ] **Memorize formulas:**
  - |G| = √(Gx² + Gy²)
  - θ = atan2(Gy, Gx)
- [ ] **List Canny 5 steps** (write from memory 3 times)
- [ ] **Canny parameters:** σ, T_high, T_low (know what each does)
- [ ] **Non-maxima suppression:** Understand the process

**PRIORITY 2: Image Filtering (High-yield calculations)**
- [ ] **Practice:** 5 convolution problems (3×3 filters) BY HAND
- [ ] **Practice:** 3 median filtering problems BY HAND
- [ ] **Memorize filter comparison:**
  - Mean → Gaussian noise, blurs edges
  - Gaussian → Gaussian noise, better than mean
  - Median → Salt & pepper, preserves edges

---

### Session 2: Afternoon (2 hours)
**PRIORITY 3: Hough Transform (Guaranteed on exam)**
- [ ] **Memorize formula:** ρ = x·cos(θ) + y·sin(θ)
- [ ] **Practice:** Calculate ρ for 10 different (x,y,θ) combinations
- [ ] **Know both representations:**
  - Slope-intercept: y = mx + b (can't do vertical)
  - ρ-θ: BETTER, handles all lines
- [ ] **Understand:** Accumulator cell = bin in parameter space
- [ ] **Quick problems:**
  - Given point (3,4), find ρ at θ = 0°, 45°, 90°
  - Why use ρ-θ instead of slope-intercept?

**PRIORITY 4: Harris Corner Detection**
- [ ] **Memorize M matrix:**
```
  M = [Iₓ²    IₓIᵧ]
      [IₓIᵧ   Iᵧ² ]
```
- [ ] **Memorize R formula:** R = det(M) - k·trace(M)²
  - det(M) = Iₓ²·Iᵧ² - (IₓIᵧ)²
  - trace(M) = Iₓ² + Iᵧ²
  - k ≈ 0.04-0.06
- [ ] **Eigenvalue interpretation:**
  - Both large → Corner
  - One large → Edge
  - Both small → Flat
- [ ] **Practice:** 3 Harris R calculations

---

## DAY 2: Concepts + Cheat Sheet (4 hours)

### Session 3: Morning (2 hours)
**PRIORITY 5: Image Formation (Often 1-2 problems)**
- [ ] **Memorize:** x = f(X/Z), y = f(Y/Z)
- [ ] **Practice:** 5 perspective projection problems
- [ ] **Homogeneous coordinates:**
  - (x, y) → [x, y, 1]ᵀ
  - [x, y, w]ᵀ → (x/w, y/w)
- [ ] **Know:** Effects of perspective (parallel lines converge, size decreases with distance)

**PRIORITY 6: Quick Concepts (Know for short answer)**
- [ ] **Image types:** Binary, grayscale, color, labeled
- [ ] **Sampling:** spatial resolution
- [ ] **Quantization:** intensity levels (8-bit = 256 levels)
- [ ] **Laplacian:** Second-order derivative, two masks:
```
  [0  1  0]      [1  1  1]
  [1 -4  1]  or  [1 -8  1]
  [0  1  0]      [1  1  1]
```
- [ ] **LoG:** Laplacian of Gaussian (smoother, less noise sensitive)
- [ ] **Gaussian pyramid:** Smooth + downsample, size = M/2^k
- [ ] **Laplacian pyramid:** Differences, CAN reconstruct

---

### Session 4: Afternoon (2 hours)
**CREATE YOUR 3 EXAM SHEETS**

**SHEET 1: Formulas That WILL Be On Exam**
```markdown
## Perspective Projection
x = f(X/Z),  y = f(Y/Z)

## Sobel (MUST KNOW)
Gx = [-1  0  1]    Gy = [-1 -2 -1]
     [-2  0  2]         [ 0  0  0]
     [-1  0  1]         [ 1  2  1]
|G| = √(Gx² + Gy²),  θ = atan2(Gy, Gx)

## Hough
ρ = x·cos(θ) + y·sin(θ)

## Harris Corner
M = [Iₓ²  IₓIᵧ]    R = det(M) - k·trace(M)²
    [IₓIᵧ Iᵧ²]     k ≈ 0.04-0.06

## Gaussian Filter (if needed)
G(x,y) = (1/2πσ²)·exp(-(x²+y²)/2σ²)
[1 2 1]
[2 4 2] × 1/16  (3×3 approximation)
[1 2 1]

## Laplacian
[0  1  0]      [1  1  1]
[1 -4  1]  or  [1 -8  1]
[0  1  0]      [1  1  1]

## RANSAC Iterations
k = log(1-p) / log(1-w^n)
```

**SHEET 2: Algorithms**
```markdown
## Canny (5 steps - KNOW THIS)
1. Gaussian smoothing
2. Gradient calculation (Sobel)
3. Non-maxima suppression
4. Double thresholding (T_high, T_low)
5. Edge tracking by hysteresis
**3 Parameters:** σ, T_high, T_low

## Non-Maxima Suppression
1. For each pixel: gradient M, direction θ
2. Compare M with 2 neighbors along θ
3. If M > both → keep, else → suppress

## Harris (5 steps)
1. Compute Iₓ, Iᵧ
2. Compute Iₓ², Iᵧ², IₓIᵧ
3. Gaussian weighting: A=G*Iₓ², B=G*Iᵧ², C=G*IₓIᵧ
4. R = AB - C² - k(A+B)²
5. Threshold + NMS

## Hough
For each edge pixel (x,y):
  For θ = 0° to 180°:
    ρ = x·cos(θ) + y·sin(θ)
    Accumulator[ρ,θ] += 1
Find peaks → lines

## RANSAC
For k iterations:
  1. Sample min points (line=2)
  2. Fit model
  3. Count inliers (dist < threshold)
  4. If best → save
Return best model
```

**SHEET 3: Quick Reference**
```markdown
## Filter Comparison (VERY testable)
Mean → Gaussian noise, blurs edges
Gaussian → Gaussian noise, smoother
Median → Salt & pepper, preserves edges

## Corner Eigenvalues
Both λ large → Corner
One λ large → Edge
Both λ small → Flat

## SIFT Properties
Scale invariant
Rotation invariant
Illumination invariant
128D descriptor (4×4 × 8 bins)

## Common Mistakes
- Sobel Gx detects VERTICAL edges
- Vertical line: θ=0°, not 90°
- Harris k = 0.04-0.06 typically
- Canny has 3 params, not 2

## Quick Facts
- Grayscale: 0-255 (8-bit)
- Sampling = spatial resolution
- Quantization = intensity levels
- Gaussian pyramid: size = M/2^k
- Laplacian CAN reconstruct

## MATLAB Reminders
edge(img,'canny',[low high],sigma)
[H,theta,rho] = hough(BW)
imfilter(img, kernel)
medfilt2(img, [3 3])
```

---

## TONIGHT (30 minutes before bed)

**Final Memorization Drill:**
- [ ] Write Sobel masks 5 times from memory
- [ ] Write Canny 5 steps from memory
- [ ] Calculate 3 quick problems:
  1. Sobel on simple patch
  2. ρ for point (2,3) at θ=45°
  3. Harris R given Iₓ=10, Iᵧ=5

---

## HIGH-YIELD TOPIC PRIORITY

**If you run out of time, focus in THIS order:**

1. **Sobel/Edge Detection** (Always on exam, easy points)
2. **Convolution by hand** (Always tested)
3. **Hough Transform** (Guaranteed calculation)
4. **Harris Corner** (Frequent calculation)
5. **Perspective Projection** (1-2 problems)
6. **Canny Algorithm** (Know 5 steps + parameters)
7. **Filter Comparison** (Short answer)
8. **SIFT/Pyramids** (Conceptual only)
9. **RANSAC** (Know basic algorithm)

---

## FINAL CHECKLIST (Morning of Exam)

- [ ] 3 sheets printed/written clearly
- [ ] Calculator ready
- [ ] Quick review (15 min):
  - Sobel masks
  - Canny 5 steps
  - Hough formula
  - Harris R formula
- [ ] Breakfast eaten
- [ ] Arrive early

---

## EXAM STRATEGY

**Remember:**
- Exams test CALCULATIONS more than concepts
- Show your work = partial credit
- If stuck, skip and come back
- Check your units (mm vs m)
- Double-check arithmetic

**The exam will likely have:**
- 2-3 convolution/filtering problems
- 2-3 edge detection (Sobel/gradient)
- 1-2 Hough transform calculations
- 1-2 Harris corner problems
- 1-2 perspective projection
- 2-3 short answer (Canny steps, filter comparison, etc.)

Focus on SPEED and ACCURACY in hand calculations.