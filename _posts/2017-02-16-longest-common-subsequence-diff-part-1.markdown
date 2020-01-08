---
layout: post
title: Longest Common Subsequence Diff [Part 1]
date: '2017-02-16 14:04:46'
---


Hi, Today is totally perfect day to start a new series of Algorithm blog. Since I’ve learned data structure & algorithm to prepare for next interview. I learnt tons of new amazing things, so I decide to write something for anyone who interesting 😉

Longest Common Subsequence (aka Diff) is classic problem in computer science. We’re using it everyday, but we didn’t notice. It also widely used by Revision Control System.

- When Github figure it out what’s new line of codes, what’s removed. They use Diff
- When [DiffChecker](https://www.diffchecker.com)  do comparison. They use Diff.
- Diff Utility use Diff as main core.

In another hand, diff is also useful to compare pair of images.

- [Kaleidoscope](http://www.kaleidoscopeapp.com) is able to spot the difference of two images with pixel accuracy, and print it out.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/kaleidoscope.jpg)

- [Facebook iOS Snapshot Test](https://github.com/facebook/ios-snapshot-test-case) make diff by drawing both of layer/view and reference image, then compare each pair of pointer in memory with the C function `memcmp(). `This make it extremely quick: from 0.013 to 0.086 seconds for fullscreen iPad on Macbook Air ([reference](https://www.objc.io/issues/15-testing/snapshot-testing/))

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/snapshots-reference-59b0b96b.png)

- [IGListKit](https://github.com/Instagram/IGListKit) is amazing open-source, built from Instagram Engineering team. According from their blog, IGListKit still use improved Diff (Paul Heckel’s algorithm) to calculate and perform insert/move/reload/delete without crashed with **linear time BigO(n)**, it’s really increadible result.

![](https://i1.wp.com/cdn-images-1.medium.com/max/1600/1*7w2BL8pAqAs-qMiDWHLENA.gif?resize=541%2C260&ssl=1)

To understand deeply LCS and a apply it to real world. I intent to split into 2 parts.

**Part 1:** We will discuss how to implement LSC with **naive solution** and with **Dynamic Programming**, by using Memorization table. I will try to visualize the algorithm of both solution as possible. I will implement by pure swift to make sure everybody can understand before moving to real shit.

The main goal, we will extend Array<Element>. Put more effort, so it can reusable in any kind of array.

```swift
// Work with generic array
// It will work [Any], which Element must adopt with <Equatable> protocol
extension Array where Element: Equatable {}
```

**Part 2:** It’s real world, we reused the knowledge from part 1. Implement Diff, DiffStep to represent each step (insert/delete/reload) need to be transform. Also extend UITableView, UICollectionView to calculate optimize transformation, from old to new Data Source

The goal looks like:

```
// Calculate diff and map to each actions
let insertionIndexPaths = diff.insertions.map({ IndexPath(row: $0.idx, section: self.sectionIndex) })
let deletionIndexPaths = diff.deletions.map({ IndexPath(row: $0.idx, section: self.sectionIndex) })
 
// Perform action
tableView.beginUpdates()
tableView.insertRows(at: insertionIndexPaths, with: insertionAnimation)
tableView.deleteRows(at: deletionIndexPaths, with: deletionAnimation)
tableView.endUpdates()
```

^ see that. Really simple for use. If you calculate the diff **manually **by your hand and your pencil, it’s really tricky and risky. If it go wrong, your app might crash completely and you’re fired =)). Brace yourself


## 1. What’s classic LCS ?

Given 2 string, find the length of Longest Common Subsequence (LCS) which present in both of them.  A subsequence is a sequence that appears in the same relative order, but not necessarily contiguous.

Example, we have string X = “ABCDEFG”. So “ABD”, “ACD”, “AFG”, “CEFB”, “FG”, …. is one of subsequence of X.

But “AED”, “EBA”, “GFEDA” … isn’t correct, because it must be same order.

So a string with n length, will have n² substrings which can construct possible.

LCS of “ABCDEF” and “ATDEXF” is ADEF, length = 4

LCS of “ABC” and “AXYZC” is AC, length = 2

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/Longest-Common-Subsequence-example.jpg)

or A = “acbaed”, B = “abcadf” => LCS is “acad”, length = 4

 

 

 

 
----------------------------------------------------------------------------------------

 

 

 
 

Before walking into Implementation and Explanation section. It would be better if you try putting your hand in real shit.

Don’t worry about performance or Big(O) yet, just finish it by yourself.

You can try online at [HackerRank](https://www.hackerrank.com/challenges/dynamic-programming-classics-the-longest-common-subsequence) with real test-cases 😉

**if you don’t have enough patient, just scroll down and  keep reading**😪

 




----------------------------------------------------------------------------------------

 

 

 


## 1.1 Naive approach

The naive solution we can think in our mind is: Iterate through each character in both of string, and compare each result to get final LCS.

In given string A with length = n, it might take O(2<sup>n</sup>) time to complete. It still work without problem if the string is short, but in particular situation,  it’s really nightmare if we have very long long text.

Don’t worry about it at this time, I will come to naive approach firstly, by using bottom-up recursion.

If we come with recursion, two important things we should to defined is** sub-problem** and **base-recursion(exit)**

### Sub-problem

**Case 1: Both of character is same.**

Example: Given “ADFGT” and “AFOXT”

We start from bottom, so we get “T” from the first one and “T” from the second one.

It’s match then T **must be a part of LCS**, so we count up by 1. Then we remove both of those, and run recursive continently.

```swift
// Case 1 - both characters are matched
LCS("ADFGT", "AFOXT") = 1 + LCS("ADFG", "AFOX")
```

**Case 2: Pair of character isn’t match:**

Given  “ADFG” and “AFOX”, and as we see

“G” != “X”

It means G maybe part of LCS between “ADF**G**” and “AFO”

or X could be part of LCS between “ADF” and “AFO**X**”

We don’t know which case is correct. So we should call recursion on two possible case and compare the get max value. We can write down like here

```
LCS("ADFG", "AFOX") = Max(LCS("ADF", "AFOX"), LCS("ADFG", "AFO"))
```

### Summarize

Give 2 string A[0..i-1], and B[0..j-1] with i and j is length of A and B

```swift
// Define findLCS func
func findLCS(A: String, B: String) {}
let m = A.length()
let m = B.length()
 
// Case 1
A[m - 1] == B[n - 1] => findLCS(A, B) = 1 + findLCS(A[0..m-2], B[0..n-2])
 
// Case 2
A[m - 1] != B[n - 1] => findLCS(A, B) = MAX(findLCS(A[0..m-2], B[0..n-1]), findLCS(A[0..m-1], B[0..n-2]))
```

### Base recursion (exit)

So, how about exit recursion ? If you’re working on recursion functionality, please, don’t forget the EXIT.

It’s same habit when you come to large building, like CVC cinema or luxury mall, look a EXIT way firstly. In-case of fire, we can get out quickly, and save your life. It’s one of must-have survival skill in modern world. So do the same things with Recursion too 😇

Really to figure it out when we should stop: either if any string become **empty string.**

```swift
// Define findLCS func
func findLCS(A: String, B: String) {}
let m = A.length()
let m = B.length()
 
// Case 1
A[m - 1] == B[n - 1] => findLCS(A, B) = 1 + findLCS(A[0..m-2], B[0..n-2])
 
// Case 2
A[m - 1] != B[n - 1] => findLCS(A, B) = MAX(findLCS(A[0..m-2], B[0..n-1]), findLCS(A[0..m-1], B[0..n-2]))
```


## 1.2 Problem

Imagination is really hard, so I will draw a tree to see what’s behind the scene.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_13.png)

^ As you can see, there are few sub-problems has been solved many times. Time complexity is O(2<sup>n</sup>) in worse case when all character of X and Y are mismatch. It will be [exponential](https://en.wikipedia.org/wiki/Exponential_growth) by time.

In next section, we should come up with **Dynamic Programming** approach.

Here is swift code

```swift
// Hack convention
// Don't mess up with Range from Swift 3
extension String {
    func charAt(_ i: Int) -> String {
        return String((self as NSString).character(at: i))
    }
}
 
/////////////////////////////
// NAIVE APPROACH
/////////////////////////////
// Recurvise func to length of LCS
func LCS(_ a: String, _ b: String) -> Int{
    
    // Exit
    if a.characters.count
        == 0 || b.characters.count == 0 {
        return 0
    }
    
    // Prepare
    let lengthA = a.characters.count
    let lengthB = b.characters.count
    let aIndex = a.index(a.endIndex, offsetBy: -1)
    let bIndex = b.index(b.endIndex, offsetBy: -1)
 
    // Sub-problem
    if a.charAt(lengthA - 1) == b.charAt(lengthB - 1) {
        // MATCH
        return 1 + LCS(a.substring(to: aIndex), b.substring(to: bIndex))
    } else {
        // NOT MATCH
        return max(LCS(a.substring(to: aIndex), b), LCS(a, b.substring(to: bIndex)))
    }
}
 
 
// Test
let a = "acbaed"
let b = "abcadf"
print(LCS(a, b)) // 4
 
// Unicode
let x = "😇🙌😉💰🎹"
let y = "🙌🍒💰✈️🎹😎🔴"
print(LCS(x, y)) // 3
```
[Swift Playground on github](https://github.com/NghiaTranUIT/LCS-Swift/blob/master/LCS-Naive-Solution-Part-1.playground/Contents.swift)

## 2. Dynamic Programming

Basically, we will use two-dimension array to store the result, reduce time calculate, and reused the result when ever needed. This technique call  as Memorization.

Using memorization table is really useful to avoid Overlapping Substructure when design algorithm, we reused result which calculated before, rather computed over and over. But what is the trade-off ?

1. **It cost more space in memory**. If string is Unicode, it also cost more space than ASCII, but if the goal of solution is aiming to time-complexity, so memorization-table is best choice right now.
2. If we implement on low-end computer, it means we have limited memory, so we should consider carefully to apply this approach. Maybe slow a bit by using Recursion will be better, and acceptable.

Talk enough, please show me your code 😪

### 2.1 Memorization table

I will visualize the table step by step now. We will use same given input string from section 1.

A = “ADFGT” and B = “AFOXT”, and m is A.length(), n = B.length()

Create two-dimension LCS = [m + 1][n + 1], fill with zero if i == 0 || j == 0

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_1.png)

The reason why we fill the zero, because the empty string “” maybe is LCS as well.

Following two equation we summarized before, but this time, we don’t use recursion, we use 2 nested-loop, and define LCS as 2-dimension array (filled with 0). But still retain the same philosophy.

```swift
// Defind LCS array (filled with 0 firstly)
var LCS = Array(repeating: Array(repeating: 0, count: m + 1), count: n + 1)
var i = 1
var j = 1
 
// Case 1: Match
A[i - 1] == B[j - 1] => LCS[i][j] = 1 + LCS[i - 1][j - 1]
 
// Case 2: Not Match
A[i - 1] == B[j - 1] => LCS[i][j] = MAX(LCS([i - 1][j]), LCS(LCS[i][j - 1]))
```

Here is the visualize of them

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_2.png)
![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_4.png)

Simple af right?  😉

We start to iterate over table with i = 1, and j = 1. We have A[i – 1] == “A”, B[j – 1] == “A” => It matches, so we do Case 1

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_5.png)

=> LCS[1][1] = 1 + LCS[0][0] = 1

Continue with i = 1, j = 2. We compare A[i – 1] != B[j – 1] => “A” != “F” => We do Case 2.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_6.png)

Yeah, so can see, by following case 2, we will compare the value of LCS[1][1] and LCS[0][2].

We have 1 > 0 => we get **1** as a result, then store in LCS[1][2].

LCS[1][2] = max(LCS[1][1], LCS[0][2])

Keep computing by your hand + brain to the rest of table.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_7.png)

=> See that 😉 just do it with case 1 again

Until the end.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_8.png)

Finally, we got it. So the Length of LCS is 3.

We haven’t stopped at here yet, because the goal of LCS doesn’t stop at print the length of LCS. We should print out the LCS it-self.

### 2.2 Print LCS

To print LCS in memorization table is tricky. Because we should traceback the table again, from the **bottom corner** to the **top corner** where we’ve started, by following new simplest rules.

- if A[i] == B[j] => It means, this element must be a part of LCS, so we print it, we will go to diagonal (means last char­ac­ter of both string has matched, so we reduce the length of both the strings by 1, so we move diag­o­nally)

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_9.png)

- if A[i] != B[j], we have two sub-case here. Please check my code

```swift
LCS[i][j] = Max(LCS[i - 1][j], LCS[i][j - 1]);
if (LCS[i][j] == LCS[i - 1][j]) {
    solution[i][j] = "top";
} else {
    solution[i][j] = "left";
}
```

Here is visualize

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_10.png)
![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_11.png)

Keep traceback-ing to the top of memorization table 🤗. We will have somethings like below. Please notice, the Red Cell is the character we need to store somewhere, because it’s part of LCS.

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/table_12.png)

The final result is AFT 🙌

### 2.3 Time complexity

### Naive solution

Because we list all possible pair of sub-string between given A and B, and have many overlap sub-problem.

It costs O(2<sup>n</sup>).

It has big problem if the length of text is really long. Because the time complexity is grow quadratically. So let imagine, if we do trivial naive approach, and we have 64 character, seem we must make comparison up to 2^64 times. It becomes big deal.

### Dynamic Programming

By using memorization table to store value calculated, we save a lot of time here. In the implementation code, we only use 2 loop nested.

It costs O(n²).

Given 64 character string, we have 64^64 = **4096** vs 2^64 = **1.8E19**

Seem O(n²) looks bad, but it’s better 1000…x than O(2<sup>n</sup>).


## 2.4 Improvement

### Reduce the problem set

In most of real-world scenario, **the beginning and the ends of file rarely changed. **If only few string in middle of texts are changed, we can remove/reduce the begin and end.

By reducing or bounding the upper/lower limitation, we can save tons of memory and comparison time.

Here is presudo-code

```swift
func TrimLCS(A: String, B: String) {
    var start = 0
    var aEnd = A.length()
    var bEnd = B.length()
 
    // Get index of beginning of file
    while start < aEnd && start < bEnd && A[start] == B[start] {
        start += 1
    }
 
    // Get index of End of file
    while start ≤ aEnd and start ≤ bEnd and A[aEnd] == B[bEnd] {
        aEnd -= 1
        bEnd -= 1
    } 
 
    // Trim 
    let newA = A.trim(start..aEnd)
    let newB = B.trim(start..bEnd)
 
    // Keep doing LCS algorithm here
    for ...
}
```

### Reduce the comparison time

The most of time costs in naive which come from comparison between two characters. In some case, such as source code diff, we only want to see new line, we don’t need to see each difference character. By comparing long text, seem be more effience

### Turn string to hashes

Instead of storing long raw string, we may consider to hash or checksum. If the text’s length from 60 or longer, the hash and checksum will turn it shortly, might be only 8-40 character.

Yeah, how about drawback in Hashmap ? 🤔

1. We need more time to preprocess from both of string.
2. We need more space to be allocated for Hashmap
3. Collision in Hashmap. Since the checksum or Hashmap isn’t guarantee to be unique. There are small chance two value could be reduced to same hash.

Finally, it’s depend on your requirement or place the algorithm will execute. If you’re going to implement LCS on high-end computer, with endless memory, but have limited speed, we should consider about time complexity.

Orthewise, if on low-end device, with really small memory, please take a look a space complexity 🙌


## 3. Swift ?

Instead of implement original solution. It means only work with String. We should put more effort, and make a extension on Array. It might be difficult to understand a bit, but LCS can work with custom model as well.

If you’re not familiar with Generic, Equatable, Element in Array. Don’t hesitate to spend 30 minutes to review base-foundation in [Swift Documentation](https://developer.apple.com/library/prerelease/content/documentation/Swift/Conceptual/Swift_Programming_Language/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-ID179)

The extension look like below.

```swift
//// Extension of Array
// But we must ensure, each element must adopt with <Equatable> protocol -> We can make comparision
extension Array where Element: Equatable {}
```

### Memorization table

The first thing is Memorization table. Please take a look at visualization I draw at previous section.

We create table: [[Element]] and run two nested loop, and calculate the length of LCS by following 2 case: MATCH and NOT MATCH

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

The code above is really simple af. Just following the table on your notebook, imagine how the i,j iterate over table, check case 1, case 2, move on to next cell.

### LCS with generic Array

It’s really tricky to run recursion from bottom corner to the top. It’s same approach I mention in section 2.2.

- Start from Bottom
- If x[i] == y[j] // Match -> Move diag­o­nally
- else, we check if table[i-1][j] > table[i][j-1] -> Go Top
- else Go Left 😎

Here is detail code. I tried to write simple as possible. Please notice **lcsFromMemorizationTable** is private method. Because I don’t want people modify it.

```swift
//// Extension of Array
// But we must ensure, each element must adopt with <Equatable> protocol -> We can make comparision
extension Array where Element: Equatable {
    
    func LCS(_ other: [Element]) -> [Element] {
        
        // Build memorization table
        let table = MemorizationTable.buildTable(x: self, y: other)
        
        // Print LCS
        return self.lcsFromMemorizationTable(table, self, other, self.count, other.count)
    }
    
    fileprivate func lcsFromMemorizationTable(_ table: [[Int]], _ x: [Element], _ y: [Element], _ i: Int, _ j: Int) -> [Element] {
        
        // Exit
        if i == 0 || j == 0 {
            return []
        }
        
        // Recurvise 
        else if x[i-1] == y[j-1] { // MATCH -> Get element
            return lcsFromMemorizationTable(table, x, y, i - 1, j - 1) + [x[i-1]]
        }
        else if table[i-1][j] > table[i][j-1] { // Top
            return lcsFromMemorizationTable(table, x, y, i - 1, j)
        }
        else { // Left
            return lcsFromMemorizationTable(table, x, y, i, j - 1)
        }
    }
}
```

### Test with string

```swift
////////////////////////
////// TEST with string
 
// Convert [CharacterView] -> [String]
let a = "acbaed".characters.map {String($0)}
let b = "abcadfa".characters.map {String($0)}
 
let lcs = a.LCS(b) //
print(lcs) //  ["a", "b", "a", "d"]
```

Because LCS is working with Array<Element>, so we need to map [CharacterView] -> [String].

### Test with array of Custom model

**UserObj** is custom struct, or any kind of class. The different thing, we must adopt Equatable.

```swift
struct UserObj: CustomStringConvertible {
    let name: String!
 
    var description: String {
        return "{\(self.name!)}"
    }
}
 
// Adopt Equatable
extension UserObj: Equatable {
    public static func ==(lhs: UserObj, rhs: UserObj) -> Bool {
        return lhs.name == rhs.name
    }
}
```

Don’t forget to adopt <Equatable> protocol. If you don’t do this. Swift can’t understand how to make a comparison between **UserObj.**

Actually, even if you won’t do it on purpose, XCode will shot you an error intermediately, at build-time ❌. It’s one reason why I really love Swift – type safe language 🤗

```swift
let localUsers = [UserObj(name: "Nghia Tran"),
                  UserObj(name: "nghiatran.me"),
                  UserObj(name: "SaiGon"),
                  UserObj(name: "Algorithm")]
let remoteUsers = [UserObj(name: "Kamakura"),
                   UserObj(name: "Nghia Tran"),
                   UserObj(name: "Algorithm"),
                   UserObj(name: "SaiGon"),
                   UserObj(name: "Somewhere")]
 
let lcsUser = localUsers.LCS(remoteUsers)
print(lcsUser) // [{Nghia Tran}, {Algorithm}]
```
[Swift playground on github](”https://github.com/NghiaTranUIT/LCS-Swift/blob/master/LCS-Dynamic-Programming-Part-1.playground/Contents.swift)

## 4. Where to go from here ?

You can download full source here

[Github](https://github.com/NghiaTranUIT/LCS-Swift)

In this blog, we’ve learn how LCS algorithm actually works, how we can visualize it step by step, and how we implement LCS for generic Array in Swift too 😎

In next blog, I will cover how we can apply LCS to iOS development, especially in UITableView and UICollectionView. How we can optimize “the transform” from DataSourceA to DataSourceB without calling reloadData().

Thank everybody for reading and blow up your brain 👻


