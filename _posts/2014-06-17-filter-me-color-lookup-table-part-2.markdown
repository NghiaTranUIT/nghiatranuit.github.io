---
layout: post
title: 'Filter Me: Color Lookup Table - Part 2'
date: '2014-06-17 04:45:11'
tags:
- filter
- media
- video-2
---

Hi, We will pick up right where we left off in Part one

In the last part, I covered to use curve file which was built by Photoshop, and how to apply it to your photo.

And now, we will deal with advanced technique. Applying many complex curve and selective-color to turn your photo to beautiful photo ever.

Standard approach to creating photo-like Instagram in last part. We just apply one curve to photo. So, what if do I wanna apply more curve or selective-color in photoshop? Should I apply each curve by step to step in iOS app? With professional designer, they can adjust more curve than one at the time.

Here is list action which designer often do :

![FilterMe_Part2_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_1.png?resize=281%2C611)

Sounds simple enough, but in practice, I found it quite hard to implement Curves and Selective Color in my iOS app exactly the way Photoshop does it. That should be crazy if I and you as iOS developer will apply each step by code. There is must solution for an implement that.


# Color Lookup Table – LUT

LUT is a recuse for iOS dev ;]

In the field of image processing, when we want to adjust the color of an image, often used Color Lookup Table (referred ColorLUT) technology.

Here is a simple example, if we want to make the R-value of each pixel in the image into the original 0.3-fold, the basic approach is to put the R-value of each pixel is multiplied by 0.3, assuming the size of the image is 1024×768, then the total to 786,432 times a floating-point multiplication.

If we start the first result to build a table, all the processed color values (R-value is 0.3 times) after recording it, and then take the color value of each pixel in the table, the color values obtained after processing, So long as we do 786432 lookup action, faster than many floating-point operations.In fact most of the color adjustment algorithms many complexes than this example, and therefore better highlight the high efficiency of the look-up table.

Here’s a sample RGB lookup table:

![FilterMe_Part2_2](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_2.png?resize=512%2C512)

So, theory enough, take action.

Open photoshop, load [this photo](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Filter-Me_sample.jpg "Sample"), and give it to your designer. They will make some adjustment to desired effect.

![FilterMe_Part2_3](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_3.png?resize=850%2C532)

But now, we are an iOS dev, I take a tour around google and seek some action Photoshop.

Here is my found: [http://www.deviantart.com/art/ATP-Dylan-Action-for-Photoshop-461167798](http://www.deviantart.com/art/ATP-Dylan-Action-for-Photoshop-461167798)

You can find by yourself, but make sure: There is no effects use additional textures, blur, sharpen and some other filters which will not work with a simple lookup texture approach. Your action should just adjust curve, layer, selective-color.

Download this action and run it.

![FilterMe_Part2_4](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_4.png?resize=338%2C309) 

And here is my result

![FilterMe_Part2_5](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_5.png?resize=850%2C531) 

Look amazing ;]

But this is work of a designer, my job is to bring this miracle to iOS app.

Continue, Download [original LUT](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_OriginalLUT.png "Original LUT")

Open this with photoshop, take actions like I did before to original LUT.

And save LUT as ProcessedLUT.png (Must save as PNJ extension)

![FilterMe_Part2_ProcessedLUT](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_ProcessedLUT.png?resize=359%2C359)

So, we have finished the first stage as an important stage in this blog. Next, we work with iOS.

For simple, I create a category for you. Feel free to download [it here](http://www.mediafire.com/download/7heho6i2pkd0pm7/CIFilter+LUT.zip "Category CIFilter+LUT").

Ex:

```objc
// Load photo
    UIImage *photo = [UIImage imageNamed:@"Filter-Me_sample.jpg"];
    
    // Create filter
    CIFilter *lutFilter = [CIFilter filterWithLUT:@"FilterMe_Part2_ProcessedLUT" dimension:64];
    
    // Set parameter
    CIImage *ciImage = [[CIImage alloc] initWithImage:photo];
    [lutFilter setValue:ciImage forKey:@"inputImage"];
    CIImage *outputImage = [lutFilter outputImage];
    
    CIContext *context = [CIContext contextWithOptions:[NSDictionary dictionaryWithObject:(__bridge id)(CGColorSpaceCreateDeviceRGB()) forKey:kCIContextWorkingColorSpace]];
    
    UIImage *newImage = [UIImage imageWithCGImage:[context createCGImage:outputImage fromRect:outputImage.extent]];
    
    _imageView.image = newImage;
```

Just remember to add CoreImage and OpenGLES framework in your project.

Here is my photo after process by LUT

![FilterMe_Part2_6](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FilterMe_Part2_6.png?resize=577%2C399)

# Another way
You can easily implement LUT process in GPUImage. Just use **GPUImageLookupFilter **class.

# Source code
[Github](https://github.com/NghiaTranUIT/FilterMe_Part_2)

# Where to go from here ?

After reading carefully this blog, I think you have some knowledge about image processing and how to bring Photoshop to the iOS world.

To make photo similar Instagram do, in the 3rd part, I will show you the way to add more texture to photo. Make it look like vintage or retro style.

Thank for reading ;]

 

 


