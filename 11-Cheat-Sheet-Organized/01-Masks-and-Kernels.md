# Masks and Kernels

## Sobel Operator
```
Gx (detects vertical edges):     Gy (detects horizontal edges):
[-1  0  1]                       [-1 -2 -1]
[-2  0  2]                       [ 0  0  0]
[-1  0  1]                       [ 1  2  1]
```
**Center row/col weighted 2x.** Gx detects vertical edges (intensity change left-right). Gy detects horizontal edges (intensity change top-bottom).

## Prewitt Operator
```
Gx:                Gy:
[-1  0  1]         [-1 -1 -1]
[-1  0  1]         [ 0  0  0]
[-1  0  1]         [ 1  1  1]
```
**Uniform weighting** (no 2x center). Sobel generally better in practice.

## Laplacian Operator
```
4-connected:       8-connected:
[ 0  1  0]         [ 1  1  1]
[ 1 -4  1]         [ 1 -8  1]
[ 0  1  0]         [ 1  1  1]
```
**2nd-order derivative.** Isotropic (rotation invariant). No direction info. Sensitive to noise. Detects edges at **zero-crossings**.

## Mean (Box) Filter
```
[1  1  1]
[1  1  1]  x (1/9)
[1  1  1]
```
**All weights equal.** Simple averaging. Good for Gaussian noise. **Blurs edges significantly.**

## Gaussian Filter (3x3, sigma ~ 1)
```
[1  2  1]
[2  4  2]  x (1/16)
[1  2  1]
```
**Center-weighted.** Better edge preservation than mean. **Separable:** 2D filter = 1D horizontal x 1D vertical. sigma controls smoothing amount.

## Projection Matrix (Homogeneous Coordinates)
```
[x']   [f  0  0  0] [X]
[y'] = [0  f  0  0] [Y]
[z']   [0  0  1  0] [Z]
                     [1]

Then: x = x'/z' = fX/Z,  y = y'/z' = fY/Z
```

---

## Convolution Boundary Handling
- **Zero padding** -- treat outside pixels as 0
- **Replicate** -- extend edge pixel values outward
- **Symmetric/mirror** -- reflect image at boundary
