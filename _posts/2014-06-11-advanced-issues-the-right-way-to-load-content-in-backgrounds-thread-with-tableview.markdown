---
layout: post
title: The right way to load content in background thread with TableView
date: '2014-06-11 04:08:04'
---

UITableView is extremely useful class to list content which Apple provide to iOS developer. UITableView use “reusable” pattern to handle content in each cell. So it can contain 100+ row with good performance.

There are many common scenarios when you deal with UITableView.

One of this is load asynchronous content in cell.

Like this

```objc
-(UITableViewCell *) tableView:(UITableView *) tableView cellForRowAtIndexPath:(NSIndexPath *) indexPath
{
    MyCustomeCell *cell = (MyCustomeCell *)[tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
 
    // weak cell
    __weak MyCustomeCell *weakCell = cell;
    // Async
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
        // Get Image
        NSURL *url = // URL link
        NSData *data = [NSData dataWithContentsOfURL:url];
        UIImage *image = [UIImage alloc] initWithData:data];
 
        // Update UI
        dispatch_async(dispatch_get_main_queue(), ^{
            weakCell.imageView = image;
        });
    });
}
```

But, in this code have some issues memory.

Image you have 100 row, each of row you must load async big UIImage. So before cell is being displayed. TableView will call “Data Source” which provide cell. And in cellForRowAtIndexPath: will fire async method to load content.

But if this cell is OUT of visible area. The async operation you called is **still doing work**. So when this operation update UI in main thread, this UIImage is unnecessary resource. Sometime, it cause some “buggy”.

UITableViewCells are often **reused instances**. This means that cells being loaded into the view may sometimes contain data that was loaded originally into a completely different cell.

So, to handle this common scenarios. I use NSOperation and NSOperationQueue  Apple was introduced NSOperationQueue and many relative class in iOS 4. This was build in top of Grand Dispatch Central. But improve more enhancement. NSOperation can be cancel and resume easily.

In brieft, we will implement step-by-step :

1. Init array or dictionary
2. In cellForRowAtIndexPath:, we will create NSOperationBlock and add to background thread. You must implement how it do if this operation is NOT cancel. Finally, we add to array or dictionary to usable.
3. Implement didEndDisplayingCell, this method will be called if cell is OUT of visible area.
4. Get operation in arr / dictionary and cancel. And remove out.

Here is sample :

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    MyCustomeCell *cell = (MyCustomeCell *)[tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
 
    // Get URL
    NSURL *url = _arrURL[indexPath.row];
 
    //Create a block operation for loading the image into the profile image view
    NSBlockOperation *loadImageOperation = [[NSBlockOperation alloc] init];
 
    //Define weak operation so that operation can be referenced from within the block without creating a retain cycle
    __weak NSBlockOperation *weakOperation = loadImageOperation;
 
    [loadImageOperation addExecutionBlock:^(void){
        //Some asynchronous work. Once the image is ready, it will load into view on the main queue
        UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:url]];
 
        // Update UI in main thread when completion
        [[NSOperationQueue mainQueue] addOperationWithBlock:^(void) {
            //Check for cancelation before proceeding. We use cellForRowAtIndexPath to make sure we get nil for a non-visible cell
            if (! weakOperation.isCancelled) 
            {
                MyCustomeCell *theCell = (MyCustomeCell *)[tableView cellForRowAtIndexPath:indexPath];
                theCell.imageView.image = image;
 
                // Remove operation
                [_myDictionary removeObjectForKey:indexPath];
            }
        }];
    }];
 
    //Save a reference to the operation in an NSMutableDictionary so that it can be cancelled later on
    if (url) 
    {
        [_myDictionary setObject: loadImageOperation forKey:indexPath];
    }
 
    //Add the operation to the designated background queue
    if (loadImageOperation) {
        [_operationQueue loadImageOperation];
    }
 
    //This would be a good place to assign a placeholder image
    cell.imageView.image = placeholdImage;
 
    return cell;
}
```

and we take advantage in didEndDisplayngCell method.

We will cancel operation which called in cellForRowAtIndexPath: before.

```objc
- (void)tableView:(UITableView *)tableView didEndDisplayingCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath 
{
    //Fetch operation that doesn't need executing anymore
    NSBlockOperation *ongoingDownloadOperation = [_myDictionary objectForKey:indexPath];
 
    // Ensure ongoingDownloadOperation is not nil
    if (ongoingDownloadOperation) 
   {
        //Cancel operation and remove from dictionary
        [ongoingDownloadOperation cancel];
        [_myDictionary removeObjectForKey:indexPath];
    }
}
```

So, this is the best way to handle this scenarios. You can cancel all of operation if you push to new UIViewController.

```objc
-(void) viewWillDisappeard:(BOOL) animated
 
{
    [super viewWillDisappeard:animated];
 
    // Cancel all
    [_myOperationQueue cancelAllOpeations];
}
```

Finally, you can improve more performance by setting `setMaxConcurrentOperationCount`.

```objc
[_myOpeationQueue setMaxConcurrentOperationCount:3];
```

Feel free for giving me your comment or your opinion.

Thanks for reading ;]


