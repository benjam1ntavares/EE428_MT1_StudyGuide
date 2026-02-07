# MATLAB Quick Reference Index

## Topics

### Image Basics
- [[Image-IO-and-Display]]
- [[Image-Type-Conversions]]
- [[Pixel-Access-and-Manipulation]]

### Filtering
- [[Convolution-and-Filtering]]
- [[Creating-Filter-Masks]]
- [[Median-and-Nonlinear-Filters]]

### Edge Detection
- [[Gradient-Operators]]
- [[Edge-Detection-Functions]]
- [[Canny-Edge-Detector]]

### Hough Transform
- [[Hough-Functions]]
- [[Line-Detection-Workflow]]

### Feature Detection
- [[Corner-Detection-Functions]]
- [[Harris-Implementation]]
- [[SIFT-in-MATLAB]]

---

## Common Functions Quick List

```matlab
% Image I/O
imread, imshow, imwrite

% Type conversion
im2double, im2uint8, rgb2gray

% Filtering
imfilter, fspecial, medfilt2

% Edge detection
edge, imgradientxy, imgradient

% Hough
hough, houghpeaks, houghlines

% Features
detectHarrisFeatures, detectSIFTFeatures
extractFeatures, matchFeatures
```

