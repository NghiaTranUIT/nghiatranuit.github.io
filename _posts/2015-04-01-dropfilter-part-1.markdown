---
layout: post
title: Drop Filter
date: '2015-04-01 10:54:15'
---

It’s brand new series about filter on camera. Yeah, actually, it’s 4th blog i’ve written :D.

So I sure there is question in your mind. Why only mention about filter camera ? Why don’t you write another things, such as algorithm or common mistake when developing app or anything else ?

Yup, The answer is so simple, because i really love it :D, it makes worst things to better. poor photographer to professional, make everything see throught camera looks amazing 😀

Here is achievement, please take a look to know what we will do next.

<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/qGRTbdT_qxs?feature=oembed" width="750"></iframe>

^ So, is it awesome :)), are you ready to implement it ?

## GPUImage

On entire blog, we’ll deal with [GPUImage](https://github.com/BradLarson/GPUImage "GPUImage"). Rather than being introduction to GPUImage, this blog assumes you are familiar with GPUImage, if not, just spent few mins and google. There is bunch of great articles talk about it, no reason to say again.

And if you’re step-by-step guy. Please create new projec with your Xcode, and follow my instruction 😀


## Install GPUImage from CocoaPods

Navigate to root of your project. Create Podfile with content below

```
platform :ios,'7.0' pod 'GPUImage', '~> 0.1'
```

Open terminal, cd to project’s directory and run

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropfilter_1.png)

```
pod install
```

…… Leave it, pod will do all for you, Just go away or make couple of coffee for yourself.

When it’s finished, we will see like this

[![dropfilter_2](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropfilter_2.png?resize=440%2C269)]

and open your **.DropFilter.xcworkspace** instead of **.xcodeproj**.


## Write simple live filter camera

Before going deeply, we should build simple live filter camera first to understand clearly.

Here is anatomy. We’re going to put GPUImageView on top of controller’s view.

![dropFilter_3](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_3.jpg?resize=525%2C424)

GPUImageView is subclass of UIView, so it has useful UIView’s property. It just warps all of code to handle render data which come from camera or filter.

Open storyboard and add UIView on root view controller, and add constrains. To guarantee camera screen will full-screen even on any device.

![dropFilter_4](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_4.png?resize=221%2C295)

Switch to Identity Inspector and configure. Class is **GPUImage** and Label is **Top Camera View**

![dropFilter_5](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_5.png?resize=244%2C310)

It’s done with interface, we’re going to implement code.

1 – Create new outlet from GPUImage with name **topCameraImageView**.

2 – Added helper code in interface side.

We use GPUImageStillCamera – subclass of GPUVideoCamera. By reuse it, we don’t have any pain when working directly with AVFoundation and OpenGL ES. Many thanks to BrandLarson.

GPUImageGrayscaleFilter is just simple things to help us apply grayscale to camera easily.

```objc
// GPUImage View 
@property (weak, nonatomic) IBOutlet GPUImageView *topCameraImageView; 
// Still Camera 
@property (strong, nonatomic) GPUImageStillCamera *stillCamera; 
// Filter 
@property (strong, nonatomic) GPUImageGrayscaleFilter *grayscaleFilter;
```

3 – Configure them

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    [self initCommon];
    
    [self configureCamera];
    
    [self configureFilter];
    
    [self configureImageView];
}
 
- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}
-(BOOL) prefersStatusBarHidden
{
    return YES;
}
#pragma mark - Cycle view
-(void) viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    
    [_stillCamera startCameraCapture];
}
-(void) viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    
    [_stillCamera stopCameraCapture];
}
#pragma mark - Init
-(void) initCommon
{
    
}
-(void) configureCamera
{
    // Init Still camera
    _stillCamera = [[GPUImageStillCamera alloc] initWithSessionPreset:AVCaptureSessionPresetHigh cameraPosition:AVCaptureDevicePositionBack];
    _stillCamera.outputImageOrientation = UIInterfaceOrientationPortrait;
}
-(void) configureFilter
{
    // Gray filter
    _grayscaleFilter = [[GPUImageGrayscaleFilter alloc] init];
}
-(void) configureImageView
{
    // Top
    [_stillCamera addTarget:_grayscaleFilter];
    [_grayscaleFilter addTarget:_topCameraImageView];
}
```

^ as you read, it’s pretty straightforward. We created instance of camera with hight quality, back camera and Portrait mode as default.

Here is flow of raw data in app. GPUImageStillCamera gets raw data from camera and sent to sub-filter to process it. Otherwise, GPUImage implement by Decorator Pattern. It means you could add many filters as you expect. 

Finally, just send processed data to GPUImageView, and it will be present on screen.

![dropFilter_9](https://i2.wp.com/128.199.214.43/wp-content/uploads/2015/04/dropFilter_9.jpg?resize=634%2C229)

So, Build and run on your iphone, here is result what we did.

<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/hDPb1HE-yeI?feature=oembed" width="750"></iframe>

## Two live filters

We’re moving to harder part in this blog. From now, we will apply two live filters to camera at same time. It’s said One picture than million words. I prepared some useful photos to explain.

Flow of data : Instead of using one flow, currently, we use 2 flows. One for GrayScaleFilter, one for AmatorkaFilter. All of them will process and send to Top/BottomImageView and present on screen simultaneously.

I admit it costs x 2 CPU/GPU than one filter. But it isn’t problem now, 4S iphone still has enough power to show it fluently, around 30FPS. I benchmarked it, trust me 😀

![dropFilter_11](https://i2.wp.com/128.199.214.43/wp-content/uploads/2015/04/dropFilter_11.jpg?resize=850%2C207)]

And here is view hierarchy. We create new GPUImageView ( call as bottomImageView), and maskLayer.

![dropFilter_12](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_12.jpg?resize=633%2C451)

Mask Layer is just CALayer with frame = { 0 , 0 , width / 2 , height }, and assign to topImageView’s layer. By using it as mask of TopImageView, the mask will hide the portion of the original layer. So we could see bottomImageView.

![dropFilter_13](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_13.jpg?resize=788%2C448)

Theory is enough, time to implement it by your hand.

1 – Open storyboard, add new UIView (call as **bottomImageView**, subclass from **GPUImageView**), and below TopImageView.

![dropFilter_14](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_14.jpg?resize=311%2C258)

2 – Open viewcontroller.m and add it as outlet, new mask Layer and new filter.

```objc
// GPUImage View
@property (weak, nonatomic) IBOutlet GPUImageView *bottomCameraView;
 
// Filter
@property (strong, nonatomic) GPUImageAmatorkaFilter *amatorkaFilter;
 
// Mask
@property (strong, nonatomic) CALayer *maskLayer;
```

Finally, we change implement section with new code.

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    [self initCommon];
    
    [self configureCamera];
    
    [self configureFilter];
    
    [self configureImageView];
    
    [self initMask];
}
 
-(void) configureFilter
{
    // Gray filter
    _grayscaleFilter = [[GPUImageGrayscaleFilter alloc] init];
    
    // Amatorka filter
    _amatorkaFilter = [[GPUImageAmatorkaFilter alloc] init];
}
-(void) configureImageView
{
    // Top
    [_stillCamera addTarget:_grayscaleFilter];
    [_grayscaleFilter addTarget:_topCameraImageView];
    
    // Botom
    [_stillCamera addTarget:_amatorkaFilter];
    [_amatorkaFilter addTarget:_bottomCameraView];
}
-(void) initMask
{
    if (!_maskLayer)
    {
        _maskLayer = [CALayer layer];
        _maskLayer.frame = CGRectMake(0, 0, self.view.bounds.size.width / 2, self.view.bounds.size.height);
        _maskLayer.backgroundColor = [UIColor whiteColor].CGColor;
        
        // Add
        _topCameraImageView.layer.mask = _maskLayer;
        _topCameraImageView.layer.masksToBounds = YES;
    }
}
```

In configureFilter, we initial GPUImageAmatorkaFilter. In configureImageView, still initial bottomImageView. Please notice we should assign `_amatorkaFilter` as `_camera’s target`, and bottomImagView is amatorkaFilter’s target.


Magic happens in initMask method. We created new CALayer and assign it into `_topCamraImageView’s layer`.
Because Top/Bottom ImageView is same frame, it’s just overlay, two filter is rendered simultaneously, so people can’t notice the magic inside. 

It works as our expectation 😀

![dropFilter_15](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_15.jpg?resize=639%2C547)

On action

<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/DnQIbRQJ0bc?feature=oembed" width="750"></iframe>


## Make it’s better

So, if you passed two section above, i guarantee you’re understand what i’m doing. But we shouldn’t release app with only noob feature. We should think and improve it professionally …..

I’m thinking about “What happen if we could experience or preview filters by our finger ?” and ” Instead of rectangle, should we try another shape in mask layer ? ”

Yeah, be inde-dev, we should ask ourself with those similar question. Each unique feature will attract people. Think difference is key of success.

**1 – Triangle mask**  
 I don’t prefer rectangle mask anymore, try triangle instead 😀 .So replace your initMask with new one.

```objc
-(void) initMask
{
    if (!_maskLayer)
    {
        CGFloat width = self.view.bounds.size.width;
        CGFloat height = self.view.bounds.size.height;
        
        _maskLayer = [CAShapeLayer layer];
        _maskLayer.frame = CGRectMake(0, 0, width * 2, height);
        _maskLayer.backgroundColor = [UIColor clearColor].CGColor;
        
        // Bezier path
        UIBezierPath *triangle = [UIBezierPath bezierPath];
        [triangle moveToPoint:CGPointZero];
        [triangle addLineToPoint:CGPointMake(width, 0)];
        [triangle addLineToPoint:CGPointMake(width * 2, height)];
        [triangle addLineToPoint:CGPointMake(0, height)];
        [triangle addLineToPoint:CGPointZero];
        
        // Add to mask layer
        _maskLayer.path = triangle.CGPath;
        _maskLayer.fillColor = [UIColor whiteColor].CGColor;
        
        // Translate to center
        _maskLayer.anchorPoint = CGPointZero;
        _maskLayer.position = CGPointMake( - width * 2, 0);
        
        // Add
        _topCameraImageView.layer.mask = _maskLayer;
        _topCameraImageView.layer.masksToBounds = YES;
    }
}
```

![dropFilter_16](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_16.jpg?resize=303%2C539)

**2 – User’s gesture**  
 As i said before, i wanna use my finger to preview filter as i want. it’ll be unique experience :D. Let add **UIPanGestureRecognize **and **FeBasicAnimationBlock** (helper for warping CABasicAnimation’s delegate to block)

**FeBasicAnimationBlock.h**

```objc
#import <Foundation/Foundation.h>
#import <QuartzCore/QuartzCore.h>
 
typedef void (^FeBasicAnimationDidStartBlock)();
typedef void (^FeBasicAnimationDidStopBlock)();
 
@interface FeBasicAnimationBlock : NSObject
@property (copy, nonatomic) FeBasicAnimationDidStartBlock blockDidStart;
@property (copy, nonatomic) FeBasicAnimationDidStopBlock blockDidStop;
 
-(void) animationDidStart:(CAAnimation *)anim;
-(void) animationDidStop:(CAAnimation *)anim finished:(BOOL)flag;
@end
```

**FeBasicAnimationBlock.m**

```objc
#import "FeBasicAnimationBlock.h"
 
@implementation FeBasicAnimationBlock
-(void) animationDidStart:(CAAnimation *)anim
{
    if (_blockDidStart)
        _blockDidStart();
}
-(void) animationDidStop:(CAAnimation *)anim finished:(BOOL)flag
{
    if (_blockDidStop)
        _blockDidStop();
}
@end
```

Switch back to ViewController.m

Add PanGesture into @interface

```
// Gesture
@property (strong, nonatomic) UIPanGestureRecognizer *panGesture;
```

And few new API

```objc
-(void) initGesture
{
    _panGesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handlePanGesture:)];
    [self.view addGestureRecognizer:_panGesture];
    
    self.topCameraImageView.userInteractionEnabled = YES;
    self.bottomCameraView.userInteractionEnabled = YES;
}
-(void) handlePanGesture:(UIPanGestureRecognizer *) sender
{
    CGPoint location = [sender locationInView:self.view];
    
    switch (sender.state)
    {
        case UIGestureRecognizerStateBegan:
        case UIGestureRecognizerStateChanged:
        {
            // Translate maskLayer
            // We should disable Implecit Animation when assign directly to property of layer
 
            CGFloat percent = location.x / self.view.bounds.size.width;
            
            [self setPositionWithoutImplicitAnimationAtTransfrom:CATransform3DMakeTranslation (self.view.bounds.size.width * 2 * percent, 0, 0)];
            
            break;
        }
        case UIGestureRecognizerStateCancelled:
        case UIGestureRecognizerStateFailed:
        case UIGestureRecognizerStateEnded:
        {
            CATransform3D transfrom = _maskLayer.transform;
            
            // m41 is x corrdinatation
            if (transfrom.m41 > self.view.bounds.size.width)
            {
                // Animate masklayer to right edge
                [self animationMaskLayerToTransform:CATransform3DMakeTranslation(self.view.bounds.size.width * 2, 0, 0)];
            }
            else
            {
                // Animate masklayer to left edge
                [self animationMaskLayerToTransform:CATransform3DMakeTranslation( 0, 0, 0)];
            }
            break;
        }
        default:
            break;
    }
}
 
-(void) animationMaskLayerToTransform:(CATransform3D) finalTransform
{
    CABasicAnimation *transalteAnimation = [CABasicAnimation animationWithKeyPath:@"transform"];
    transalteAnimation.toValue = (id)[NSValue valueWithCATransform3D:finalTransform];
    transalteAnimation.duration = 0.3f;
    transalteAnimation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    transalteAnimation.removedOnCompletion = NO;
    transalteAnimation.fillMode = kCAFillModeForwards;
    
    // Delegate
    FeBasicAnimationBlock *blockDelegate = [FeBasicAnimationBlock new];
    transalteAnimation.delegate = blockDelegate;
    
    __weak typeof(self) weakSelf = self;
    blockDelegate.blockDidStart = ^{
        typeof(self) strongSelf = weakSelf;
        
        // Disable gesture
        strongSelf.panGesture.enabled = NO;
    };
    blockDelegate.blockDidStop = ^{
        typeof(self) strongSelf = weakSelf;
        
        // Enable
        strongSelf.panGesture.enabled = YES;
        
        // remove
        [strongSelf.maskLayer removeAllAnimations];
        
        // Set final
        [strongSelf setPositionWithoutImplicitAnimationAtTransfrom:finalTransform];
    };
    
    [_maskLayer addAnimation:transalteAnimation forKey:@"animation"];
}
-(void) setPositionWithoutImplicitAnimationAtTransfrom:(CATransform3D ) transform
{
    [CATransaction begin];
    
    // Disable
    [CATransaction setDisableActions:YES];
    
    // Point
    _maskLayer.transform = transform;
    
    [CATransaction commit];
}
```

Finally, Run on your iphone, and preview live filter with your finger 😀

<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/0f3xzTNwRgE?feature=oembed" width="750"></iframe>


## Filter name

It would be great if we add some filter name label in each side. Of course, it must be translate depend on your finger. So we should implement some helper API, to calculator center of cross line.

Yeah, to achieve it, time to get your notebook and pencil to come back to high school.  
 Sin, cos,tan,cotan are waiting you 😀

It’s easy to understand what i wrote. The final answer is find position of O point. It’s center of cross line.

[![dropFilter_17](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_17-e1428221741695.jpg?resize=750%2C500)](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2015/04/dropFilter_17-e1428221741695.jpg)

Add two label into @interface

```objc
// Label
@property (strong, nonatomic) UILabel *grayscaleLbl;
@property (strong, nonatomic) UILabel *amatorkarLbl;
```

Initial two labels

```objc
-(void) initLabels
{
    if (!_grayscaleLbl)
    {
        _grayscaleLbl = [[UILabel alloc] init];
        _grayscaleLbl.text = @"Oldboy";
        _grayscaleLbl.font = [UIFont fontWithName:@"HelveticaNeue-Light" size:60];
        _grayscaleLbl.textColor = [UIColor whiteColor];
        _grayscaleLbl.backgroundColor = [UIColor clearColor];
        _grayscaleLbl.textAlignment = NSTextAlignmentCenter;
        
        [_grayscaleLbl sizeToFit];
    }
    if (!_amatorkarLbl)
    {
        _amatorkarLbl = [[UILabel alloc] init];
        _amatorkarLbl.text = @"Retro";
        _amatorkarLbl.font = [UIFont fontWithName:@"HelveticaNeue-Light" size:60];
        _amatorkarLbl.textColor = [UIColor whiteColor];
        _amatorkarLbl.backgroundColor = [UIColor clearColor];
        _amatorkarLbl.textAlignment = NSTextAlignmentCenter;
        
        [_amatorkarLbl sizeToFit];
    }
    
    _grayscaleLbl.alpha = 0;
    _amatorkarLbl.alpha = 0;
    
    [self.view addSubview:_grayscaleLbl];
    [self.view addSubview:_amatorkarLbl];
}
```

Add 2 line of code in next of UIGestureRecognizerStateChanged.

```objc
// Calculate O depend on maskLayer's transform
CGPoint center = [self centerPointerDependTransform:_maskLayer.transform];
 
// Set position of two labels     
[self setLabelsWithCenter:center];
```

And here is the math.

```objc
-(CGPoint) centerPointerDependTransform:(CATransform3D) transform
{
    // Translate lable
    if (transform.m41 > self.view.bounds.size.width)
    {
        CGFloat width = self.view.bounds.size.width;
        
        CGFloat GA = transform.m41 - width;
        CGFloat AC = width - GA;
        CGFloat AB = AC / cos(alphaAngel);
        
        CGFloat AO = AB / 2.0f;
        CGFloat y = sin(alphaAngel) * AO;
        CGFloat x = GA + cos(alphaAngel) * AO;
    
        return CGPointMake(x, y);
    }
    else
    {
        CGFloat height = self.view.bounds.size.height;
        
        CGFloat DB =  transform.m41;
        CGFloat belta = M_PI / 2 - alphaAngel;
        
        CGFloat AB = DB / sin(belta);
        
        CGFloat AO = AB / 2.0f;
        CGFloat x = sin(belta) * AO;
        CGFloat y = (height - cos(belta) * AB) + cos(belta) * AO;
        
        NSLog(@"DB = %.2f",DB);
        
        return CGPointMake(x, y);
    }
    return CGPointZero;
}
```

and

```objc
-(void) setLabelsWithCenter:(CGPoint) center
{
    // Set center
    _grayscaleLbl.center = center;
    _amatorkarLbl.center = center;
    
    // Rotate
    CATransform3D t1 = CATransform3DIdentity;
    t1 = CATransform3DTranslate(t1, 40, 0, 0);
    t1 = CATransform3DRotate(t1, alphaAngel, 0, 0, 1);
    
    CATransform3D t2 = CATransform3DIdentity;
    t2 = CATransform3DTranslate(t2, -40, 0, 0);
    t2 = CATransform3DRotate(t2, alphaAngel, 0, 0, 1);
    
    _amatorkarLbl.layer.transform = t1;
    _grayscaleLbl.layer.transform = t2;
    
    // Alpha
    if (center.x > self.view.bounds.size.width / 2)
    {
        _grayscaleLbl.alpha = 1;
        
        CGFloat GA = _maskLayer.transform.m41 - self.view.bounds.size.width;
        _amatorkarLbl.alpha = 1 - (GA / self.view.bounds.size.width);
        
    }
    else
    {
        _amatorkarLbl.alpha = 1;
        
        CGFloat GA = _maskLayer.transform.m41 - self.view.bounds.size.width;
        GA = fabs(GA);
        _grayscaleLbl.alpha = 1 - (GA / self.view.bounds.size.width);
    }
}
```

On action  
<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/qGRTbdT_qxs?feature=oembed" width="750"></iframe>

## What’s next ?

“That’s one small step for you, one giant leap for your career” – Hear similar with Neli Armstrong’s quote when he was putting step in moon  😀 ? Yup, All we did is just is small steps, we if we understand clearly, we could build awesome live filter by your-self 😀

Many things to improve, complex instagram filter and swipe to preview many live filters in camera view 😀 Here is achievements i’ve done for 1 months 😀

<iframe allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="563" src="https://www.youtube.com/embed/lNbbB9zqBOI?feature=oembed" width="750"></iframe>

Thanks for reading 😀
[Download Github](https://github.com/NghiaTranUIT/DropFilter)
