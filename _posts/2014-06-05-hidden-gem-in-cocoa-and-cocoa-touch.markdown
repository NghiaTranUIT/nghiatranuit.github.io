---
layout: post
title: Hidden Gem in Cocoa and Cocoa Touch
date: '2014-06-05 13:37:16'
---

Hello my friend, this is my first time I have written blog about iOS. With 2 year exp develop iOS app.

There are many thing I learned from ebook and internet. I think right now is the best time  I should start to share everything I know to you.

`When you teach someone, you learn twice !`

In this blog, I will focus to something hide in Cocoa framework.

If you are a experienced developer iOS, even you are beginner. I think this blog is worth reading. Trust me ;]


# I divide my blog to many Section.

You can extent and close easily. Making my blog is clearly.


## Override Description

NSLog is useful method when you wanna know object’ information when debugging.

NSLog is supported log many object in Cocoa framework like NSString, NSArray, NSDictionary,… and many class in UIKit.

But if you have custom object, when you log it.

It is just log what class your object is and your object’s address in memory. Sometime, It don’t have enough what you want.

So there is a simple way to approach it.

Just override `-(NSString *) description`.

Here is sample :

```objc
@interface MyClass : NSObject
 
@property (strong, nonautomatic) NSString *myInfo;
 
@property (assign) CGFloat myFloat;
 
@end
 
// Your&nbsp;implementation
 
@implementation&nbsp;MyClass
-(NSString *) description // Override this method
{
     return [NSString stringWithFormat:@"This is my description with custom info | myInfo = %@ , myFloat = %.2f ",_myInfo,_myFloat)];
}
@end
```

And your log like this :

This is my description with custom info | myInfo = NghiaTran , myFloat = 10.0

So, when you need more information when log custom object, you just override it simply.

**NSExpression**

Stand back, I’m going to try math.

```objc
NSString *mathExpression = @"3 + 5 *(12 - 6) / 100 + 2e10"
```

If this is your exercises, it’s very solve it.

But Can you solve this expression by your code ?

Whoa, this is NOT easily. But in Cocoa has some special for you.

NSExpression is a recuse.

```objc
NSString *mathExpression = @"3 + 5 *(12 - 6) / 100 + 2e10"
 
NSExpression *e = [NSExpression expressionWithFormat:mathExpression, nil];
 
NSNumber *result = [e expressionValueWithObject:nil context:nil];
```

This class solve more than easily.

Don’t need a big program with many method, analystic in it.

This save your day.

**Trick**

I collect useful trick when you deal with Collection like NSArray, NSDictionary.

Reverse array quickly inline.

```objc
NSArray *numbers = @[ @1, @2, @3 ];
 
NSArray *reversed = numbers.reverseObjectEnumerator.allObjects;
```

### NSValue

```objc
typedef struct RGB
 
{
 
float red, green, blue;
 
} _RBG;
 
RGB color = {1.0 , 1.0 , 0);
 
// Use NSValue to hold your struct
 
NSValue *myStruct = [NSValue valueWithBytes:&color objCType:@encode(RGB)];
```

### Key-value coding

```objc
NSArray *arrString = @{@"first",@"second",@"third"};
 
// Convert all member in array to UpperCase
 
[arrString valueForKey:@"uppercaseString"]
 
// And log like this
 
// {FIRST, SECOND, THIRD}
```

**NSDataDetector**

In some situation, your app should detect what is useful info in a long email or note.

![HiddenGem_1](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/HiddenGem_1.png?resize=845%2C416)

to

![HiddenGem_2](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/HiddenGem_2.png?resize=841%2C408)

Cocoa have awesome class – NSDataDetector was built-in.

NSDataDetector can detect :

1. Dates
2. Link
3. Phone Number
4. Address
5. Transit infomation

Here is example :

```objc
NSString *string = @"123 Main St. / (555) 555-5555";
 
NSError *error;
 
NSDataDetector *detector = [NSDataDetector dataDetectorWithTypes:NSTextCheckingTypeLink | NSTextCheckingTypePhoneNumber error:&error];
 
[detector enumerateMatchesInString:string options:kNilOptions range:NSMakeRange(0, [string length]) usingBlock:^(NSTextCheckingResult *result, NSMatchingFlags flags, BOOL *stop) {
 
NSLog(@"Match: %@", result);
 
}];
```

**CFStringTransform**

Have ever you hear or used CFStringTransform in your app ?

This is the last Gem in Cocoa I will show you.

It will strip accents and diacritics. It will name Unicode characters. It will encode XML hex entities which is useful if you’re ever, you know, making XML or decoding XML.

And you can also transliterate between writing systems which is kind of mind-blowing whenever you see it in action.

So here it is, meet CFStringTransform.

```objc
Boolean CFStringTransform (
 
CFMutableStringRef string,
 
CFRange *range,
 
CFStringRef transform,
 
Boolean reverse
 
);
```

1. The first argument Â is a CFMutableStringRef which you should remember NSMutableString is toll-free bridge.
2. The second is range of string you want to apply transform. If you want to transform to entity string, you just pass NULL.
3. This is a kind of transform.
4. And then, some of those transformation can be reverse. Should pass YES if you want or NO

Here is the first sample :

```objc
NSMutableString *string = [@"Énġlišh låcks iñterêsţing diaçrïtičş" mutableCopy];
 
CFStringTransform((__bridge CFMutableStringRef)(string), NULL, kCFStringTransformStripCombiningMarks, NO);
 
// => This string will transformÂ to "English lack interesting diacities"
```

Show what about Emoji ?

CFStringTransform can translate Emoji character to english.

```objc
NSMutableString *string = [@"<Pig Face>" mutable];
 
CFStringTransform((__bridge CFMutableStringRef)(string), NULL, kCFStringTransformToUnicodeName, NO);
 
// => Pig Face
````

The last gem I will show you is a ability to transform non-latin character to latin character.

If mean, if your app has some text in Japanese like “こんにちは” and you want to convert it to pronunciation for speech easily.

Sample :

```objc
NSMutableString *string = [@"こんにちは" mutableCopy];
 
CFStringTransform((__bridge CFMutableStringRef)(string), NULL, kCFStringTransformToLatin, NO);
 
// => pronunciation of this word is Kon'nichiwa
```

You can gain more User Experience.

So, there are many hidden gem in Cocoa and Cocoa Touch. With it, you can implement awesome featured in your iOS app.

Thank for reading ;]


