---
layout: post
title: 'Diff in iOS [Part 2]'
date: '2017-03-04 10:52:22'
---

Hi, it’s part 2 of my LCS series. Hope you have a great day 😇

In this blog, I would like to cover how we can apply LCS (aka. Diff) into iOS Development. It’s so boring if I keep writing blog with “deep” theory, and no place for enjoying.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/98b322c7bf5992110646420866f72f492bc8706eb4769179676149ad713251d3.jpg)

Before diving, we should try a real problem I ensure everybody will encounter if you’re iOS S.E.

Let imagine, we have an app, same philosophy with Instagram. We have Feed screen, where we can see all post of your friends, your following.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/diff_screenshot_app.jpg)

On the top-right of navigation bar. We have pull-to-refresh button. Whenever you tap it. It will get new data from server, then filling data into UITableView.

Your requirement is figured it out the way to “reload” table view with **less effort** as much as **possible**. 😉

### Solution 1: Force reloadData()

Don’t care any things, just call
```swift
self.tableView.reloadData()
```
In particular scenario, it might be the best solution if you have < 10 items, and the hierarchy of cell’s subviews are simple. But what if your app grows up quickly, and have around 100 items, each TableViewRow has tons of complex subviews, such as UIStackView, UICollectionView,…

By forcing **reloadData**(). It means UITableView will re-draw and calculate dynamic size of 100 cells again -> It hits your CPU and reduces performance as well. It’s painful.

I will more detail in Benchmark section to show you how it’s bad.

### Solution 2: Do manually

Many people will come up with solution 2. Instead of calling reloadData(). They will calculate the difference (insert/delete/reload) **manually** and warp into tableView.performAction{}

Some things like this
```swift
// Prepare data
let oldData = self.data
let newData = feedResponse.data
 
// Check diff manually
// How do it if the order of newData is completely difference with oldData ? ❌
let insertIndexPaths = // If-then stuff
let deleteIndexPaths = // if-then stuff
 
// Reload
self.tableView.beginUpdates()
 
self.tableView.insertRows(at: insertIndexPaths, with: .none)
self.tableView.deleteRows(at: deleteIndexPaths, with: .none)
 
self.tableView.endUpdates()
```
#### Pros

- By calling reload/insert/delete on particular cell. You reduces time to re-draw cell. It’s good point.
- You can apply specific animation for each kind of transform easily.

#### Cons

- By calculating the diff manually, it means by your hand and brain. It’s still a nightmare if you have a many new item, and the order of new item is completely difference than previous items.
- If you have enough patient, and careful. You could do it, but who know your solution is the best optimization? Maybe there is someone out there who come up with another comparison, have less “transform” than your result? 🤔 Working manually is not optimize approach.
- If you make a mistake, your app crash with some kind of error like this

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/lcs-part-2-error.jpg)

It always happens you indexPaths doesn’t match with data source.

### Solution 3: Diff Algorithm

How about LCS (Diff). Good news, we can reuse what we actually did on [LCS Part 1](http://nghiatran.me/longest-common-subsequence-diff-part-1/). By using modified Diff algorithm, we can extract “transform” from memorization table. We can know which items are removed, added, or reloaded. All of messy tasks will compute automatically. You don’t need put more effort, or get nervous when app crashes anymore.

The final result.

```swift
// Calculate diff and map to each actions
// Does automatically 💯
let insertionIndexPaths = diff.insertions.map({ IndexPath(row: $0.idx, section: self.sectionIndex) })
let deletionIndexPaths = diff.deletions.map({ IndexPath(row: $0.idx, section: self.sectionIndex) })
 
// Perform action
tableView.beginUpdates()
tableView.insertRows(at: insertionIndexPaths, with: insertionAnimation)
tableView.deleteRows(at: deletionIndexPaths, with: deletionAnimation)
tableView.endUpdates()
```

Those codes are self-explanatory, sound interesting enough? 😍

Besides that, I will do benchmark by each solution on my old iPhone 5, give you overall perspective.


## 1. Diff Algorithm

### 1.1 Memorization table

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_12.png)

If you’re not familiar with that memorization-table. I would like to recommend to read [LCS-Diff Part 1](http://nghiatran.me/longest-common-subsequence-diff-part-1/) before going next.

Let me remind in case you’ve forgotten. LCS is one of classic computer science problems.

Given string A = ADFGT, string B = AFOXT. The LCS of A and B is **AFT**.

But we need more than LCS, we should need “how” A can transform into B. Fortunately, from the table above, we can “extract” transform easily too. Memorization-Table is a new King 😎

### 1.2 DiffTransform enum

To understand what’s is transform stand for, I will create new generic enum

```swift
// Transform
enum DiffTransform<T> {
    
    // Represent for reload/insert/delete in tableView
    // Int: index need to be transform
    // T: generic data
    case reload(Int, T)
    case insert(Int, T)
    case delete(Int, T)
}
```

We created DiffTransform enum for representing a “transform”. Please notice one thing, I prefer use Enum with **Generic** and **associated-value** than struct/class. Because if we come with Struct/Class, we also need to define another Enum to handle kind of Transform. It’s duplicated work.

With DiffTransform, we can store “kind of transform”, the index, and the real data at the same object, with less code as possible 😉

In few trivial programming language like Java or objc, it’s impossible unfortunately. We must create class and enum, but from Swift 2.0, we can do it. It’s save me a lot of redundancy code. Less code, less bug. You know 🤘🏽

In case it’s first time you’ve heard it. Take a look at [Generic Enum](https://appventure.me/2015/10/17/advanced-practical-enum-examples/#sec-2-3) and [Enum with associated-value](https://appventure.me/2015/10/17/advanced-practical-enum-examples/#sec-1-5)

I also implement few helper to quick access data in future.

```swift
// Helper
extension DiffTransform: CustomStringConvertible, CustomDebugStringConvertible {
    
    // Value - quick access
    var value: T {
        switch self {
        case .reload( _, let value):
            return value
        case .insert( _, let value):
            return value
        case .delete( _, let value):
            return value
        }
    }
    
    // index - quick access
    var index: Int {
        switch self {
        case .reload(let index, _):
            return index
        case .insert(let index, _):
            return index
        case .delete(let index, _):
            return index
        }
    }
    
    // CustomStringConvertible
    var description: String {
        return self.toString
    }
    
    // CustomDebugStringConvertible
    var debugDescription: String {
        return self.toString
    }
    
    // [Private] To string
    private var toString: String {
        switch self {
        case .reload(let index, let value):
            return "Reload((\(index))[\(value)])"
        case .insert(let index, let value):
            return "Insert((\(index))[\(value)])"
        case .delete(let index, let value):
            return "Delete((\(index))[\(value)])"
        }
    }
}
```

From previous LCS blog, we have 3 rules. I will remind in each step, also make a sightly modification.

We will traceback from right-bottom corner to left-top corner.

- If A[i] == B[j] ->It means, A[i] is part of LCS. Then we move diagonal by i–, j–

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_9.png)

**T** from string A convert to **T** from string B -> There is no difference, we just reload it.

The “transform” simple is
```swift
let reload = DiffTransform.reload(i, A[i-1])
```

- if A[i] != B[i], we have two sub-case

```swift
if table[i][j] == table[i-1][j] {
    // Go top
    // Delete at i-1
}
else if table[i][j] == table[i][j-1] {
    // Go Left
    // Insert at j-1
}
```

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_10.png)

As you can see, G != X. It means, G needs to be removed from A.
```swift
let delete = DiffTransform.delete(i-1, A[i-1])
```
![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_11.png)

F != X. It means, X need to be added.

```swift
let insert = DiffTransform.insert(i-1, B[j-1])
```
```
- i == 0 || j ==0
```

It’s two special case we should mention. Because we need to figure it out the transform, don’t miss this case

```swift
if i == 0 {
    // Insert at j-1
    let insert = DiffTransform.insert(j-1, B[j-1])
}
else j == 0 {
    // Remove at i-1
    let remove = DiffTransform.delete(i-1, A[i-1])
}
```

#### Summary

By following 4 cases, we can determine when transform is applied.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/lcs-part-2-1.png)

Here is final instruction.

```swift
let reload = DiffTransform.reload(5, A[4]) // at (i,j) = (5,5)
let delete = DiffTransform.delete(3, A[3]) // at (4,4)
let insert = DiffTransform.insert(3, B[3]) // at (3,4)
let insert = DiffTransform.insert(2, B[2]) // at (3,3)
let reload = DiffTransform.reload(3, A[2]) // at (3,2)
let delete = DiffTransform.delete(1, A[1]) // at (2,1)
let reload = DiffTransform.reload(1, A[0]) // at (1,1)
// Stop recursive at (0,0)
```

### 1.3 Diff

This time to implement the container for storing all DiffTransforms which we calculated by hand 😉

```swift
// Diff Container
struct Diff<T> {
    
    // Shortcut Element
    typealias Element = DiffTransform<T>
    
    // Result
    private var _result: [Element] = []
    var result: [Element] {
        return self._result
    }
    
    // Insertion
    var insertion: [Element] {
        return self.result.filter({ $0.isInsertion })
    }
    
    // Deletion
    var deletion: [Element] {
        return self.result.filter({ $0.isDeletion })
    }
    
    // Reload
    var reload: [Element] {
        return self.result.filter({ $0.isReload })
    }
    
    mutating func append(item: Element) {
        self._result.append(item)
    }
}
 
// Override + : Diff + DiffTransform
func +<T> ( left: Diff<T>, right: DiffTransform<T>) -> Diff<T> {
    var left = left
    left.append(item: right)
    return left
}
```

A Diff is a simple struct. It contains array of DiffTransform. I prefer to use Generic all time as possible. I suppose it’s hard to read at the first time, but if you’re familiar enough, it’s quite simple. Generic is really flexible for container as well.

I defined Diff<T> and DiffTransform<T> is same Generic on purpose. So both of model store same kind of value.

Beside that, I also implement few helper methods, it helps us get insertion/deletion/reload quickly, and override + operation too.

### 1.4 Warp it up

Time to combine all separately things into one part. I’m still following same approach from Part 1, extend Array, generate MemorizationTable, recursive the table and calculate the DiffTransform.

#### Memorization table

```swift
// Memorization table
struct MemorizationTable<T: Equatable> {
    
    static func buildTable(x: [T], y: [T]) -> [[Int]] {
        
        // Create 2-dimension table, and filled with zero
        let n = x.count
        let m = y.count
        var table = Array(repeating: Array(repeating: 0, count: m + 1), count: n + 1)
    
        // Iterate from top-left corner -> bottom-right corner
        for i in 0...n {
            for j in 0...m {
                if i == 0 || j == 0 {
                    table[i][j] = 0
                }
                else if x[i-1] == y[j-1] { // MATCH
                    table[i][j] = table[i-1][j-1] + 1
                }
                else { // NOT MATCH
                    table[i][j] = max(table[i-1][j], table[i][j-1])
                }
            }
        }
        
        return table
    }
}
```

#### Calculate DiffTransfrom, Diff
```swift
// But we must ensure, each element must adopt with <Equatable> protocol -> We can make comparision
extension Array where Element: Equatable {
    
    func diff(_ other: [Element]) -> Diff<Element> {
        
        // Build memorization table
        let table = MemorizationTable.buildTable(x: self, y: other)
        
        // Print Diff
        return Array.diffFromMemorizationTable(table, self, other, self.count, other.count)
    }
    
    fileprivate static func diffFromMemorizationTable(_ table: [[Int]], _ x: [Element], _ y: [Element], _ i: Int, _ j: Int) -> Diff<Element> {
        
        // Exit
        if i == 0 && j == 0 {
            return Diff<Element>()
        }
        else if i == 0 { // Insert
            return diffFromMemorizationTable(table, x, y, i, j-1) + DiffTransform.insert(j-1, y[j-1])
        }
        else if j == 0 { // Delete
            return diffFromMemorizationTable(table, x, y, i-1, j) + DiffTransform.delete(i-1, x[i-1])
        }
        else if table[i][j] == table[i-1][j] { // Delete
            return diffFromMemorizationTable(table, x, y, i-1, j) + DiffTransform.delete(i-1, x[i-1])
        }
        else if table[i][j] == table[i][j-1] { // Insert
            return diffFromMemorizationTable(table, x, y, i, j-1) + DiffTransform.insert(j-1, y[j-1])
        }
        else { // Reload
            return diffFromMemorizationTable(table, x, y, i-1, j-1) + DiffTransform.reload(i-1, x[i-1])
        }
    }
}
```

It’s sightly modification than the original LCS Algorithm. Instead of trace-back and print LCS string, we check small case and determine when “Transform” and warp into DiffTransform.

The heart of Diff is at **diffFromMemorizationTable **func. It’s recursion method, and base-recursion return Diff(), then adding DiffTransform from prior recursive func. The finally result will be an Diff instance, with result is all transforms.

#### Apply Diff

```swift
// Apply Diff 
func apply(diff: Diff<Element>) -> [Element] {
    var copy = self
    
    // Delete first
    for delete in diff.deletion {
        copy.remove(at: delete.index)
    }
    
    // Insert
    for insert in diff.insertion {
        copy.insert(insert.value, at: insert.index)
    }
    
    return copy
}
```

It’s place for magic happen. [A] transform to [B] by applying DiffTransform one by one. We start with Deletion Transform firstly, then Insertion Transform. We also able to apply ReloadTransform, but it this case, we’re working on String, we don’t need to reload it-self.

### Test with String

```swift
// Test
let a = ["A", "D", "F", "G", "T"]
let b = ["A", "F", "O", "X", "T"]
 
let diff = a.diff(b) 
// diff = [Reload((0)[A]), Delete((1)[D]), Reload((2)[F]), Insert((2)[O]),
Insert((3)[X]), Delete((3)[G]), Reload((4)[T])]
 
let c = a.apply(diff)
print(c) // c = ["A", "F", "O", "X", "T"]
```

Sound easy, right? 😉 The Diff is the optimize way as possible, and it does 100% automatically. Life is easy now 🤗


## 2. Integration with UITableView

We create DiffCalculator struct to handle all transform logic for UITableView. It stores tableview as weak variable (We don’t want to make cycle-retain right?), and data model.

The especially things is Setter of data. We calculate the DiffTransform for Insertion/Deletion/Reload, then performing the transform in <beginUpdate> and <endUpdate>

```swift
// UITable View Integration
struct DiffCalculator<T: Equatable> {
    
    // Weak table view
    weak var tableView: UITableView?
    
    // Data
    private var _data: [T] = []
    var data: [T] {
        get {return self._data}
        set {
            let old = self._data
            let diff = old.diff(newValue)
            
            // Set
            self._data = newValue
            
            // Transform
            self.applyTransform(with: diff)
        }
    }
    //
    // MARK: - Init
    init(tableView: UITableView, data: [T]) {
        self.tableView = tableView
        self._data = data
    }
    
    // Apply Transfrom
    fileprivate func applyTransform<T: Equatable>(with diff: Diff<T>) {
        
        // Update transform
        guard diff.result.count > 0 else {return}
        guard let tableView = self.tableView else {return}
        
        tableView.beginUpdates()
        
        // Map indexPath
        let insertion = diff.insertion.map({ IndexPath(row: $0.index, section: 0) })
        let deletion = diff.deletion.map({ IndexPath(row: $0.index, section: 0) })
        let reload = diff.reload.map({ IndexPath(row: $0.index, section: 0) })
        
        // Delete
        tableView.deleteRows(at: deletion, with: .automatic)
        tableView.insertRows(at: insertion, with: .automatic)
        tableView.reloadRows(at: reload, with: .automatic)
        
        tableView.endUpdates()
    }
}
```

Using DiffCaculator is really simple.

```swift
// Example
let tableView = UITableView()
let data = ["Nghia Tran", "nghiatran.me", "Saigon", "Singapore", "Bangkok"]
var diffCalculator = DiffCalculator<String>(tableView: tableView, data: data)
 
// Using diffCalculator.data as dataSource for UITableView.
 
// Pull to refresh with new data
let newData = ["Nghia Tran", "Uni", "nghiatran.me", "Ha Noi", "KL", "Singapore", "Bangkok", "Finland"]
 
// Update new data
// Table reload with optimize way.
diffCalculator.data = newData
```


## 3. Benchmark

For demonstration, I write a simple app with smallDataSource (10 rows) and reload with large DataSource (20 rows) (simulate it’s from API).

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/diff_app.jpg)

On my purpose, the size of cells are dynamic, and collectionView inside each cell and the quality of images are high. Because we want to see the benchmark clearly. So make it’s more complex.

The Sample app has 3 tabs, stand for each approach we’ve discussed: Force Reload, manually calculate, and Diff algorithm.

On this benchmark, I will test on my **iPhone 5**, gain accurate results, it will reflect efficiency for each solution.

### 3.1 Force reloadData()

By calling tableView.reloadData() is naive solution we could do. As you can see clearly, the first peak intends for the first time we reload and fill data into UITableView.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/diff_benchmark_reload_2.jpg)

Noticeable, the second peak really impacts our performance. It costs **47%**. Because the table view must calculate the dynamic size on runtime, get minimum size which matched cell’s constraints, it depends on length of comment string. And reload the collectionview built-in the cell.

```swift
// My Purpose
// Don't Cache image in memory
// Just make it complex to render -> Easier to analytic in Instrustment
let path = Bundle.main.path(forResource: imageName, ofType: "jpg")!
let image = UIImage(contentsOfFile: path)
 
self.imageView.image = image

```

Please bear in mind, I did it on my purpose. Because in a real world, the layout of cells are most complex. And of course, as more as complex we can see the result clearly.

### 3.2 Do manually

Here is sample data: The data source of Feed screen doesn’t order by created_at, it looks like random, depend on the “score” of each Item.

```swift
let initalData = [data_1, data_2, data_3, data_4, data_5, data_6, data_7, data_8, data_9, data_10]
let newData = [data_1, data_4, data_5, data_6, data_3, data_9, data_2]
```

How could I do manually? 🤔

How could I get **insertIndexPaths** and **deleteIndexPaths** then passing into insertRows/DeleteRows 🤔

```swift
// Reload
self.tableView.beginUpdates()
self.tableView.insertRows(at: insertIndexPaths, with: .none)
self.tableView.deleteRows(at: deleteIndexPaths, with: .none)
self.tableView.endUpdates()
```

Really hard, right? 🤔

Yeah, if you’re struggling to solve it, there are no change to make sure your result is optimization. Or you might get mistake, and your app is crashed completely.

Diff algorithm is a rescue now 🤗

### 3.3 Diff algorithm

It’s the perfect time to reused DiffCalculator which we implemented in the beginning.

```swift
// Initial diff
self.diffCalculator = DiffCalculator<FeedObj>(tableView: self.tableView, data: self.feedObjs)
 
// From API
let newData = FeedObj.pullNewLargeDataSource()
self.feedObjs = newData
 
// Make Dif and reload automatically + optimazation
self.diffCalculator.data = newData
```

It’s completely straight-forward. Just initialize self.diffCalculator and set data with new data from API. DiffCalculator will do difficult part for us.

```swift
[Delete((1)[Diff.FeedObj]), Delete((2)[Diff.FeedObj]), Insert((4)[Diff.FeedObj]),
 Delete((6)[Diff.FeedObj]), Delete((7)[Diff.FeedObj]),Insert((6)[Diff.FeedObj]),
 Delete((9)[Diff.FeedObj])]
 ```

We only need 6 steps to transform. It’s optimization approach for now 🤗

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/diff_benchmark_small_data_source.jpg)

The result doesn’t make us disappointed. From **47%**, we reduce to **21%**. Good point 💯

### Summary

- Get perfect transform between 2 data source, absolutely optimize than doing by hand manually
- 100% calculate automatically.
- Avoid crash unexpected.
- Suitable for complex tableview/collectionview.


## 4. Drawback

Everything has two sides, it’s unfair if we only mention the bright side. Does Diff algorithm (based on LCS) have any disadvantage? 🤔

Let try on large set of data. The initial data has 100 item, and new data has 210 item, and do each step which same we did.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/03/diff_benchmark_large_data_source.jpg)

Wow, **56%** CPU for Diff. What’s wrong here 🤔. As big as data source’s length could be, the time for Diff increases significantly.

The problem is time complexity of Diff is **O(n * m)**. So we have 100*210 = 210,000 cell in memorization-table, and of course, cost more CPU to iterate through the table.
Diff algorithm which we discussed is based on **LCS** [Part 1].  So it’s not best algorithm we could implement. I choice this approach because it’s simple, and it’s relevant with [Part 1](http://nghiatran.me/longest-common-subsequence-diff-part-1/).

## 5. What’s next?

In next chapter, we will discuss **Paul Heckel’s Diff**.

This algorithm finds all the possible update that UITableView needs in **linear time **O(n) . It’s most efficiently approach which widely used in applications. Moreover, [IGListKit](https://engineering.instagram.com/open-sourcing-iglistkit-3d66f1e4e9aa#.p8ojr79an) also uses it as back core as well. 🤗

## 6. Where to go from here?

[iOS diff on Github](”https://github.com/NghiaTranUIT/LCS-Swift)

Hope you enjoy my blog about LCS and Diff on iOS Development 👍

Diff algorithm and integration with UITableView which I cover, it’s not perfect optimize solution. It’s perfect place for beginner. There are various algorithms to improve the time complexity. One of them is [Paul Heckel’s Diff](https://gist.github.com/ndarville/3166060) (was published 1978).

If you would like to take a deep research, take a look at [IGListKit](https://github.com/Instagram/IGListKit) by Instagram Engineer, and [their blog](https://engineering.instagram.com/open-sourcing-iglistkit-3d66f1e4e9aa#.4xyqertyv).

If you need more challenge. Here is the puzzle:

```swift
DiffCalculator has limited when only working with UITableView.
How about UICollectionView, maybe we're missing it ?
 
Challenge yourself and implement DiffCalculate generic protocol instead of struct.
Make a default extension, and adopt it with both of TableView/CollectionView.
 
Final result: DiffCalculateProtocol will work with UITableView and UICollectionView or any  with less duplicated code, reusable.
 
Let do it 😎
```

 

 


