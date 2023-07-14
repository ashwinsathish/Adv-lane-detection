# Advaned lane detection and departure warning

In this project, I developed a computer vision system that can detect and track lane boundaries on the road using camera images. The goal of this project was to create an accurate and robust lane detection algorithm that can be used in autonomous driving systems.

## Preliminary outline
- Perform camera calibration by calculating the camera calibration matrix and distortion coefficients using a collection of chessboard images.
- Correct image distortion by applying the obtained calibration parameters to raw input images.
- Utilize various techniques such as color transforms and gradients to create a binary image with informative lane features based on thresholding.
- Apply a perspective transform to rectify the binary image, providing a "birds-eye view" perspective of the road.
- Identify lane pixels and fit a suitable mathematical curve to accurately determine the lane boundaries.
- Calculate the curvature of the lane and determine the vehicle's position relative to the lane center.
- Revert the detected lane boundaries from the transformed perspective back onto the original image using an inverse perspective transform.
- Generate a visually appealing output display showcasing the detected lane boundaries along with numerical estimates of lane curvature and vehicle position.

----

## 1. Camera Calibration 
The code includes a function `camera_matrix()` that loads the camera matrix and distortion coefficients from a pickle file. This calibration is necessary to correct the distortion in images captured by the camera.

## 2. Image preprocessing
Several image preprocessing functions are defined to prepare the input image for lane detection. These functions include:

- `cal_undistort()`: Applies camera calibration to undistort the input image.
- `grayscale()`: Converts the image to grayscale.
- `gaussian_blur()`: Applies Gaussian blur to the image to reduce noise.
- Region of Interest: The `region_of_interest()` function defines a polygonal region in the image that encompasses the lane area of interest. This region is used to mask the image and focus only on the relevant lane region.

### 2.1. Gradient Thresholding 
Gradient-based edge detection techniques are used to identify lane markings. The code includes functions for gradient thresholding:

- `abs_sobel_threshold()`: Calculates the absolute gradient using Sobel operators in the specified orientation (x or y). Thresholds are applied to create a binary image indicating the gradient magnitude.
- `mag_threshold()`: Calculates the gradient magnitude using Sobel operators in both x and y directions. Thresholds are applied to create a binary image indicating the gradient magnitude.
- `dir_threshold()`: Calculates the gradient direction using Sobel operators in both x and y directions. Thresholds are applied to create a binary image indicating the gradient direction.
- Color Thresholding: This is used to detect lane markings based on color information. The `color_threshold()` function converts the image to HLS and HSV color spaces. Thresholds are applied to the S (saturation) and V (value) channels separately, and a binary image is created based on the thresholded values.
- Threshold Pipeline: The `thresh_pipeline()` function combines the results of gradient and color thresholding to create a final binary image that highlights the detected lane markings.

### 2.2. Perspective Transform
The code includes functions to perform perspective transformation on the image. These functions are used to obtain a bird's-eye view of the lane markings, which helps in analyzing the lane curvature and detecting departures. The functions include:

- `img_persp()`: Performs perspective transform on the input image using source and destination points.
Perspective Transformation Setup: The `perspective_transformer()` function sets up the source and destination points for the perspective transformation. These points define a trapezoidal region of interest in the original image and map it to a rectangular region in the transformed image.

### 2.3. Image Warping
The img_warp() function applies the perspective transform to the input image using the specified source and destination points. This results in a warped image where the lane markings appear as straight lines.

## 3. Lane detection

### 3.1 Interpol_RanSac Function
The Interpol_RanSac function performs polynomial interpolation using the RANSAC algorithm.It takes input X and y arrays representing the x and y coordinates of lane pixels, and x_plot representing the x coordinates for plotting the fitted curve. The function uses the `RANSACRegressor` method to robustly fit a polynomial curve to the given data points.

### 3.2 Sliding window search
This function performs lane detection using a sliding window approach. It takes an input binary image that has undergone perspective transformation and thresholding.It starts by creating a histogram of pixel intensities along the vertical axis of the bottom half of the image and then identifies the starting points for the left and right lanes by locating the peaks in the left and right halves of the histogram.Sliding windows are then used to track the lane pixels vertically from bottom to top.The helps identify and collect the pixel coordinates belonging to the left and right lanes, and fit a second-order polynomial to each set of coordinates.

### 3.3 Lane masking and tracking:

The `lane_mask()` function creates a lane mask overlay on the original undistorted image. This uses the fitted lane curves to generate polygonal curves for the left and right lanes. The purpose of this function is to provide a visual representation of the detected lanes on the original undistorted image.

The lane_tracking function performs lane tracking based on the previously detected lane curves.
It takes the input binary image that has undergone perspective transformation and thresholding, as well as the previous lane curves `left_fit` and `right_fit`.This function performs a margin-based search around the previous lane curves to identify lane pixels. The identified lane pixels are then used to refit second-order polynomials to the left and right lanes.


