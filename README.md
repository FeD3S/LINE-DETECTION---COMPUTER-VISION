# Computer Vision Lane Detection

An automated pipeline to detect road lanes and classify markings (solid vs. dashed) using classical image processing. Developed by Federico Saporiti.

##  How it Works
1. **Preprocessing & Masking:** Uses Grayscale, Gaussian Blur, Canny Edge Detection, and a triangular ROI mask to isolate structural road boundaries.
2. **Line Extraction:** Applies the Probabilistic Hough Transform to find segments, which are averaged by slope to form stable left/right lane boundaries.
3. **Classification:** Calculates a custom `hit_ratio` using 50 horizontal check lines. High intersection rates indicate solid lines; gaps indicate dashed lines.

## Possible Developements
* Automate the manual ROI using vanishing point estimation.
* Improve algorithmic robustness against shadows and pavement transitions.
* Implement temporal smoothing across video frames for classification stability.

For more details, consult the pdf file in the repository.
