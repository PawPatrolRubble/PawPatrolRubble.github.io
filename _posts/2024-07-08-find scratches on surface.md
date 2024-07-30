---
title: "How to detect scratches on a surface using Halcon script?"
date: 2024-07-30 12:00:00 +0800
categories: [halcon,defects-detection]
tags: [halcon,defects-detection]
---

To detect scratches on a surface using the Halcon script provided, you can follow these steps:

### 1. **Image Preparation**
   - **Load the Image**: Start by loading the image of the surface where scratches need to be detected.
   - **Invert the Image**: Inverting the image helps in highlighting the scratches, especially if they appear darker than the surrounding area.

### 2. **Frequency Domain Filtering**
   - **Create a Bandpass Filter**: Generate a suitable bandpass filter that allows a specific range of frequencies to pass. This is crucial for isolating the high-frequency components of the image, which correspond to fine details like scratches.
   - **Fourier Transform the Image**: Transform the inverted image into the frequency domain using a Fourier Transform. This step converts the spatial information into frequency information, where each point represents a specific frequency component of the image.
   - **Apply the Bandpass Filter**: Use the generated bandpass filter on the frequency domain representation of the image. This enhances the high-frequency components (i.e., scratches) by filtering out low-frequency information (i.e., background and gradual illumination changes).
   - **Inverse Fourier Transform**: Convert the filtered frequency domain image back into the spatial domain. The resulting image will have enhanced scratches while suppressing other features.

### 3. **Morphological Processing and Segmentation**
   - **Thresholding**: Apply a threshold to the image to segment the enhanced scratches. This creates a binary image where the pixels corresponding to scratches are set to one, and the rest are set to zero.
   - **Connected Components**: Identify connected components in the binary image to separate different regions, each potentially representing a scratch or a group of scratches.
   - **Shape Selection**: Filter the connected components based on specific shape features, such as area, to remove noise or irrelevant regions. This helps in focusing on actual scratches.
   - **Dilation**: Apply a dilation operation to the selected regions. Dilation helps in enhancing the visibility of scratches by expanding their boundaries.
   - **Union of Regions**: Combine the dilated regions into a single region representing all the detected scratches.
   - **Domain Reduction**: Reduce the domain of the original image to the area containing the detected scratches, which isolates the scratches from the rest of the image.
   - **Line Detection**: Use line detection techniques (e.g., Gaussian filtering) to refine the detection of scratch-like structures.
   - **Merge Collinear Contours**: Combine collinear line segments into longer lines, ensuring that individual segments of a scratch are connected.
   - **Final Shape Selection**: Perform a final selection based on the length or other attributes to refine the set of detected scratches.
   - **Convert to Region**: Convert the detected lines or contours into regions for easier visualization and further processing.
   - **Final Dilation**: Apply another dilation to ensure that the scratches are fully captured and visible.

### 4. **Display and Output**
   - **Set Display Properties**: Configure the display properties, such as line width and color, for visualizing the results.
   - **Display Original Image and Detected Scratches**: Show the original image and overlay the detected scratches to visualize the results.

These steps provide a comprehensive method for detecting and highlighting scratches on a surface, utilizing advanced image processing techniques like frequency domain filtering and morphological operations.


### scripts

```c++

explain the following halcon script:
* This program shows how to detect defects (scratches) in
* an inhomogeneously illuminated surface by filtering in
* the frequency domain.
* First, a suitable bandpass filter is created. Then, the
* input image is fourier transformed and filtered in the
* frequency domain, so that high frequency information is
* enhanced. Finally, it is transformed back to the
* spatial domain and the enhanced defects are post-processed
* by morphology.
* 
dev_update_off ()
dev_close_window ()
read_image (Image, 'surface_scratch')
invert_image (Image, ImageInverted)
get_image_size (Image, Width, Height)
dev_open_window (0, 0, Width, Height, 'black', WindowHandle)
set_display_font (WindowHandle, 16, 'mono', 'true', 'false')
dev_display (Image)
* 
* Optimize the speed of the fast fourier transform
* Message := 'Optimize the speed of the fast fourier transform.'
* Message[1] := 'Please wait...'
* disp_message (WindowHandle, Message, 'window', 12, 12, 'black', 'true')
* optimize_rft_speed (Width, Height, 'standard')
* disp_continue_message (WindowHandle, 'black', 'true')
* stop ()
* 
* Enhance the scratches by filtering in the frequency domain
gen_sin_bandpass (ImageBandpass, 0.4, 'none', 'rft', Width, Height)
rft_generic (ImageInverted, ImageFFT, 'to_freq', 'none', 'complex', Width)
convol_fft (ImageFFT, ImageBandpass, ImageConvol)
rft_generic (ImageConvol, Lines, 'from_freq', 'n', 'byte', Width)
* 
* Segment the scratches by using morphology
threshold (Lines, Region, 5, 255)
connection (Region, ConnectedRegions)
select_shape (ConnectedRegions, SelectedRegions, 'area', 'and', 5, 5000)
dilation_circle (SelectedRegions, RegionDilation, 5.5)
union1 (RegionDilation, RegionUnion)
reduce_domain (Image, RegionUnion, ImageReduced)
lines_gauss (ImageReduced, LinesXLD, 0.8, 3, 5, 'dark', 'false', 'bar-shaped', 'false')
union_collinear_contours_xld (LinesXLD, UnionContours, 40, 3, 3, 0.2, 'attr_keep')
select_shape_xld (UnionContours, SelectedXLD, 'contlength', 'and', 15, 1000)
gen_region_contour_xld (SelectedXLD, RegionXLD, 'filled')
union1 (RegionXLD, RegionUnion)
dilation_circle (RegionUnion, RegionScratches, 10.5)
* 
* Display the results
dev_set_draw ('margin')
dev_set_line_width (3)
dev_set_colored (12)
dev_display (Image)
dev_display (RegionScratches)
```


### Step-by-Step Explanation

1. **Initialization and Setup**:
   - `dev_update_off()`: Turns off automatic updates for the display window to prevent flickering during processing.
   - `dev_close_window()`: Closes any existing graphics window.
   - `read_image(Image, 'surface_scratch')`: Loads the input image containing the surface with potential scratches.
   - `invert_image(Image, ImageInverted)`: Inverts the image to emphasize the scratches, assuming they appear as dark lines.
   - `get_image_size(Image, Width, Height)`: Retrieves the image's dimensions.
   - `dev_open_window(...)`: Opens a new graphics window to display the images and results.
   - `set_display_font(...)`: Sets the font for displaying messages in the window.
   - `dev_display(Image)`: Displays the original image in the window.

2. **Optimization for FFT**:
   - The script includes comments suggesting optimizing the speed for the Fourier Transform (FFT) process, but these lines are commented out.

3. **Frequency Domain Processing**:
   - `gen_sin_bandpass(ImageBandpass, 0.4, ...)`: Generates a sine bandpass filter, which will allow only a certain range of frequencies to pass through. The parameter `0.4` sets the frequency range of the bandpass filter.
   - `rft_generic(ImageInverted, ImageFFT, 'to_freq', ...)`: Transforms the inverted image into the frequency domain using the Fourier Transform.
   - `convol_fft(ImageFFT, ImageBandpass, ImageConvol)`: Applies the bandpass filter to the frequency domain representation of the image, enhancing the high-frequency components (scratches).
   - `rft_generic(ImageConvol, Lines, 'from_freq', ...)`: Transforms the filtered image back to the spatial domain, emphasizing the scratches.

4. **Post-processing and Segmentation**:
   - `threshold(Lines, Region, 5, 255)`: Segments the enhanced scratches by thresholding, creating a binary region where scratches are marked.
   - `connection(Region, ConnectedRegions)`: Splits the segmented regions into connected components.
   - `select_shape(ConnectedRegions, SelectedRegions, 'area', ...)`: Selects regions based on their area to filter out noise or irrelevant regions.
   - `dilation_circle(SelectedRegions, RegionDilation, 5.5)`: Dilates the selected regions to enhance the visibility of the scratches.
   - `union1(RegionDilation, RegionUnion)`: Combines all the dilated regions into one.
   - `reduce_domain(Image, RegionUnion, ImageReduced)`: Reduces the domain of the image to the detected regions, which contains scratches.
   - `lines_gauss(ImageReduced, LinesXLD, ...)`: Extracts line-like structures (scratches) using a Gaussian filter.
   - `union_collinear_contours_xld(...)`: Merges collinear contours to form longer lines, representing scratches.
   - `select_shape_xld(...)`: Further refines the selection of line segments based on their length.
   - `gen_region_contour_xld(SelectedXLD, RegionXLD, ...)`: Converts the selected contours into regions.
   - `union1(RegionXLD, RegionUnion)`: Combines all the regions into a single region.
   - `dilation_circle(RegionUnion, RegionScratches, 10.5)`: Applies another dilation to the regions to ensure the scratches are fully captured.

5. **Display Results**:
   - `dev_set_draw('margin')`: Sets the drawing mode to draw only the edges of regions.
   - `dev_set_line_width(3)`: Sets the line width for drawing.
   - `dev_set_colored(12)`: Sets the number of colors for display.
   - `dev_display(Image)`: Displays the original image.
   - `dev_display(RegionScratches)`: Overlays the detected scratches on the image.

This script utilizes advanced image processing techniques, including frequency domain filtering and morphological operations, to detect and highlight scratches on a surface. The frequency domain filtering helps enhance fine details (like scratches), while the subsequent morphological operations refine the detection and visualization of these defects.