---
title: operators in halcon to extract edges
date: 2024-06-28 12:00:00 +0800
categories: [halcon,edge-extraction]
tags: [halcon, dip, edge-extraction]
---


### Introduction

In this article, we will learn about the various operators in halcon that can be used to extract edges from an image. We will also see how to use these operators to extract edges from an image.

### Edge detection

Edge detection is the process of identifying the boundaries of objects in an image. It is a fundamental step in many image processing tasks such as object recognition, object tracking, and image segmentation.

There are several methods for edge detection in images, including:

1. Canny edge detector
2. Sobel edge detector
3. Prewitt edge detector
4. Roberts edge detector
5. LoG (Laplacian of Gaussian) edge detector

In this article, we will learn about the algorithms proposed by S.Lanser[1][2] .

[1]:S.Lanser, W.Eckstein: “Eine Modifikation des Deriche-Verfahrens zur Kantendetektion” DAGM-Symposium, München; Informatik Fachberichte 290; Seite 151 - 158; Springer-Verlag; 1991.

[2]:S.Lanser: “Detektion von Stufenkanten mittels rekursiver Filter nach Deriche”; Diplomarbeit; Technische Universität München, Institut für Informatik, Lehrstuhl Prof. Radig; 1991.

compared to the traditional Canny edge detector, lanser algorithm is more robust to noise and can handle a wider range of lighting conditions, can detect edges in a wide range of orientations.

### edges_sub_pix (Operator)

### description

edges_sub_pix — Extract sub-pixel precise edges using Deriche, Lanser, Shen, or Canny filters.
edges_sub_pix detects step edges using recursively implemented filters (according to Deriche, Lanser and Shen) or the conventionally implemented “derivative of Gaussian” filter (using filter masks) proposed by Canny. Thus, the following edge operators are available:

'deriche1', 'lanser1', 'deriche2', 'lanser2', 'shen', 'mshen', 'canny', 'sobel', and 'sobel_fast'


### response

The extracted edges are returned as sub-pixel precise XLD contours in Edges. For all edge operators except 'sobel_fast', the following attributes are defined for each edge point (see get_contour_attrib_xld):

'edge_direction'	Edge direction
'angle'	Direction of the normal vectors to the contour (oriented such that the normal vectors point to the right side of the contour as the contour is traversed from start to end point; the angles are given with respect to the row axis of the image.)
'response'	Edge amplitude (gradient magnitude)

### how edges are choosen

edges_sub_pix links the edge points into edges by using an algorithm similar to a hysteresis threshold operation, which is also used in lines_gauss. Points with an amplitude larger than **High** are immediately accepted as belonging to an edge, while points with an amplitude smaller than **Low** are rejected. All other points are accepted as edges if they are connected to accepted edge points (see also lines_gauss and hysteresis_threshold).


### filters with junctions

Because edge extractors are often unable to extract certain junctions, a mode that tries to extract these missing junctions by different means can be selected by appending '_junctions' to the values of Filter that are described above. This mode is analogous to the mode for completing junctions that is available in lines_gauss.

The following filters can be used with the '_junctions' mode:

- 'deriche1_junctions'
- 'deriche2_junctions'
- 'lanser2_junctions'
- 'laser1_junctions'
- 'shen_junctions'
- 'mshen_junctions'
- 'sobel_junctions'


> Hysteresis thresholding is a common technique used in edge detection to separate foreground from background pixels based on their intensity values. In this technique, a threshold value is chosen for the foreground pixels, and all pixels with an intensity value above the threshold are considered foreground(edges). All pixels with an intensity value below the threshold are considered background(non-edges). However, this technique can lead to the loss of edges that are too small or too weak to be detected. The hysteresis thresholding algorithm is used to avoid this loss by allowing some pixels to be classified as either foreground or background based on their intensity values, but not immediately accepting or rejecting them. Instead, the algorithm gradually decreases the threshold value until only the most significant edges are detected.

- Apply the High Threshold:

Mark all pixels greater than the high threshold as edges.

- Apply the Low Threshold:

Mark all pixels below the low threshold as non-edges.

- Between the Thresholds:

For pixels with intensity between the low and high thresholds, consider them as edges only if they are connected to a pixel that is already marked as an edge.

### parameters

- **Image** (Image): Input image.
- **Filter** (string): Edge detection filter to be used. Possible values are: 'deriche1', 'deriche2', 'lanser1', 'lanser2','shen','mshen', 'canny','sobel', and'sobel_fast'.
- **High** (real): High threshold for hysteresis thresholding.
- **Low** (real): Low threshold for hysteresis thresholding.
- **alpha** (real): Filter parameter: small values result in strong smoothing, and thus less detail (opposite for 'canny').

``` python

read_image(Image,'fabrik')
edges_sub_pix (GrayImage, Edges1, 'lanser2', 0.2, 7, 15)

```


### example extracting circles from an image

``` python

read_image(Image,'fabrik')
# extract edges from image, using Lanser2 filter with hysteresis thresholding
edges_sub_pix (GrayImage, Edges1, 'lanser2', 0.2, 7, 15)
# select circles with circularity greater than 0.8 and area greater than 1
select_shape_xld (Edges1, SelectedXLD, 'circularity', 'and', 0.8, 1) 

# use smallest_circle_xld to extract the center and radius of the circles
smallest_circle_xld (SelectedXLD, Row, Column, Radius)

```


``` python

# get image from camera

# Image Acquisition 01: Code generated by Image Acquisition 01
open_framegrabber ('USB3Vision', 0, 0, 0, 0, 0, 0, 'progressive', -1, 'default', -1, 'false', 'default', '2BDF45565170_Hikvision_MVCE06010UM', 0, -1, AcqHandle)
set_framegrabber_param (AcqHandle, 'DeviceUserID', '')
set_framegrabber_param (AcqHandle, 'PixelFormat', 'Mono8')
set_framegrabber_param (AcqHandle, 'AcquisitionFrameRateEnable', 0)
set_framegrabber_param (AcqHandle, 'TriggerCacheEnable', 0)
set_framegrabber_param (AcqHandle, 'ExposureMode', 'Timed')
set_framegrabber_param (AcqHandle, 'ExposureTime', 25000.0)
set_framegrabber_param (AcqHandle, 'grab_timeout', 500)
set_framegrabber_param (AcqHandle, 'start_async_after_grab_async', 'enable')
set_framegrabber_param (AcqHandle, 'volatile', 'disable')
grab_image_start (AcqHandle, -1)

while (true)
    grab_image_async (Image, AcqHandle, -1)
    rgb1_to_gray (Image, GrayImage)
    
    edges_sub_pix (GrayImage, Edges1, 'lanser2', 0.5, 10, 20)
    # select circles with circularity greater than 0.7 and width greater than 120
    select_shape_xld (Edges1, SelectedXLD, ['circularity','width'], 'and', [0.7,120], [1,170]) 
    
    # use smallest_circle_xld to fit the circles so that we can get the center and radius
    smallest_circle_xld (SelectedXLD, Row, Column, Radius)
    # generate cross contours at the center of the circles
    gen_cross_contour_xld (Cross, Row, Column, 60, 0)

endwhile

close_framegrabber (AcqHandle)
```