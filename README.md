<img width="938" alt="Screenshot 2023-07-14 at 11 35 02 PM" src="https://github.com/ashwinsathish/Adv-lane-detection/assets/94456641/a627570b-3a49-41f3-95c4-1556d8128e3b"># Advaned lane detection and departure warning

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

## 4. Lane parameters

The `meas_curvature()` function calculates the curvature of the lane lines and the offset of the vehicle from the center of the lane. The radii of curvature for both lane lines are then computed using the fitted polynomial coefficients and the y-coordinate of the bottom of the image.The offset of the vehicle from the center of the lane is determined by calculating the difference between the midpoint of the lane lines and the midpoint of the image, converted to meters.The `lane_chars function()` then calculates the mean and variance of the lane width. 

## 5. Lane finding and departure warning

The `lane_finding()` function is the main pipeline for lane detection and departure warning. It takes an undistorted image as input and performs the following steps:
- Applies thresholding to obtain a binary image with lane pixels.
- Warps the binary image to a bird's-eye view using perspective transform.
- If the previous lane detection was successful for both left and right lanes, it uses the lane_tracking function to track the lanes in the current frame based on the previous fits.
- Otherwise, it uses the sliding_windows_search function to perform a sliding window search and detect the lanes.
- Updates the current fits, best fits, and recent fits for both left and right lanes.
- Checks the lane width, lane continuity, and lane width variance to validate the detected lanes. If the lane parameters do not meet the specified criteria, the lane detection is considered unsuccessful.
- If the lane detection is successful, the function recalculates the curvature and lane width using the best fits.
- Warps the detected lane boundaries back onto the original image, adds text annotations for curvature, vehicle offset, and lane width, and displays a lane departure warning if the vehicle is outside the acceptable lane deviation threshold.
- Finally, returns the annotated output image.

----

## Salient features
The code incorporates several salient features that differentiate it from conventional options.It implements a robust mechanism to handle scenarios where the pipeline fails to detect lane pixels based on the previous frame. In such cases, it switches to a blind search mode, thoroughly scanning the entire binary image to identify non-zero pixels representing the lanes. This adaptive approach ensures comprehensive coverage for accurate lane detection.To achieve smooth and stable lane tracking, the code employs polynomial fitting techniques to estimate the lane lines. The coefficients of the polynomials are averaged over a span of multiple frames, providing temporal stability and reducing fluctuations in lane position estimation. 

To evaluate the lane quality, the code calculates various metrics such as lane curvature, vehicle shift from the center, and lane width. These metrics are displayed on the output image to provide meaningful insights into the lane position and alignment. Additionally, the code includes lane departure warning functionality, issuing an alert when the vehicle deviates significantly from the lane. 

The algorithm automatically validates the detected lane lines through 3 major sanity checks. Firstly, the code checks if the detected lane lines have similar curvatures By comparing the curvature of the left and right lines. Secondly, the code verifies the horizontal separation between the detected lines for ensuring even spacing throughout the calculations. Lastly, the code assesses the parallelism of the detected lines ensuring that the detected lines align with the expected parallel orientation of lane markings.

## Challenges and scope

### 1. Object detection functionality
In developing a system for lane detection and road sign detection, several challenges were encountered that affected the performance and accuracy of the algorithms. One of the primary challenges was the inability to correctly visualize border boxes around road signs, even when the images contained sign boards. This may be due to perspective and viewpoint variations such as when the model may have been trained only on a specific camera perspective (i.e., dashcam position). This could be improved by using a more comprehensive dataset and complex feature extraction techniques to account for variations in sign board designs across places.

### 2. Highly dynamic frames
The algorithm suffered from the challenge of dynamically adjusting to different lighting conditions or road curves. There seemes to be a marginally improved performance by limiting frame rate and using slomo-type input videos. This could be improved upon by creating dynamic polygons for each frame or categorizing specific type of lane curves, lighting and road conditions and training them on specific algorithms. 


