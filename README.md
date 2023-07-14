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

1. Camera Calibration: The code includes a function camera_matrix() that loads the camera matrix and distortion coefficients from a pickle file. This calibration is necessary to correct the distortion in images captured by the camera.

2. Image Preprocessing: Several image preprocessing functions are defined to prepare the input image for lane detection. These functions include:

cal_undistort(img, mtx, dist): Applies camera calibration to undistort the input image.
grayscale(img): Converts the image to grayscale.
gaussian_blur(img, kernel_size): Applies Gaussian blur to the image to reduce noise.
Region of Interest: The region_of_interest(img) function defines a polygonal region in the image that encompasses the lane area of interest. This region is used to mask the image and focus only on the relevant lane region.

Gradient Thresholding: Gradient-based edge detection techniques are used to identify lane markings. The code includes functions for gradient thresholding:

abs_sobel_threshold(img, orient='x', sobel_kernel=3, thresh=(0, 255)): Calculates the absolute gradient using Sobel operators in the specified orientation (x or y). Thresholds are applied to create a binary image indicating the gradient magnitude.
mag_threshold(image, sobel_kernel=3, mag_thresh=(0, 255)): Calculates the gradient magnitude using Sobel operators in both x and y directions. Thresholds are applied to create a binary image indicating the gradient magnitude.
dir_threshold(image, sobel_kernel=3, thresh=(0, np.pi/2)): Calculates the gradient direction using Sobel operators in both x and y directions. Thresholds are applied to create a binary image indicating the gradient direction.
Color Thresholding: Color thresholding is used to detect lane markings based on color information. The color_threshold(img, s_thresh=(0,255), v_thresh=(0,255)) function converts the image to HLS and HSV color spaces. Thresholds are applied to the S (saturation) and V (value) channels separately, and a binary image is created based on the thresholded values.

Threshold Pipeline: The thresh_pipeline(img, gradx_thresh=(0,255), grady_thresh=(0,255), s_thresh=(0, 255), v_thresh=(0, 255)) function combines the results of gradient and color thresholding to create a final binary image that highlights the detected lane markings.

Perspective Transform: The code includes functions to perform perspective transformation on the image. These functions are used to obtain a bird's-eye view of the lane markings, which helps in analyzing the lane curvature and detecting departures. The functions include:

img_persp(img): Performs perspective transform on the input image using source and destination points.
Perspective Transformation Setup: The perspective_transformer() function sets up the source and destination points for the perspective transformation. These points define a trapezoidal region of interest in the original image and map it to a rectangular region in the transformed image.

Image Warping: The img_warp(img, src, dst) function applies the perspective transform to the input image using the specified source and destination points. This results in a warped image where the lane markings appear as straight lines.

