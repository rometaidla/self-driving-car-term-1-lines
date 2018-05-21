# **Finding Lane Lines on the Road** 

[//]: # (Image References)

[color_mask]: ./examples/colormask.png "Yellow and white colors masked"
[grayscale]: ./examples/grayscale.png "Image converted to grayscale"
[blur]: ./examples/blur.png "Gaussian blur applied to image"
[region_of_interest]: ./examples/region_of_interest.png "Region of interest"
[canny]: ./examples/canny.png "Canny edges detected"
[hough_transfrom]: ./examples/hough_transform.png "Hough tranform"

---

## Line detection pipeline

Pipeline has following 6 step:


1. **Mask yellow and white colors**:
    
    Mask is created to keep white and yellow colors in RGB color space and then also in HLS 
    color space for improving color detection in noisy places like shadows etc.
    
    ![color_mask]
    
2. **Convert to grayscale**:

    Image is converted to grayscale.
    
    ![grayscale]
    
3. **Blur image**:

    Gaussian blur with kernel size 5 is applied to image to reduce noise.
    
    ![blur]
    
4. **Detect edges**:

    Canny edges is detected using low threshold 50 and high threshold 150
    
    ![canny]
    
5. **Mask region of interest**:

    Region of interest is selected using polygon. Polygon vertices is calculated using proportions of
    image width and height:
    
    `(.55*imshape[1], .58*imshape[0]), (.45*imshape[1], .58*imshape[0]), (0, imshape[0]), (imshape[1], imshape[0])`
    
    ![region_of_interest]
    
6. **Hough lines**:

    Hough lines is detected and lines having slopes between 55°..90° and -55°..-99° are used to calculate average slopes and 
    center points for left and right sides.
    
    Lines are then drawn from bottom of the image (`imshape[0]`) to the high point of region of interest (`.58*imshape[0]`) 
    passing through average center points and using found average slopes found earlier.
    
    ![hough_transfrom]


## Shortcomings and possible improvements

1. Lines are detected on every frame separately, so there is some fluttering between different frames. Usage of some
momentum algorithm like moving average would give better result.
2. Linear approximation is used, so the solution works best, when car is driving straight line. When car is cornering 
there is some curvature so quadratic function would be probably better fit.
3. On challenge video there is still a lot of noise (shadows) detected as lines, which should be filtered out.