# **Finding Lane Lines on the Road** 

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps:
1. Converting the images to grayscale
   * This is done by using the helper function `grayscale()`
2.  Applying Gaussian blur to the images
    * This is done by using the helper funciton `gaussian_blur()`
    * The argument `kernel_size=13` is imitated from the suggestion of a Udacity Student in [a blog post](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954)
3.  Finding object edges by using Canny Edges
    * This is done by using the helper function `canny()`
    * Arguments used of `low_threshold=50` and `high_threshold=150` are imitated from the suggestion in the classroom and especially for keeping the ratio of `low_threshold/high_threshold` by 1:3
4. Masking the Canny Edges output images with a trapezoid area
   * This is done by using the helper function `region_of_interest()`
   * The vertices arguments of trapezoid shape are passed as to approximate the region where the lane lies on the image
5. Applying Hough Transforms to get lines of the lane
   * This is done by using the helper function `hough_lines()`
   * `hough_lines()` is modified with the addition of `try...finally...` to handle an error raised by `draw_lines()`, as when there is no left or right lines found by with Hough Transforms.
   * The arguments passed are:
     * `rho = 1`
     * `theta = np.pi/180`
     * `threshold = 15`: A value set by random guessing
     * `min_line_length = 10` and `max_line_gap = 250`: Imitated from [this link](https://livecodestream.dev/post/hough-transformation/)
6. Annotate the images with the Hough lines overlayed
   * This is done by using the helper function `weighted_img()`

In order to draw a single line on the left and right lanes, I modified the `draw_lines()` function as follows:
1. I exclude the lines with slope < 0.5 as to only include lines with the orientation of the lane.
2. Lines are grouped as `left_lines` and `right_lines` consisted of lines approximating where the left or right lane lie.
3. Get an average line by averaging all `left_lines` and `right_lines`.
4. Get coefficients of the average line by using the `np.polyfit()`.
5. Use the coefficients to extrapolate the left and right lane lines.
6. Draw the extrapolated lines to an image.

### 2. Identify potential shortcomings with your current pipeline

* Averaging the Hough-lines per se might not be the best algorithm to arrive at the best approximated lane-lines. It is easy to foresee a shortcoming where there would be several lines detected in the 'interest area' of the image of which do not represent the lane-lines: lines found on the road, but between or outside of the lane lines, thus would skew the extrapolated lines.
* The lighting of the road would be another potential shortcoming as it might reduce the contrast between the lanes and the road or it might produce shadows of the objects surrounding the road, thus skewing the extrapolated lines: shadows of trees or buildings.
* Dirty road or uneven colored road could also pose as another shortcoming as it would produce Hough-lines that do not correspond to the lanes (but to the dirt-streak on the road, for example), thus skewing the extrapolated lines.
* There would be times that there is no line detected on the road and posing the risk of the car not knowing its lane to drive on.

### 3. Suggest possible improvements to your pipeline

* A modification of the region of interest, which is not a general trapezoid consisted of the two lane lines and everyting in between or outside it, as to approximate the left-lane line and the right-lane line separately. This is to ensure that no lines which could skew the extrapolated lines would be detected, as it would exclude the lines found in between the lanes and the large chunk or region outside the lane. I believe this modification would significantly improve the potential shortcomings of the lines-averaging algorithm, lighting of the road, and uneven-colored road.
* As to the problem when the lines are occasionally not detected, I think adding more sensors to the car, e.g. Lidar, would significantly help the car to decide where its lane is.
