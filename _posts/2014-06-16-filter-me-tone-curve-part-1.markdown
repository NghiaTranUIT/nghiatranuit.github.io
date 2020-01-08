---
layout: post
title: 'Filter me: Tone Curve - Part 1'
date: '2014-06-16 14:37:20'
tags:
- filter
- media
- video-2
--- 

 Hello guy, in this blog we will discuss some technique for filtering photo like instagram.

Nowaday, If you take a tour around AppStore, you will realize more than 80% app which has filter photo or video ability. This is the must-have featured in your app especially social app or photo video editor app.

If you are fresh developer like me before, I ensure you must have a question like that “How can I bring awesome featured to my app” or “Where can I find snippet code to implement it easily more than else ? ”

With more 1 year deal with image processing, I have some mysterious thing to show you. I will split to 3 part blog. Sort by level, from easy to advanced.

In this blog, I will cover some famous image-process Framework in the world, and the easy way to implement it.


## Core Image

Core Image is powerful framework let you apple easily filter to your photo such as modifying contact, hue, vibrance,…. Core Image was built with a ton of filter, include common filter and high-up filter.

Core Image mainly process your photo by CPU and GPU. So fast enough to process each frame in high quality video. With Core Image, you can apply chain of filters to make a whole-new filter. Each filter has own parameter to adjust behaviour, so you can built professional editor app.

But Core Image also some limit.


## GPUImage

GPUImage is very similar Core Image. The awesome framework was built by BradLarson.

You can check it out in at his [github](https://github.com/BradLarson/GPUImage "github").

The different things between them is GPUImage was build on top OpenGL ES. So GPUImage can take all powerful advantage of GPU. GPU provide many stuff for you to apply filter to photo, or filter real-time video.

For massively parallel operations like processing images or live video frames, GPUs have some significant performance advantages over CPUs. On an iPhone 4, a simple image filter can be over 100 times faster to perform on the GPU than an equivalent CPU-based filter.

You can create own custom filter with GPUImage, but you must have a little knowledge about OpenGL ES and OpenGL Shading Language.

Here is a chart comparison between GPUImage vs Core Image by Brad Larson

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/chart_1.png)

You see, in case processing video, GPUImage took 2,5ms to upload frame from camera, apply gamma filter and display to screen, versus 106 ms for the same operation using Core Image.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/chart_2.png)

One more time, GPUImage process photo faster 4x more than Core Image in some common case.


## Limit

So, Is GPUImage the best choice for editor iOS app ?

No, everything always have two-side. When I deal with GPUImage, I often get error when build project which add GPUImage static library in 64-bit chip. And get many warning if I add GPUImage as independent project. You can ignore warnings, but It make too much noise.

For customize filter, you must write custom filter by OpenGL Shading Language which is low-level language.

What about Core Image ?

Core Image also include ton of filter. But you can custom it by own. But to adding Core Image to project is very very simple.

Using like that :

```objc
// 1
NSString *filePath =
  [[NSBundle mainBundle] pathForResource:@"image" ofType:@"png"];
NSURL *fileNameAndPath = [NSURL fileURLWithPath:filePath];
 
// 2
CIImage *beginImage =
  [CIImage imageWithContentsOfURL:fileNameAndPath];
 
// 3
CIFilter *filter = [CIFilter filterWithName:@"CISepiaTone"
                              keysAndValues: kCIInputImageKey, beginImage,
                    @"inputIntensity", @0.8, nil];
CIImage *outputImage = [filter outputImage];
 
// 4
UIImage *newImage = [UIImage imageWithCIImage:outputImage];
self.imageView.image = newImage;
```

1.  Init Path for Image in bundle
2. Init CIImage
3. Init SepiaTone Filter, and configure some parameter
4. Get filtered image out.


## Pre-filter

If you want to re-create photo in iOS App within the filter, you can use the Core Image to implement a series of CIFilter. CIFilter offers can adjust the brightness, contrast and so a variety of special effects filters.

But there are mysterious approach, we can directly use Photoshop and other image processing software, directly adjust these color parameters, export some pattern, and use this pattern to apply photo again, We can dramatically speed up development.


## Tone Curve

Tone Curve is the simplest way to apply filter. The Tone Curve tool in photoshop is designed to allow you to modify the various light levels found within an image in a way that will give you greater control over the tonal range and contrast of your photograph.

As we capture our images we are capturing an array of light from the scene. From the darkest of the shadows to the whitest of the highlights the Tone Curve gives us a way of visually modifying how these levels of lights appear in the final image.

We use Photoshop to create own tone curve, and apply it to photo by Core Image.

Open Photoshop

![Curve_blog_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Curve_blog_1.png?resize=303%2C143)

 Take [Original Photo](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/toneCurve_photoshop_before.jpg "Original Photo") I prepared for you.

Open with photoshop

![Curve_blog_2](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Curve_blog_2.png?resize=829%2C518)

Go to Image -> Adjustments -> Curves …

![Curve_blog_3](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Curve_blog_3.png?resize=508%2C205)

Here is Tone Curve tool. You can easily tone of R G or G by drag each point. I created sample [Sample_Tone_Curve](http://www.mediafire.com/download/swbfvzvx6ckd6gg/customToneCurve.acv "Sample_Tone_Curve")  for you.

![Curve_blog_4](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Curve_blog_4.png?resize=396%2C466)

By changing each point in tone curve tool, we can see realtime filtered photo. And here is my result. 

**After**

![toneCurve_photoshop_before](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/toneCurve_photoshop_before.jpg?resize=744%2C495)

**Before**

![toneCurve_photoshop_after](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/toneCurve_photoshop_after.jpg?resize=744%2C496) 

Yep, the second photo look pretty than original. The finally, you should export to “Curve file”.


![Curve_blog_5](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/Curve_blog_5.png?resize=129%2C131)

You can apply “curve file” to your photo easily. Just download [GPUImage](https://github.com/BradLarson/GPUImage "GPUImage") here.

Add GPUImage to your project as independent project or build static library. Just a couple of code

```objc
-(UIImage *) imageByFitering:(UIImage *)image
{
    NSLog(@"filter with %@",_filterNameACV);
    
    GPUImageToneCurveFilter *curveFilter = [[GPUImageToneCurveFilter alloc] initWithACV:_filterNameACV];
    
    return [curveFilter imageByFilteringImage:image];
}
```

So, what about Core Image. Core Image provide “CIToneCurve” for dev. But there is no method that allow read data from “curve file”. If you still want implement it. You take a deep at [this link](http://www.adobe.com/devnet-apps/photoshop/fileformatashtml/ "this link"). This describe how Structure of Curve file was build. and you can read it by following instruction.

## Here we go

In first series blog about filter photo, I covered some technique that make your photo get beautiful. Built editor photo app as quickly as possible.

Feel free to drop your comment here ;]

 


