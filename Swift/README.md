# Swift Style Guide

This style guide outlines the coding conventions of the iOS teams at Brain Craft Ltd.

# Table of Contents

- [Semicolons](#semicolons)
- [Spacing](#spacing)
- [Parentheses](#parentheses)
- [Conditionals](#conditionals)
  - [Failing Guards](#failing-guards)
  - [Ternary Operator](#ternary-operator)
- [Error Handling](#error-handling)
- [Methods](#methods)
  - [Delegates](#delegates)
  - [Button Action Naming](#button-action-naming)
- [Variables](#variables)
- [Naming](#naming)
  - [UI elements Naming](#ui-elements-naming)
  - [UINavigationController & UIViewController Naming](#uinavigationcontroller--uiviewcontroller-naming)
  - [Utility Class](#utility-class)
  - [Helper Class](#helper-class)
  - [Data Model](#data-model)
  - [Notifications](#notifications)
  - [Extensions](#extensions)
  - [Protocols](#protocols)
  - [Shared Singleton](#shared-singleton)
  - [Assets Naming](#assets-naming)
  - [Generics](#generics)
- [Comments](#comments)
- [Protocol Conformance](#protocol-conformance)
- [Literals](#literals)
  - [Syntactic Sugar](#syntactic-sugar)
  - [Use Type Inferred Context](#use-type-inferred-context)
- [`CGRect` Functions](#cgrect-functions)
- [Constants](#constants)
- [Optionals](#optionals)
- [Bitmasks](#bitmasks)
- [Struct vs Class](#struct-vs-class)
- [Read Only Properties](#read-only-properties)
- [Minimal Imports](#minimal-imports)


## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

**For example:**
```swift
let title = "Swift is an awesome language"
```

**Not:**
```swift
let title = "Swift is an awesome language";
```

## Spacing

* Indentation MUST use 4 spaces or 1 tab.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) MUST open on the same line as the statement. Braces MUST close on a new line.

  **For example:**
  ```swift
  if user.isHappy {
      // Do something
  } else {
      // Do something else
  }
  ```

* There SHOULD be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods MAY separate functionality, though this inclination often indicates an opportunity to split the method into several, smaller methods. In methods with long or verbose names, a single line of whitespace MAY be used to provide visual separation before the method’s body.

## Parentheses

Parentheses around control flow statement are not required and should be omitted.

**For example:**
```swift
if score > 90 {
    print("You are a genius!")
}
```

**Not:**
```swift
if (score > 90) {
    print("You are a genius!")
}
```

## Conditionals

When coding with conditionals, the left-hand margin of the code should be the "golden" or "happy" path. That is, don't nest if statements. Multiple return statements are OK. The guard statement is built for this.

**For example**
```swift
func computeData(context: Context?, inputData: InputData?) throws -> Data {
  guard let context = context else {
    throw SMError.noContext
  }
  guard let inputData = inputData else {
    throw SMError.noInputData
  }

  // use context and input to compute the data
  return data
}
```

**Not:**
```swift
func computeData(context: Context?, inputData: InputData?) throws -> Data {
  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the data

      return data
    } else {
      throw SMError.noInputData
    }
  } else {
    throw SMError.noContext
  }
}
```

When multiple optionals are unwrapped either with `guard` or `if let`, minimize nesting by using the compound version when possible. In the compound version, place the guard on its own line, then indent each condition on its own line. The else clause is indented to match the guard itself, as shown below.


**For example**
```swift
guard 
  let number1 = number1,
  let number2 = number2,
  let number3 = number3 
else {
  fatalError("impossible")
}
// do something with numbers
```

```swift
if 
  let number1 = number1,
  let number2 = number2,
  let number3 = number3 
{
  // do something with numbers
}
```

**Not:**
```swift
if let number1 = number1 {
  if let number2 = number2 {
    if let number3 = number3 {
      // do something with numbers
    } else {
      fatalError("impossible")
    }
  } else {
    fatalError("impossible")
  }
} else {
  fatalError("impossible")
}
```

### Failing Guards
Guard statements are required to exit in some way. Generally, this should be simple one line statement such as `return`, `throw`, `break`, `continue`, and `fatalError()`. Large code blocks should be avoided. If cleanup code is required for multiple exit points, consider using a `defer` block to avoid cleanup code duplication.

### Ternary Operator

The intent of the ternary operator, `?` , is to increase clarity or code neatness. The ternary SHOULD only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables.

**For example:**
```swift
let result = a > b ? x : y
```

**Not:**
```swift
let result = a > b ? x = c > d ? c : d : y
```

## Error Handling

When methods throws an error, code MUST NOT ignore errors. Log the error message, it might help to debug errors.

**For example:**
```swift
do {
    try doSomethingThatThrows()
} catch {
    // Handle Error
    print(error)
}
```

**Not:**
```swift
try? doSomethingThatThrows()
```

## Methods

* Naming a method is always relative and can vary from individual to individual but one thing we need to ensure the credibility of the name, a method’s purpose should easily be understood by it’s name. Apple provides some basic [guidelines](https://www.swift.org/documentation/api-design-guidelines/#naming) for developers in terms of method naming , these are mentioned below:-
  * Begin names of factory methods with “make”, e.g. `x.makeIterator()`.
  * Those with side-effects should be named as imperative verb phrases. Apply the “ed” or “ing” suffix to name its nonmutating counterpart.
    | Mutating | Nonmutating |
    | --- | --- |
    | x.sort() | z = x.sorted() |
    | x.append(y) | z = x.appending(y) |
  * Those without side-effects should be named as noun phrases. Apply the “form” prefix to name its mutating counterpart.
    | Nonmutating | Mutating |
    | --- | --- |
    | x = y.union(z) | y.formUnion(z) |
    | j = c.successor(i) | c.formSuccessor(&i) |

* Methods exceeding 80 characters SHOULD be represented like a form with a new line after each argument

    **For example:**
    ```swift
    func setExampleText(
        text: String, 
        image: UIImage,
        color: UIColor,
        alternativeText: String
    )
    ```

### Delegates

Each delegate method should start the name by identifying the class of the object that is sending the message and an unnamed first parameter should be the delegate source. 

**For example:**

```swift
func filterView(_ filterView: SMFilterView, didSelectFilterAtIndex index: Int)
func filterViewShouldReload(_ filterView: SMFilterView) -> Bool
```

**Not:**
```swift
func didSelectFilterAtIndex(filterView: SMFilterView, index: Int)
func filterViewShouldReload() -> Bool
```

### Button Action Naming

As per apple guidelines, a button action name should always start with a **verb**. As recommended, we should try to avoid using **tapped, pressed, action.** 

For example , if in **SMFilterView**  an action of done button is invoked to disappear/hide filter menu view, the done button action naming should be `disappearFilterMenuView` or `hideFilterMenuView` rather than **hideBtnPressed/Tapped/Action**.

## Variables

Variables SHOULD be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**For example:**

* `title: String`: It is reasonable to assume a “title” is a string.
* `titleHTML: String`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively.
* `titleJSONString: String`: This indicates a title that may contain **JSON String** which needs parsing for read information.
* `titleAttributedString: NSAttributedString`: A title, already formatted for display. `AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context.
* `now: Date`: No further clarification is needed.
* `lastModifiedDate: Date`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `url: URL` vs. `urlString: String`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `releaseDateString: String`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are NOT RECOMMENDED, except as simple counter variables in loops.

Colon indicating a type MUST be “attached to” the variable name. **For example,** `text: String` **not** `text :String` or `text : String`.

## Naming

Class names (along with category and protocol names) should follow **pascal case** naming convention(e.g. `PictureViewController`) start as uppercase and use mixed case to delimit words. 

Every class’s name whether a model,custom view or a view-controller  relevant to a particular project should always contain the prefix of the project to denote which project that specific class belongs to. In this documentation we will take our very own project StickerMaker by example, thus our project prefix will be `SM` and should almost always be **capital case**.


### UI elements Naming
	
The name of any UI element should contain it’s project prefix, functionality and generic class name. So the generic formula of ui elements naming should be as below:-
```
PREFIX+CLASS_NAME/CLASS_FUNCTIONALITY+GENERIC_CLASS_NAME
```
For example, let’s say we are using a subclass of `UIView` that is meant to be rounded. As per the formula mentioned above, the naming of that class should be as followed:-
```
SM(PREFIX)+Rounded(CLASS_FUNCTIONALITY)+View(GENERIC_CLASS_NAME)
```
So the name is rounded to be `SMRoundedView`. For all ui elements naming, we should stick to the above approach. Some other examples `SMLoadingView`, `SMCircleButton`.


### UINavigationController & UIViewController Naming

Naming `UIViewController`s and `UINavigationController`s are similar to the approach of UI elements Naming. However there are fundamentally two types of ViewControllers or NavigationControllers observed in any project. They are BaseViewControllers and screen based ViewControllers we will try to cover both aspects of naming below:-

* **BaseViewController:-** The naming of base ViewController is pretty straightforward. It comprises Project prefix and generic class name. 
    ```
    PREFIX+GENERIC_CLASS_NAME
    ```
  According to the approach, the base classes of our sample project **StickerMaker** should be named as followed :-  `SMNavigationController`, `SMViewController`.

* **Screen Based ViewControllers:-** Almost as similar as to the naming approach of UI elements. 
    ```
    PREFIX+CLASS_NAME/CLASS_FUNCTIONALITY+GENERIC_CLASS_NAME
    ```
Just to give an example, the home page or landing page of StickerMaker is named like `SMHomeViewController`.


### Utility Class

Every project SHOULD have classes where all the utility methods (like conversion from one class to another, permissions etc.) that might be used throughout the project will be declared and defined. Generally these classes are considered as Utility classes. The naming approach is pretty similar to the other approaches.
```
PREFIX+Utility/UtilityManager
```

`SMUtility` or `SMUtilityManger` are prime examples of Utility class naming.


### Helper Class 

Applications require classes that revolve around specific aspects of a particular project. For example, almost all the applications need to make server requests and manage their responses. In cases like these, we should always try to introduce a helper class to make things more clean and manageable. The helper class nomenclature is provided below:-
```
PREFIX + FUNCTION_TYPE + MANAGER
```
As examples, a class for overseeing the server request’s naming should be :-
```
SM(PREFIX)+Server(FUNCTION_TYPE)+Manager(GENERIC_NAME)
```
Similarly, for managing a layer the class could be named as :- `SMLayerManager`.


### Data Model 

Data Model or Model classes are an integral part of any project, these classes help us to organize our stored data in a structured way. Usually, model classes should be used to prepare a data source that may be used for later purpose, it is an alternative representation  of dictionary, more crash-resistant and convenient to use. The naming convention of model classes are mentioned below:-
```
PREFIX + STORED_OBJECT_TYPE + DATAMODEL/MODEL
```
To store some information about CIFilters we might utilize model classes whose properties might be hue,brightness,saturation etc. The model should be named :- `SMFilterDataModel`.


### Notifications

To broadcast events across our applications, we use notifications, notifications name should be associated with it’s triggering event, time and sender. 

```
PREFIX + NAME_OF_ASSOCIATED_CLASS + DID/WILL + EVENT + NOTIFICATION
```
Let’s say that we need to send a notification after a screen let’s call it home vc gets dismissed. As per convention the name will be :- `SMHomeVCDidDismissNotification`.


### Extensions

Methods and properties added in extensions MUST be named with an app or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two extensions from different libraries adding a method of the same name.

**For example:**

```swift
extension Array {
    func nyt_object(at index: Int) -> Element?
}
```

**Not:**

```swift
extension Array {
    func object(at index: Int) -> Element?
}
```

### Protocols

Protocols naming  follow the same approach as other classes. It can be generalized as below:-

```
	PREFIX + CLASS_NAME + DELEGATE
```
A protocol utilized for filter view in StickerMaker project is named as :-  `SMFilterViewDelegate`.


### Shared Singleton

When implementing a singleton design pattern, the singleton class name should always be associated with **Shared** and **Manager** and it’s **prefix**. The nomenclature can be formulated as :-
```
PREFIX + Shared + OBJECTIVE + Manager
```
If a class is destined to manage purchase related chores , the naming should be `SMPurchaseManager`.


### Assets Naming

Assets names should be named consistently to preserve organization and developer sanity. Assets SHOULD be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Assets that are used for a similar purpose SHOULD be grouped in respective groups in an Asset folder or Asset Catalog.


### Generics

Generic type parameters should be descriptive, upper camel case names. When a type name doesn't have a meaningful relationship or role, use a traditional single uppercase letter such as `T`, `U`, or `V`.

**For example:**

```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**Not:**
```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

## Comments

When they are needed, comments SHOULD be used to explain **why** a particular piece of code does something. Any comments that are used MUST be kept up-to-date or deleted.

Block comments are NOT RECOMMENDED, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

Extensions are RECOMMENDED to concisely segment functionality and `MARK` comment should be added to describe the functionality.

**For example:**

```swift
// MARK: - NYTMediaPlaying
extension UIViewController {

}
```

**Not:**

```swift
extension NYTAdvertisement {

}
```

## Protocol Conformance

In particular, when adding protocol conformance to a model, prefer adding a separate extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

**For example:**

```swift
class MyViewController: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**Not:**
```swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

## Literals

`String`, `Dictionary`, `Array`, and `Number` literals SHOULD be used whenever creating instances of those objects.

**For example:**

```swift
let names = ["Brian", "Matt", "Chris", "Alex", "Steve", "Paul"]
let productManagers = ["iPhone": "Kate", "iPad": "Kamal", "Mobile Web": "Bill"]
let shouldUseLiterals = "Literals are easy"
let buildingZIPCode = 10018
```

**Not:**

```swift
let names = Array(arrayLiteral: "Brian", "Matt", "Chris", "Alex", "Steve", "Paul")
let productManagers = Dictionary(dictionaryLiteral: ("iPhone", "Kate"), ("iPad", "Kamal"), ("Mobile Web", "Bill"))
let shouldUseLiterals = String("Literals are easy")
let buildingZIPCode = Int(10018)
```

### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**For example:**

```swift
var foods: [String]
var recipes: [Int: String]
var marks: Int?
```

**Not:**

```swift
var foods: Array<String>
var recipes: Dictionary<Int, String>
var marks: Optional<Int>
```

### Use Type Inferred Context

Use compiler inferred context to write shorter, clear code. (Also see [Type Inference](https://github.com/kodecocodes/swift-style-guide#type-inference).)

**For example:**

```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**Not:**
```swift
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
```

## `CGRect` Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, code MUST use the [`CGGeometry` functions](https://developer.apple.com/documentation/coregraphics/cggeometry) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```swift
let frame = view.frame

let x = CGRectGetMinX(frame)
let y = CGRectGetMinY(frame)
let width = CGRectGetWidth(frame)
let height = CGRectGetHeight(frame)
```

**Not:**

```swift
let frame = view.frame

let x = frame.origin.x
let y = frame.origin.y
let width = frame.size.width
let height = frame.size.height
```

## Constants
**Based on Decision**

Constants are RECOMMENDED over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. You can define constants on a type rather than on an instance of that type using type properties. To declare a type property as a constant simply use `static let`. Type properties declared in this way are generally preferred over global constants because they are easier to distinguish from instance properties. 

**For example:**

```swift
enum Math {
  static let e = 2.718281828459045235360287
}

let result = side * Math.e
```

## Optionals

Declare variables and function return types as optional with `?` where a `nil` value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad()`. Prefer optional binding to implicitly unwrapped optionals in most other cases.

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
textContainer?.textLabel?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let textContainer {
  // do many things with textContainer
}
```

When naming optional variables and properties, avoid naming them like `optionalString` or `maybeView` since their optional-ness is already in the type declaration.

For optional binding, shadow the original name whenever possible rather than using names like `unwrappedView` or `actualLabel`.

**For example:**

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}

// another example
resource.request().onComplete { [weak self] response in
  guard let self = self else { return }
  let model = self.updateModel(response)
  self.updateUI(model)
}
```

**Not:**

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let strongSelf = self else { return }
  strongSelf.alpha = 1.0
}
```

## Bitmasks

When working with bitmasks, the `OptionSet` protocol MUST be used.

**Example:**

```swift
struct NYTAdCategory: OptionSet {
    let rawValue: UInt32
    
    static let autos      = NYTAdCategory(rawValue: 1 << 0)
    static let jobs       = NYTAdCategory(rawValue: 1 << 1)
    static let realState  = NYTAdCategory(rawValue: 1 << 2)
    static let technology = NYTAdCategory(rawValue: 1 << 3)
}
```

## Struct vs Class

In Swift, structs and classes are two types of data structures with distinct semantics. 

Structs have [value semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_144) and should be used for simple data types that don't require reference semantics or complex inheritance. Arrays are a good example of a struct because they represent values that are interchangeable and have no identity. 

Classes have [reference semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_145) and should be used for more complex types that require a unique identity or reference semantics. You would model a person as a class because two person objects are two different things. Just because two people have the same name and birthdate, doesn't mean they are the same person. But the person's birthdate would be a struct because a date of 3 March 1950 is the same as any other date object for 3 March 1950. The date itself doesn't have an identity.

Sometimes, things should be structs but need to conform to `AnyObject` or are historically modeled as classes already (`NSDate`, `NSSet`). Try to follow these guidelines as closely as possible.

## Read Only Properties

If a property need only to be read from any outside class, the property MUST declare as `private(set)`. 

**For example:**

```swift
class NYTAdvertisement : NSObject {
    private(set) var googleAdView: GADBannerView
    
    private var iAdView: ADBannerView
    private var adXWebView: UIWebView
}
```

## Minimal Imports

Import only the modules a source file requires. For example, don't import `UIKit` when importing `Foundation` will suffice. Likewise, don't import `Foundation` if you must import `UIKit`.

**For example:**

```swift
import UIKit

var view: UIView
var deviceModels: [String]
```

**Not:**

```swift
import UIKit
import Foundation

var view: UIView
var deviceModels: [String]
```

**For example:**

```swift
import Foundation

var deviceModels: [String]
```

**Not:**

```swift
import UIKit

var deviceModels: [String]
```