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

