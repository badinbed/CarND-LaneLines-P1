#**Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/solidYellowCurve2_colored.jpg "Colored Lines"
[image2]: ./examples/solidYellowCurve2_lanes.jpg "Expanded lanes"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps.

1. First I converted the image to grayscale and applied a gaussian smoothing to reduce the noise. I kernel size of 5 seemed to work just fine.
2. Then a canny edge detector is applied to find significant gradients in the images. The thresholds (50, 150) perform good enough on the test set.
3. Afterwards the edge image is masked with a region of interest in the shape of a trapezoid. Its size depends on the image shape. In this pipeline it fills the bottom 40% of the picture vertically and and 10% (top) to 90% (bottom) horizontally. It is applied after the edge detection as the canny would have found strong edges at the border of the region.
4. Next, a hough transformation is used to find all edges in the shape of lines. The linesare then being categorized as either left lane or right lane and colored diffeerently (red and green). Lines that did not fit either category are discarded for further processing and colored blue. The categorization uses the following steps:
	1. Calculate angle between each line and the y-axis and the line lengths
	2. Run a histogram over the angles weighted by the line length
	3. Split the histogram at the 0 degree for left and right lane
	4. Use the maximum bins in each half as starting angle ranges for the lanes
	5. Expand the angle ranges by neighboring bins if they include lines as well
	6. Any line whose angle falls into one of the ranges is categorized accordingly
	
5. Lastly to draw a single line per lane a linear regression of the line end points for each lane is calculated. The result is a linear function x = m * y + b for each lane. The lanes are then drawn for the interval [min_sample_y, img.shape_y] where min_sample_y is the minimum y occuring in any sample line in both lanes.


![alt text][image1]

![alt text][image2]

###2. Identify potential shortcomings with your current pipeline


One shortcoming would be the handling of lanes detection. If the lanes angles' with the y-axis cannot be separated by sign, the algorithm will fail.

Another shortcoming is the rigid handling of the region of interest. It might cut off strong curves.


###3. Suggest possible improvements to your pipeline

A better classification algorithm that can categorize lines into lanes is needed. The classification should not only take the angle or slope into account but the intersection with the bottom of the image as well. This should help distinguishing the two lanes without having to make strict assumptions about the slope.

Also I feel like, that the information gathered on previous frames can improve the decision making as lanes usually only make slight changes from frame to frame.