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

