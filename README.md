# Computer Vision Lane Detection 

An automated computer vision pipeline designed to detect road lanes, highlight the drivable roadway area, and classify lane markings (solid vs. dashed) in both static images and video streams using classical image processing techniques.

Developed by Federico Saporiti, Control Systems Engineer, University of Padua

##  Objective
The primary goal of this program is to extract structural road information without relying on deep learning models. By processing raw frames through a series of geometric and pixel-intensity transformations, the algorithm successfully isolates lane boundaries and interprets their marking type.

## The Detection Pipeline

### 1. Image Preprocessing
The raw image is passed through a Canny edge detection pipeline to highlight structural boundaries:
* **Grayscale Conversion:** Converts the standard BGR image to a single intensity channel, stripping away non-essential color data to simplify computation.
* **Gaussian Blur:** Applies a 5x5 smoothing kernel to reduce high-frequency noise and intensity fluctuations, preserving only major edges.
* **Canny Edge Detection:** Computes intensity gradients to locate sharp transitions. It uses non-maximum suppression to thin the edges and a double-threshold mechanism to retain only the strongest, most reliable edge maps.

### 2. Region of Interest (ROI) Masking
To prevent the algorithm from processing irrelevant background noise (like trees, sky, or oncoming traffic), a triangular Region of Interest is applied. This mask isolates the lower-center portion of the frame where lane markings are predictably located.

### 3. Line Extraction & Consolidation
* **Probabilistic Hough Transform (`cv.HoughLinesP`):** Identifies straight line segments by mapping edge pixels into a parameter space (Hough space) and finding collinear points. The probabilistic approach significantly reduces computational complexity, making it suitable for real-time video processing.
* **Averaging:** The raw segments are separated into "left" and "right" lines based on the sign of their slope (negative for left, positive for right). The algorithm averages the slope and intercept of these segments to calculate a single, highly stable mathematical line for each side. 
* **Lane Area:** A polygon is dynamically drawn and filled between the two averaged lines to highlight the current ego-lane.

##  Line Type Classification (Solid vs. Dashed)

To determine the legal passing constraints of the lane, the algorithm analyzes the structural gaps in the raw Hough segments using a custom **`hit_ratio`** metric:
1. **Horizontal Scanning:** 50 equally spaced, invisible horizontal "check lines" are generated across the lower 60% of the image.
2. **Intersection Checking:** The program checks how many of these horizontal lines intersect with the raw detected lane segments.
3. **Classification:** * **Solid Lines:** Produce continuous vertical edges, intersecting with the vast majority of the check lines (high `hit_ratio`).
   * **Dashed Lines:** Contain physical gaps, resulting in alternating hits and misses (low `hit_ratio`).
   * If the `hit_ratio` exceeds a specific threshold (e.g., 0.7), the lane is marked as solid; otherwise, it is dashed.

## Limitations & Future Developments

* **Static ROI:** The Region of Interest is currently manually defined. A future iteration could implement dynamic ROI generation based on vanishing point estimation or camera calibration.
* **Noise Susceptibility:** The Hough Transform can occasionally be misled by strong shadows, guardrails, or pavement transitions.
* **Temporal Smoothing:** For video processing, the solid/dashed classification could be upgraded from a spatial check to a temporal one. By tracking a single horizontal control line across a 20-30 frame window, the program could mathematically confirm intermittent intersections over time, vastly improving classification stability.
