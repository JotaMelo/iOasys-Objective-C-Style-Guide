# iOasys Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at iOasys.

[Based on The News York Times Objective-C Style Guide](https://github.com/NYTimes/objective-c-style-guide).

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn’t mentioned here, it’s probably covered in great detail in one of these:

* [The Objective-C Programming Language](https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html#//apple_ref/doc/uid/TP30001163)
* [Cocoa Fundamentals Guide](https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html#//apple_ref/doc/uid/TP40002974-CH1-SW1)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
  * [Constants](#constants)
  * [Categories](#categories)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Bitmasks](#bitmasks)
* [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Imports](#imports)
* [Protocols](#protocols)
* [View Controllers](#view-controllers)
* [Dead Code](#dead-code)
* [Xcode Project](#xcode-project)

## Dot Notation Syntax

Dot notation should **always** be used for getting and setting properties. Always check the class header if unsure.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
textField.text = string.lowercaseString;
UIApplication.sharedApplication.delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
[textField setText:[string lowercaseString]];
[[UIApplication sharedApplication] delegate];
```

## Spacing

* Indentation must use 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Specifically, method braces should open in a new line. If preferred it can be on the same line as the statement, in which case you must have a new line separating the method declaration from its implementation.

**For example:**
```objc
// preferred
- (NSInteger)randomNumber
{
    return 4;
}

// also accepted
- (NSInteger)randomNumber {
    
    return 4;
}
```

* Other braces (`if`/`else`/`switch`/`while` etc.) must open on the same line as the statement. The final brace must close on a new line.

**For example:**
```objc
if (user.isHappy) {
    // Do something
} else {
    // Do something else
}
```

* There should be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods may separate functionality, though this inclination often indicates an opportunity to split the method into several, smaller methods.

## Conditionals

Conditional bodies must use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) can happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The intent of the ternary operator, `?` , is to increase clarity or code neatness. The ternary should only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error Handling

When methods return an error parameter by reference, code must switch on the returned value and must not switch on the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

* In method signatures, there must be a space after the scope (`-` or `+` symbol). There must be a space between the method segments. There must not be a space before the variable name.


**For example:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

**Not:**
```objc
-(void) setExempleText:(NSString*)text image:(UIImage*) image;
```

* You can separate method segments with new lines, as long as they're colon aligned. Although this should not be done in methods where one of the parameters is an inline block.

**For example:**
```objc
- (void)methodWithAReallyLongNameThatTakesThisParameter:(NSString *)title
                                  andThisOtherParameter:(NSUInteger)count;

[self methodWithAReallyLongNameThatTakesThisParameter:@"Hey there"
                                andThisOtherParameter:10];
```

## Variables

Variables should be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**For example:**

* `NSString *title`: It is reasonable to assume a “title” is a string.
* `NSString *titleHTML`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively._
* `NSAttributedString *titleAttributedString`: A title, already formatted for display. _`AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context._
* `NSDate *now`: _No further clarification is needed._
* `NSDate *lastModifiedDate`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `NSURL *URL` vs. `NSString *URLString`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `NSString *releaseDateString`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are not recommended, except as simple counter variables in loops.

Asterisks indicating a type is a pointer must be “attached to” the variable name. **For example,** `NSString *text` **not** `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const NYTConstantString`).

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information, see [Apple’s docs on using accessor methods in initializer methods and `dealloc`](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface NYTSection: NSObject

@property (strong, nonatomic) NSString *headline;

@end
```

```objc
- (void)updateUI
{
    self.textField.text = self.headline;
}
```

**Not:**

```objc
- (void)updateUI
{
    _textField.text = _headline;
}
```

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

```objc
- (void)updateUI
{
    textField.text = headline;
}
```

#### Property Qualifiers

Properties should always have at least two qualifiers. Qualifiers must be in this order:
* ```class```
* ```weak``` / ```strong``` / ```copy``` / ```assign```
* ```nonatomic``` / ```atomic```
* ```readonly```
* ```nonnull``` / ```nullable```

Primitives (```BOOL```, ```NSInteger```, ```CGFloat``` etc) are always ```assign```, outlets and delegates are always ```weak```, blocks are always ```copy```. Public properties should always specify nullability.

**For exemple:**
```objc
@property (weak, nonatomic) IBOutlet UITableView *tableView;
@property (strong, nonatomic) NSString *name;
@property (assign, nonatomic) NSUInteger currentIndex;
@property (copy, nonatomic) void(^callbackBlock)(id response);
```

**Not:**
```objc
@property (nonatomic, strong) IBOutlet UITableView *tableView;
@property (strong) NSString *name;
@property (nonatomic) NSUinteger currentindex;
@property void(^callbackBlock)(id response);
```

#### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed before the variable type, e.g., `__weak NSString *text`. 

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good. **Abbreviations are not good**.

**For example:**

```objc
UIButton *settingsButton;
```

**Not:**

```objc
UIButton *sttgsBtn;
```

Properties and local variables must be camel-case with the leading word being lowercase.

Instance variables must be camel-case with the leading word being lowercase, and must be prefixed with an underscore (but you should not create these yourself). This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

### Constants

Private constants (e.g. defined in the class' implementation file) should use camel case names beginning with "k":
```objc
static CGFloat const kCellHeight = 50.f;
```

Public constants (defined in the header file) should be prefixed by the class name:
```objc
// header file
FOUNDATION_EXPORT CGFloat const UserCellHeight;

// implementation file, before @interface
CGFloat const UserCellHeight = 50.f;
```

### Categories

Categories are recommended to concisely segment functionality and should be named to describe that functionality.

**For example:**

```objc
@interface UIViewController (NYTMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Not:**

```objc
@interface NYTAdvertisement (private)
@interface NSString (NYTAdditions)
```

Methods and properties added in categories devem be named with an app or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

**For example:**

```objc
@interface NSArray (NYTAccessors)
- (id)nyt_objectOrNilAtIndex:(NSUInteger)index;
@end
```

**Not:**

```objc
@interface NSArray (NYTAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init and dealloc

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` methods should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

```objc
- (instancetype)init 
{
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

When initializing objects, prefer `[Object new]` over `[[Object alloc] init]` and other class methods when possible.

## Literals

* `NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.
* There should **always** be a space after commas and colons, but **never** before.
* It's recommended to use generics to specify array and dictionary types. This improves code completion and clarity.

**For example:**


```objc
NSArray<NSString *> *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSMutableArray<NSString *> *platforms = @[@"macOS", @"iOS", @"watchOS", @"tvOS"].mutableCopy;
NSDictionary<NSString *, NSString *> *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSMutableArray *platforms = [NSMutableArray arrayWithObjects:@"macOS", @"iOS", @"watchOS", @"tvOS", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## Constants

Constants are recommended over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants must be declared as `static` constants. Constants **must not** be declared as `#define`, leave that only for macros.

**For example:**
```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static CGFloat const NYTImageThumbnailHeight = 50.0;
```

**Not:**
```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, the new fixed underlying type specification must be used; it provides stronger type checking and code completion. The SDK includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`.

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Bitmasks

When working with bitmasks, the `NS_OPTIONS` macro must be used.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. In most cases, outlets should be private.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

## Booleans

Values must not be compared directly to `YES`, because `YES` is defined as `1`, and a `BOOL` in Objective-C is a `CHAR` type that is 8 bits long (so a value of `11111110` will return `NO` if compared to `YES`). Comparing to `NO` or `nil` should also be avoided.

**For an object pointer:**

```objc
if (!someObject) {
}
```

**Not:**
```objc
if (someObject == nil) {
}
```

**For a `BOOL` value:**

```objc
if (isAwesome)
if (!someNumber.boolValue)
```

**Not:**

```objc
if (isAwesome == YES) // Never do this.
if (someNumber.boolValue == NO) // !someNumber.boolValue is preferred
```

`YES` and `NO` are the only acceptable values for `BOOL`, **do not** use `TRUE` / `true` / `FALSE` / `false`.

If the name of a `BOOL` property is expressed as an adjective, the property’s name may omit the `is` prefix but should specify the conventional name for the getter.

**For example:**

```objc
@property (assign, getter=isEditable) BOOL editable;
```

_Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)._

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedManager
{
    static SomethingManager sharedManager = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedManager = [SomethingManager new];
    });

    return sharedManager;
}
```
This will prevent [possible and sometimes frequent crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

Also remember to declare singletons as read-only class properties:

```objc
@property (class, nonatomic, readonly) SomethingManager *sharedManager;
```

## Imports

If there is more than one import statement, statements must be grouped [together](http://ashfurrow.com/blog/structuring-modern-objective-c). Groups may be commented.

```objc
// Frameworks
#import <CoreLocation/CoreLocation.h>

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"
```

## Protocols

In a [delegate or data source protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), the first parameter to each method should be the object sending the message.

This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

**For example:**

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**Not:**

```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```

When declaring conformance to protocols, prefer to place them in class extensions in the implementation file, unless the class really **needs** others objects to know about its conformance.

**For exemple:**
```objc
@interface NYTAdvertisement () <UITableViewDataSource, UITableViewDelegate>
@end
```

## View Controllers

Lifecycle methods, when used, should be declared in this order:
* `viewDidLoad`
* `viewWillAppear:`
* `viewDidAppear:`
* `viewWillDisappear:`
* `viewDidDisappear:`

The storyboard ID of view controllers should be the same as the class. A view controller named `HomeViewController` should a have a storyboard ID `HomeViewController`.

## Dead Code

Methods that are not called anywhere in the code or have an empty implementation are considered dead code. These are the default methods defined for you by Xcode when creating a new view controller:
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

/*
#pragma mark - Navigation

// In a storyboard-based application, you will often want to do a little preparation before navigation
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    // Get the new view controller using [segue destinationViewController].
    // Pass the selected object to the new view controller.
}
*/
```
If you're not doing anything in `didReceiveMemoryWarning` or `viewDidLoad`, you should delete them. The same applies for the commented `prepareForSegue:sender:`, if you're not using it, delete it.

## Xcode project

Files should be grouped not only by type, but also by feature.

**For example:**
```
- Models
    User.h
    User.m
    Order.h
    Order.m
- View Controllers
    InitialViewController.h
    InitialViewController.m
    - Home
        HomeViewController.h
        HomeViewController.m
    - Order
        - Cart
            CartViewController.h
            CartViewController.m
            OrderItemsTableViewController.h
            OrderItemsTableViewController.m
        - Checkout
            CheckoutViewController.h
            CheckoutViewController.m
            - Payment
                CreditCardFormViewController.h
                CreditCardFormViewController.m
                CreditCardScannerViewController.h
                CreditCardScannerViewController.m
```

Groups should not be used with the sole purpose of grouping a single class' header and implementation file, although it is accepted if a certain feature has only one class.

Warnings are bad. Avoid any warning as much as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
