# Formulas

## Perspective Projection
```
x = f * (X / Z)
y = f * (Y / Z)
```
**f** = focal length, **(X,Y,Z)** = 3D world point, **(x,y)** = 2D image point. **Larger Z = smaller image.**

## Homogeneous Coordinates
- **Cartesian -> Homogeneous:** (x, y) -> [x, y, 1]
- **Homogeneous -> Cartesian:** [x, y, w] -> (x/w, y/w)
- **Purpose:** turns nonlinear projection into linear matrix multiply; enables chaining transforms

## Convolution
```
Output(i,j) = SUM SUM Image(i+m, j+n) x Kernel(m,n)
```
**Process:** place kernel over patch, multiply corresponding elements, sum all products.

## Gradient Magnitude and Direction
```
|G| = sqrt(Gx^2 + Gy^2)
theta = atan2(Gy, Gx)
```

## Gaussian Function (2D)
```
G(x,y) = (1 / (2*pi*sigma^2)) * exp(-(x^2 + y^2) / (2*sigma^2))
```

## Laplacian of Gaussian (LoG)
```
LoG = (x^2 + y^2 - 2*sigma^2) / sigma^4 * exp(-(x^2 + y^2) / (2*sigma^2))
```
**Combines smoothing + edge detection.** Edges found at **zero-crossings.**

## Hough Transform (rho-theta)
```
rho = x*cos(theta) + y*sin(theta)
```
**rho** = perpendicular distance from origin to line, **theta** = angle of perpendicular **[0, 180)**.

**Convert from slope-intercept:**
```
Given y = mx + b:
rho = b / sqrt(1 + m^2)
theta = arctan(-1/m)
```

## Harris Corner Response
```
R = det(M) - k * (trace(M))^2
R = lambda1*lambda2 - k*(lambda1 + lambda2)^2
```
**k** = 0.04 to 0.06. **R > threshold = corner. R < 0 = edge. |R| small = flat.**

## Second Moment Matrix M
```
M = SUM w(x,y) [Ix^2    Ix*Iy]     M = [A  C]     A = SUM Ix^2
    x,y        [Ix*Iy   Iy^2 ]         [C  B]     B = SUM Iy^2
                                                    C = SUM Ix*Iy
det(M) = AB - C^2
trace(M) = A + B
```

## Error Function E(u,v)
```
E(u,v) = SUM w(x,y) [I(x+u, y+v) - I(x,y)]^2
```
**Taylor approx:** `I(x+u, y+v) ~ I(x,y) + Ix*u + Iy*v` --> **Simplified:** `E(u,v) ~ [u v] M [u; v]`

## Patch Comparison
- **SSD:** `SUM (I1(x,y) - I2(x,y))^2` -- **lower = more similar**
- **NCC:** `SUM(I1*I2) / sqrt(SUM(I1^2) * SUM(I2^2))` -- range [-1,1], **higher = more similar**

## Scale Space (SIFT)
```
L(x, y, sigma) = G(x, y, sigma) * I(x, y)
```

## Difference of Gaussians (DoG)
```
D(x, y, sigma) = L(x, y, k*sigma) - L(x, y, sigma)
```
**Approximates scale-normalized Laplacian.** k = typically sqrt(2).

## SIFT Descriptor Size
```
4x4 spatial grid x 8 orientation bins = 128 dimensions
```

## RANSAC Iterations
```
k = log(1 - p) / log(1 - w^n)
```
**p** = desired success probability (e.g. 0.99), **w** = inlier ratio, **n** = minimum sample size.

## Pyramid Sizes
```
Level k size: M/2^k x N/2^k
```
Example: 512x512 -> level 1: 256x256 -> level 2: 128x128 -> level 3: 64x64

## Laplacian Pyramid
```
L_k = G_k - expand(G_{k+1})
Reconstruct: G_k = L_k + expand(G_{k+1})
```
