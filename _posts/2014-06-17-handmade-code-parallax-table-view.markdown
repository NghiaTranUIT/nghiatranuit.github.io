---
layout: post
title: 'Handmade Code: Parallax Table View - Part 1'
date: '2014-06-17 08:17:20'
---

This is the first blog about crafting customize control from scratch that I call with epic name “Handmade code” ;]


# Something to talk about UI / UX

Have you ever downloaded & opened a famous iOS app like Page by FB or something else ? Have you surprised or realized that these app never use “old-fashion” Apple’s control ?

Today is a new generation  , if you can implement new feature, I can too. It mean. there is no difference, no fence between you and me, same as a developer. So here a question, what thing can make user download my app instead your app ?

To answer this question, I decide make few survey with my friend. 80% answer is something similar about app’s appearance. If I am asked by you, I also give same answer.

Design UI / UX is a first stage when developing application. As an independent iOS developer, we should have some knowledge about design principle. But in this blog, I should NOT mention it. In the future, I will write some blog what take closer look about it.


# Parallax Effect

sParallax scrolling is a special scrolling technique in computer graphics, wherein background images move by the camera slower than foreground images, creating an illusion of depth in a 2D video game and adding to the immersion.

If you are game developer, parallax effect is in your hand.

But right now, I’m going to bring incredible effect to iOS app. Here is result.

Youtube :

<iframe allowfullscreen="allowfullscreen" frameborder="0" height="315" src="//www.youtube.com/embed/FyJJuCvBNTc" width="420"></iframe>


# Coding time

Theory enough, we should take a real action. I will instruct you build customize Table View with Parallax Effect.

Create new project as SingleView Templete,  in this sample, I name “FeParallaxTableView_SampleCode”. Be sure name your prefix as you like and select devices is iPhone.

![SampleCode_ParallaxTableView_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_1.png?resize=731%2C492)

Create class FeParallaxTableView subclass UITableView. This is main class we will deal all time.

![SampleCode_ParallaxTableView_3](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_3.png?resize=536%2C175)

Next, create new class call “FeCell” sublcass UITableViewCell. Make sure you do NOT select “Create XIB file” option and select iPhone in dropbox.

In fact, you need avoid creating XIB file. You should create cell programmatically

Because**You cannot change the frame of an object if you’ve put it through Interface Builder.**

![SampleCode_ParallaxTableView_4](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_4.png?resize=537%2C187)

Here project look like

![SampleCode_ParallaxTableView_5](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_5.png?resize=260%2C430)

# Photo Bundle

For saving your time, I prepared for you Photo Bundle. You can download it at [Mediafire](http://www.mediafire.com/download/l367m8hd4sfuwmr/PhotoBundle.zip)

Extract it, and see in there.

![SampleCode_ParallaxTableView_6](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_6.png?resize=417%2C181)

This bundle include 8 photos of big city in the world, and a Plist file. This plist file will help me parse data to app easily than more. If you open it, it simple a array contain name of string as each row.

![SampleCode_ParallaxTableView_7](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_7.png?resize=598%2C282)

# Delegation Pattern

FeParallaxTabbleView use pattern similar UITableView. It mean, we should have 2 protocol. One is DataSource with provide data for own custom table view, and one is Delegate has some useful method.

Now, open FeParallaxTableView.h and add some code :

```objc
@protocol FeParallaxTableViewDataSource;
@protocol FeParallaxTableViewDelegate;
 
@interface FeParallaxTableView : UITableView
 
// Delegate
@property (weak, nonatomic) id<FeParallaxTableViewDataSource> delegateParallax;
 
@end
 
 
////////////////
// Data source
@protocol FeParallaxTableViewDataSource <NSObject>
@required
 
// Image for cell at indexpath
-(UIImage *) FeParallaxTableView:(FeParallaxTableView *) sender imageForCellAtIndexPath:(NSIndexPath *) indexPath;
 
// Title for cell
-(NSString *) FeParallaxTableView:(FeParallaxTableView *) sender titleForCellAtIndexPath:(NSIndexPath *) indexPath;
 
// number Of Row
-(NSInteger) numberOfRowForParallaxCell;
 
@end
 
////////////////
// Delegate
@protocol FeParallaxTableViewDelegate <NSObject>
 
// Add late
 
@end
```

We have 2 important method in Data source here. As you this name call, this method will provide image for each cell at specific index path.

```objc
// Image for cell at indexpath
-(UIImage *) FeParallaxTableView:(FeParallaxTableView *) sender imageForCellAtIndexPath:(NSIndexPath *) indexPath;
```

and the rest method will provide title for each cell

```objc
// Title for cell 
-(NSString *) FeParallaxTableView:(FeParallaxTableView *) sender titleForCellAtIndexPath:(NSIndexPath *) indexPath;
```

Switch to .m file

We override initalization method.

```objc
- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self)
    {
        // Initialization code
        
        self.delegate = self;
        self.dataSource = self;
        self.separatorStyle = UITableViewCellSeparatorStyleNone;
        
        // Register
        [self registerClass:[FeCell class] forCellReuseIdentifier:kFeCellID];
    }
    return self;
}
```

Make sure your already import FeCell and conform TableView’s protocol. And define new const for cell’s ID.

```objc
#define kFeCellID @"FeCell"
```

Add data source’s method

```objc
-(NSInteger) numberOfRowsInSection:(NSInteger)section
{
    return 1;
}
-(CGFloat) tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    return 180;
}
-(NSInteger) tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    NSUInteger numberOfCell = [_delegateParallax numberOfRowForParallaxCell];
    return numberOfCell;
}
-(UITableViewCell *) tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    FeCell *cell = [tableView dequeueReusableCellWithIdentifier:kFeCellID forIndexPath:indexPath];
    
    return cell;
}
-(void) tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
 
}
```

Here we provide number of cell via method numberOfRowForParallaxCell, and return 1 section.

# FeCell

Now, switch FeCell.m file. Add some property

```objc
@interface FeCell ()
// Photo
@property (strong, nonatomic) UIImageView *photoView;
 
// Title
@property (strong, nonatomic) UILabel *titleLbl;
@end
```

and add 2 helper methods in .h file.

```objc
-(void) configureWithPhoto:(UIImage *) image title:(NSString *) title;
-(void) configureFramePhoto:(CGRect) frame;
```

and .m file.

```objc
-(instancetype) initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
{
    self = [super initWithStyle:style reuseIdentifier:reuseIdentifier];
    if (self)
    {
        [self initSubView];
    }
    return self;
}
-(void) initSubView
{
    /////////////
    // Photo
    _photoView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 320, 568)];
    _photoView.contentMode = UIViewContentModeScaleAspectFill;
    
    ////////////
    // Title
    _titleLbl = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 320, 180)];
    _titleLbl.textAlignment = NSTextAlignmentCenter;
    _titleLbl.font = [UIFont systemFontOfSize:44];
    _titleLbl.textColor = [UIColor whiteColor];
    
    [self.contentView addSubview:_photoView];
    [self.contentView addSubview:_titleLbl];
}
-(void) configureWithPhoto:(UIImage *)image title:(NSString *)title
{
    _photoView.image = image;
    _titleLbl.text = title;
}
-(void) configureFramePhoto:(CGRect)frame
{
    _photoView.frame = frame;
}
```

Do you notice I set frame for photoView with 568 px as height ? There is a reason for this.


# First testing

We have just implemented some necessary method in FeParallaxTableView and FeCell for the first test. So the next step, we should test it, make sure it work ;]

Go back to FeViewController.m file. Import two own file and conform FeParallaxTableView’s data source. And copy this code to interface :

```objc
// Table View
@property (strong, nonatomic) FeParallaxTableView *tableView;
 
// Arr of image
@property (strong, nonatomic) NSMutableArray *arrPhotos;
 
// Arr of title
@property (strong, nonatomic) NSMutableArray *arrTitles;
 
-(void) initPhoto;
-(void) initTableView;
```

As your expectation, we should read City.plist and parse data to Photo array and Title Array.

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
	// Do any additional setup after loading the view, typically from a nib.
    
    [self initPhoto];
    
    [self initTableView];
}
 
-(void) initPhoto
{
    // Get photo from plist file
    // Path
    NSString *path = [[NSBundle mainBundle] pathForResource:@"City" ofType:@"plist"];
    
    // - 1
    _arrTitles = [NSArray arrayWithContentsOfFile:path];
    
    // - 2
    _arrPhotos = [NSMutableArray arrayWithCapacity:_arrTitles.count];
    
    // - 3
    for (NSString *namePhoto in _arrTitles)
    {
        NSString *fullNamePhoto = [NSString stringWithFormat:@"%@.jpg",namePhoto];
        UIImage *image = [UIImage imageNamed:fullNamePhoto];
        
        [_arrPhotos addObject:image];
    }
    
}
-(void) initTableView
{
    _tableView = [[FeParallaxTableView alloc] initWithFrame:self.view.bounds];
    _tableView.delegateParallax = self;
    [self.view addSubview:_tableView];
}
```

1.  Just part plist file to Title array
2. Init Photo array with capacity equal with titleArray’s count.
3. Init UIImage with each name and add to Photo Array.

And implement required method.

```objc
#pragma mark - FeParallax Table View
-(UIImage *) FeParallaxTableView:(FeParallaxTableView *)sender imageForCellAtIndexPath:(NSIndexPath *)indexPath
{
    UIImage *image = _arrPhotos[indexPath.row];
    return image;
}
-(NSString *) FeParallaxTableView:(FeParallaxTableView *)sender titleForCellAtIndexPath:(NSIndexPath *)indexPath
{
    NSString *title = _arrTitles[indexPath.row];
    
    return title;
}
-(NSInteger) numberOfRowForParallaxCell
{
    return _arrPhotos.count;
}
```

Click run on iPhone 4″ and here is result.

![SampleCode_ParallaxTableView_10](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_10.png?resize=214%2C380)

Before continue reading, confirm you has same result as photo above.

Next step, we add some code to bring photo to each cell.

Now, Go back FeParallaxTableView.m file and look at cellAtIndexPath method.

We need some code to add photo and title into cell.

```objc
-(UITableViewCell *) tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    FeCell *cell = [tableView dequeueReusableCellWithIdentifier:kFeCellID forIndexPath:indexPath];
    
    // Clip to bound
    cell.clipsToBounds = YES;
    cell.contentMode = UIViewContentModeScaleAspectFill;
    
    // Get photo
    UIImage *image = [_delegateParallax FeParallaxTableView:self imageForCellAtIndexPath:indexPath];
    NSString *title = [_delegateParallax FeParallaxTableView:self titleForCellAtIndexPath:indexPath];
    
    // Configure
    [cell configureWithPhoto:image title:title];
 
    return cell;
}
```

Run it again.

![SampleCode_ParallaxTableView_11](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_11.png?resize=242%2C430)

Look beautiful, but if you take closer look, you should notice something wrong. Photo in each cell do NOT same as we expect.

Let check sample photo again.

![SampleCode_ParallaxTableView_13](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_13.png?resize=242%2C430)

With closer look, we can see, our expectation is not the same as It should be. The position of photo in each cells is different. So, go back to resource folder, look each photo again, you may realize that photo’s offset was transform by denta.

Now the time we should deal with new delegate method, are available in iOS 7.

```objc
-(void) tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
   //1
    FeCell *feCell = (FeCell *) cell;
    
    //2
    // Frame
    CGFloat cellY = feCell.frame.origin.y;
    CGFloat denta = cellY - self.contentOffset.y;
 
    //3    
    [feCell configureFramePhoto:CGRectMake(0, -denta  , self.bounds.size.width,self.bounds.size.height)];
}
```

This is **[tableview:willDisplayCell:forRowAtIndexPath]**. This method will be call when new cell will display into tableView. Because, this method is called after [tableView:cellForRowAtIndexPath:], it mean, your cell is already initializer. It’s useful when you want to apply transform to cell before cell will be display.

But, you should bear in mind that it’s only available in iOS 7 and above.

So, I explain what i did :

1. Just cast original cell to FeCell. Because, we will use own method in the next step.
2. If you are brainy guy, the denta property may figure out by subtracting cell’s y offset to UITableView’s contentOffset y.
3. Just transform UIImageView with this denta.

Run it, and check it out ;]

![SampleCode_ParallaxTableView_14](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_14.png?resize=215%2C382)

Everything is OK, but when you scroll, something went wrong ;[. Don’t need to be disappointed. We will make it work correctly right now.

So,we  add some code below :

```objc
-(void) scrollViewDidScroll:(UIScrollView *)scrollView
{
    for (NSIndexPath *indexPath in self.indexPathsForVisibleRows)
    {
        FeCell *cell = (FeCell *)[self cellForRowAtIndexPath:indexPath];
        
        CGFloat cellY = cell.frame.origin.y;
        CGFloat denta = cellY - self.contentOffset.y;
        
        [cell configureFramePhoto:CGRectMake(0,-denta , self.bounds.size.width,self.bounds.size.height)];
    }
}
```

In this time, we use [scrollViewDidScroll:] method. If you are beginner, you may have some question like :”Hey, I don’t see any UIScrollView in app from begin this blog, why is it ? ” or “Why can I still use [scollViewDidScroll:] even my class do NOT conform <UIScrollViewDelegate> ? or something else..

Here is answer. TableView is build on top UIScrollView, It mean, there is UIScrollView inside UITableView, and we can use any delegate’s method comfortable. We don’t need to conform [UIScrollViewDelegate].

Look at this block code above, It’s similar what we did in [tableView:willDisplayCell:…]. Just a bit different. I enumerate each cell which is visible, and apply transform.

Run it again. This is what we want it to be ;]

[Download Mediafire](http://www.mediafire.com/download/e0fehra19v4f1f9/FeParallaxTableView_SampleCode_Part_1.zip)

# The end

To implementing customize control, we should have a deep knowledge about original Apple’s control. As we did, using original control is very simple, but to build new control depend on what they look is another side. It’s not easy.

In this blog, I covered what we need to build a customize control. And the next, I will improve it, create new behaviour.

Thank for reading. Please drop comment if you have questions.