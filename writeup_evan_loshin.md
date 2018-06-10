# **Finding Lane Lines on the Road** 

## By: Evan Loshin
**June 9, 2018**

---

[//]: # (Image References)

[image1]: ./test_images_output/first_attempt.png "First Attempt"
[image2]: ./test_images_output/hash_mark.png "Hash Mark"

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline follows these primary steps:
  1. Converts image to grayscale
  1. Blurs image with gaussian noise
  1. Applies canny to identify gradients
  1. Masks the region of interest
  1. Draws Hough lines on masked region
  1. Extrapolates Hough lines and combines with original image

In the last step, I modify the draw_lines() function to create two separate lines and calculate a moving average to overcome a shortcoming.

First, I calculate slope and intercept for each Hough line segment and separate hough lines into left and right on the criteria of positive and negative slope. Then, I average the slopes and intercepts for each side. Initially, this yielded mostly acceptable lane indicators but I was not satified with occasional spurious shifts.

![alt text][image1]

I had an idea to reduce noise by taking a moving average of the line parameters over several video frames. This required the global variable declaration, `avg_lines = []`, in the main cell to retain results over multiple frames. In draw_lines(), I keep a record of the latest 6 frames and average the entries to produce my extrapolated lines. While there is still room for improvement, this method gave much better results.


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming could occur when changing lanes. Although I haven't tested it, I believe draw_lines() could yield an undesireable result, perhaps such as two slightly offset lines, when the lane's slope approaches infinty. The root cause may be HoughLinesP returning segments with positive and negative slopes that define one single lane line. Additionally, there is a chance the near infinite or zero slope may reach the computer's computational limits.

Another shortcoming is construction zones where traffic flow is defined with non-linear objects such as cones and signs. I don't believe Hough Lines would be the right approach in these scenarios. Perhaps image classification would be part of the solution.


### 3. Suggest possible improvements to your pipeline

One potential improvement that I decided to tackle is taking a moving average of the lines' parameters. This yielded a pretty good improvement, but I still noticed a deviation at 11 seconds in the solidYellowLeft.mp4 video caused by an unexpected horizontal hash mark.

![alt test][image2]

An adequate solution might include clustering the slopes and intercepts of the Hough segments. This "data science" approach could remove outliers until a pre-determined measure of cluster effectiveness is acheived. This should eliminate near-horizontal segments.
