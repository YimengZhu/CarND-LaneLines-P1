# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Description of the pipeline and draw_lines() function.

My pipeline consisted of following 6 steps, which are implemented in the process_single_image(image) function:
1. convert the image to a gray scale image

`gray = grayscale(image)`

2. apply the Gaussian filter with the kernel size of 5

```python
kernel_size = 5
blur_gray = gaussian_blur(gray, kernel_size)
```

3. apply the Candy to detect the edges

```python
low_threshold = 50
high_threshold = 150
edges = canny(blur_gray, low_threshold, high_threshold)
```

4. apply the mask to select the interested area

```python
imshape = image.shape
mask = np.array([[(30,imshape[0]),(450, 325), (500, 325), (imshape[1],imshape[0])]], dtype=np.int32)
masked_edge = region_of_interest(edges, mask)
```

5. apply the Hough transformation to get the endpoints of lines with the following parameters

```python
rho = 1
theta = np.pi / 180
threshold = 15
min_line_length = 30
max_line_gap = 10
lines = hough_lines(masked_edge, rho, theta, threshold, min_line_length, max_line_gap)
```

6. draw the lines on the edge image

`lines_image = weighted_img(lines, image)`

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by average the slope and intercept of the line segments on each side. Based on the averaged slope and intercept the line can be drawn between the highest position of the line segments and the bottom of the image.

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be that the parameters such as thresholds in the canny are hard coded in the program. This may hurt the robustness of the pipeline. For example when the light condition is different the result of detection of the lane lines would also be much different under the parameters given in this program.


### 3. Suggest possible improvements to your pipeline
To overcome the shortcoming mentioned above we may not use the hardcoded threshold for the canny. Instead we can firstly adjust the contrast of the image and then compute the threshold based on the image after contrast adjusting. This could make the pipeline more robust and have a better performance under different light condition.
