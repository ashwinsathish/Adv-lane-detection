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

