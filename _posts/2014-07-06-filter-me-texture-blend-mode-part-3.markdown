---
layout: post
title: 'Filter Me: Texture & Blend Modem - Part 3'
date: '2014-07-06 12:33:40'
---

In two previous parts of Filter Me series tutorial, I covered some useful technique for applying a filter to photo. Here is a shortcut for you: [Part 1](http://nghiatran.me/index.php/filter-me-tone-curve-part-1/ "Part 1") and [Part 2](http://nghiatran.me/index.php/filter-me-color-lookup-table-part-2/ "Part 2").

If you a new guy, I strongly recommend you should spend couple hours for reading Part 1 and part 2, and ensure you have some knowledge about Core Image and GPUImage before continue.

# Before reading

In this blog, I use GPUImage in entity project. Rather than being an introduction to the GPUImage framework, I assume you are familiar with the basics and focused on the way to implement filter function like Instagram does.

For saving your time, I google some nice blog about GPUImage :

- [GPUImage](https://github.com/BradLarson/GPUImage "GPUImage") by Brad Larson
- [Build a Photo App with GPUImage ](http://code.tutsplus.com/tutorials/build-a-photo-app-with-gpuimage--mobile-12223 "Build a Photo App")by Mark Hammonds
- [Image Processing using GPUImage](http://www.theappguruz.com/tutorial/ios-image-processsing-using-gpuimage-framework/ "Image Processing using GPUImage") by Swati Choksi.


# What next?

To create an app like Instagram’s filters, I will cover everything we need to implement it, including apply texture and adjust blend mode into the photo.

In brief, what we will do, I have shown you a gallery of photos I did in a hour ago.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_SlideShow_9.jpg?resize=640%2C640&ssl=1)

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_SlideShow_8.jpg?resize=640%2C640&ssl=1)

Steps to do:

1. Download vintage texture brush from Google
2. Using Photoshop and create your own texture ( by combining brush and blend mode)
3. Apply this texture to your photo by using GPUImage.
4. Refactor code, bring animation for transiting photo.

The first photo is an original photo. As you see, each next photo has a different style, by adding textures like grunge, you can turn your photo like Retro, Vintage style.

# Step 1 – Prepare yourself

Before starting work, We should download awesome brush for vintage style in google. Here is what I find [bursh](http://speckyboy.com/2012/02/20/20-subtle-and-soft-textured-photoshop-brush-packs-225-brushes/ "Awesome brush").

Download what you want, and run it.

![FilterMe_Part3_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_1.png?resize=410%2C54)


# Step 2 – Photoshop is God

And open Photoshop app and create a new canvas with 640px as width and height.

![FilterMe_Part3_2](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/Screen-Shot-2014-07-06-at-13.24.49.png?resize=607%2C425)

Ensure you choose Color Mode is RGB Color, not CMYK.

Drag this [original photo](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/originalPhoto.jpg "Original Photo") to your canvas, and create new layer for your texture

![FilterMe_Part3_4](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_4.png?resize=201%2C121)

Next, open your brush. As you see, there are many brush texture in your gallery

![FilterMe_Part3_5](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_5.png?resize=442%2C419)

Pick one, adjust a size and draw to photo whatever you like. If you want to improve brush’s effect, you can choose blend mode in the Layer panel.

![FilterMe_Part3_SlideShow_3](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/FilterMe_Part3_SlideShow_3.jpg?resize=640%2C640)

Above photo is the result when I combine brush which I downloaded from the Internet with** Blend mode is Linear Burn.**

Feel free for adjusting option before saving.

## Save your texture

You must save your texture as PNG, and make sure you just turn “the eye” of texture layer. Turn off “the eye” of the original photo.

![texture_linearBurnBlend_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/07/texture_linearBurnBlend_1-300x300.png?resize=300%2C300) 

Bunch of texture which I have created and collected for you.

[Textures](https://github.com/NghiaTranUIT/FilterMe-Texture)

# Step 3 – GPUImage

Now is a time we turn miracle to your app. Just drink a sip coffee and see what will happen next ;]

Instead of talking about overview GPUImage framework again, I will mention directly what is inside GPUImage we should deal with it.

The GPUImage library provides plenty of useful classes for blending texture with the photo. Such as **GPUImageNormalBlendFilter** for normal blend, **GPUImageOverlayBlendFilter** for overlay blend, **GPUImageMultiplyBlendFilter** for multiply blend and so on. All of them is subclass by GPUImageTwoInputFilter class.

Here is snippet code for you

```objc
  GPUImageNormalBlendFilter  *filter = [[GPUImageNormalBlendFilter alloc] init];
    [filter useNextFrameForImageCapture]; // 1
 
    // 2
    // GPUImage picture
    UIImage *imageOriginal = [UIImage imageNamed:@"originalPhoto.jpg"];
    UIImage *imageOverlay = [UIImage imageNamed:filterName];
    
    GPUImagePicture *picture_1 = [[GPUImagePicture alloc] initWithImage:imageOriginal];
    GPUImagePicture *picture_2 = [[GPUImagePicture alloc] initWithImage:imageOverlay];
    
    // 3 - Target
    [picture_1 addTarget:filter];
    [picture_1 processImage];
    
    [picture_2 addTarget:filter];
    [picture_2 processImage];
    
    // 4 - Blend
    UIImage *destinationPhoto = [filter imageFromCurrentFramebuffer];
```

1. The first step, we should determine what is blend mode match with texture. For simply, I create object filter as GPUImageNormalBlendFilter class. And call [filter useNextFrameForImageCapture] for static image processing.
2. Convert UIImage object to GPUImagePicture
3. Add target and call -processImage to processing two images.
4. Call -imageFromCurrentFramebuffer to get destination image. If your final photo wrong orientation, call imageFromCurrentFramebufferWithOrientation: and pass original photo’s orientation.

If you are confuse about **step 1**, please read [this blog](http://www.sunsetlakesoftware.com/2014/03/17/switching-gpuimage-use-cached-framebuffers "Blog"), in the newest version GPUImage.

Brad Larson changed the way for processing image because some memory structure issue, and make sure you MUST call `-useNextFrameForImageCapture`. If not, NSAsset will be fired.


# Note

All of the method we called above is run in the main thread, if you process large image, it can block your main thread and UI will not responsive.

You should consider calling it in background thread instead.

In short, this video below will show you:  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="415" src="//www.youtube.com/embed/nJ0UQQ8DfV0" width="600"></iframe>


# Step 4 – Adding animation

Step 4 is just option step. When you finished step 3, I think you can understand enough to implement. But if you wanna gain more User Experience, we should animate photo when applying new filter.

```objc
CABasicAnimation *fadeOutAnimation = [CABasicAnimation animationWithKeyPath:@"contents"];
    fadeOutAnimation.fromValue = (__bridge id) _previousImage;
    fadeOutAnimation.toValue = (id) destinationPhoto.CGImage;
    fadeOutAnimation.duration = 1.6f;
    fadeOutAnimation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    fadeOutAnimation.fillMode = kCAFillModeForwards;
    fadeOutAnimation.removedOnCompletion = NO;
    
    // Delegate
    FeBasicAnimationBlock *block = [[FeBasicAnimationBlock alloc] init];
    block.blockDidStart = ^{
        _tableView.userInteractionEnabled = NO;
    };
    block.blockDidStop = ^{
        _tableView.userInteractionEnabled = YES;
        _previousImage = destinationPhoto.CGImage;
    };
    
    // Set delegate
    fadeOutAnimation.delegate = block;
    
    [_imageView.layer addAnimation:fadeOutAnimation forKey:@"aa"];
```

I created CABasicAnimation object and add to \_imageView.layer. If you are familiar with CABasicAnimation, this is not problem for you. But not, I explain briefly.

1. Because I want to change Layer’s contents property, so I use CABasicAnimation instead of  [UIView animate…].
2. FromValue is the previous Image
3. ToValue is a new filtered image.
4. Adjust some property like duration, timingFunction, fillMode, …
5. Reuse FeBasicAnimationBlock which I created in a couple month ago, and pass itself is CABasicAnimation’s delegate.
6. Disable / Enable table view while animation is animating.
7. Adding animation to layer.

Everything is done.  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="415" src="//www.youtube.com/embed/8RetiD4N1IE" width="600"></iframe>

# Project sample code

[Sample Code](https://github.com/NghiaTranUIT/FilterMe-Texture)

# What I covered

So, There are many famous editing photo app in Appstore often use techniques or combine it to make new filter. If you want to build up the beautiful filter, you should spent more time to create new filter, textured sample in photoshop before coding.

Have a questions ? Drop it here ;]


