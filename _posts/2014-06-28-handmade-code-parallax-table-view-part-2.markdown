---
layout: post
title: 'Handmade Code: Parallax Table View – Part 2'
date: '2014-06-28 10:10:28'
---


![TableParallax_icon-01](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/TableParallax_icon-01-300x300.jpg?resize=300%2C300)

Hi guy, if your are new reader in this series handmade code. I strongly recommend read Part 1 before continue

# Improve UX

Here is what we did in a previous part.  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="450" src="//www.youtube.com/embed/DFUNjN3YUhQ" width="600"></iframe>

Everything is done. But if you are perfectionist guy, you should be angry. Because, animation don’t feel naturally. It like “fake” parallax.

Yes, this is things we need improve more.


# Real parallax effect

We need add velocity to photos in each cell.

Time to go back  FeParallaxTableView.m file.

```objc
-(void) tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
    FeCell *feCell = (FeCell *) cell;
    
    // Frame
    CGFloat cellY = feCell.frame.origin.y;
    CGFloat denta = cellY - self.contentOffset.y;
    
    // Get natural
    CGFloat threshold = 200.0f;
    CGFloat percent = denta / self.bounds.size.height;
    CGFloat dentaParallax = percent * threshold;
 
    [feCell configureFramePhoto:CGRectMake(0,-denta  + dentaParallax , self.bounds.size.width,self.bounds.size.height)];
}
```

Here, I have just add a bit code in `[tableView:willDisplayCell:forRowAtIndexPath:]`. In this time, i calculator new variable which called `dentaParallax`

As according to its name, this variable was defined depend on How much the photo in cell should be translated. I take the height of screen as landmark, and a percent of parallax is division between denta and landmark.

Then, I convert percent to px, by multiplying percent with threshold variable. You can choose any native number as you like. The large number will affected the speed of parallax effect. So, with 3,5″ or 4″ screen, 200px is the best number for parallax effect.

But, it’s not the end. We should add some code above into [scrollViewDidScroll:].

```objc
-(void) scrollViewDidScroll:(UIScrollView *)scrollView
{
    for (NSIndexPath *indexPath in self.indexPathsForVisibleRows)
    {
        FeCell *cell = (FeCell *)[self cellForRowAtIndexPath:indexPath];
        
        // Frame
        CGFloat cellY = cell.frame.origin.y;
        CGFloat denta = cellY - self.contentOffset.y;
        
        // Get natural
        CGFloat threshold = 200.0f;
        CGFloat percent = denta / self.bounds.size.height;
        CGFloat dentaParallax = percent * threshold;
 
        [cell configureFramePhoto:CGRectMake(0,-denta + dentaParallax , self.bounds.size.width,self.bounds.size.height)];
    }
}
```

Run it again, you will be surprised ;]  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="450" src="//www.youtube.com/embed/m1VkOUtGtls" width="600"></iframe>

# Just a bit

Hey, before closing my blog, please wait me a minute.

Do you notice when scrollView is stopped, the upper cell’s photo is overlaid ? The title and photo were hidden a part by status bar.

![SampleCode_ParallaxTableView_15](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/SampleCode_ParallaxTableView_15.png?resize=320%2C480)

And what I expect is the scrollView can adjust content offset to nice position itself.

```objc
-(void) scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate
{
    if (!decelerate)
    {
        [self scrollToNearestRow];
    }
}
-(void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    [self scrollToNearestRow];
}
```

We handle two common situation.

1. The first, when I scroll a scrollView and lift my finger up while the scrollView is stopped.
2. And the last, I drag scrollView and lift my finger up again, but scrollView is decelerating in this time.

And I use 2 method above. One is [scrollViewDidEndDragging:willDecelerate:], the rest is [scrollViewDidEndDecelerating:];

```objc
-(void) scrollToNearestRow
{
    CGPoint point = self.contentOffset;
    
    // Cell
    NSIndexPath *indexPath = [self indexPathForRowAtPoint:point];
    FeCell *cell = (FeCell *)[self cellForRowAtIndexPath:indexPath];
    
    CGFloat denta = (cell.frame.origin.y + cell.frame.size.height) - point.y;
    CGFloat percent = denta / (float)cell.frame.size.height;
    
    if (percent > 0.5)
    {
        [self scrollToRowAtIndexPath:indexPath atScrollPosition:UITableViewScrollPositionTop animated:YES];
    }
    else
    {
        [self scrollToRowAtIndexPath:[NSIndexPath indexPathForRow:indexPath.row + 1 inSection:0] atScrollPosition:UITableViewScrollPositionTop animated:YES];
    }
    
}
```

So, here is final result.  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="450" src="//www.youtube.com/embed/AIpDvKbn8-E" width="600"></iframe>
 

 


