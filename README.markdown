# Swift-Best-Practices

Best practices for software development with Swift.

## Note to contributors

Please make sure all examples are runnable. This markdown will be converted to a Mac OS X playground.

## Preface

See: https://github.com/schwa/SwiftGraphics/blob/develop/Documentation/Notes.markdown

## Golden Rules

Never write code merely to attempt to reduce the number of keystrokes you need to type. Rely on autocompletion, autosuggestion, copy and paste, etc instead.

## Style Guide

This document is not intended to be a style guide. Although some style advice is given, it's clear that the world doesn't need more language style debats and argument. Use the style Apple has defined within their “[The Swift Programming Language][1]” book. This document will contain further clarifications where necessary.

[1]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html

### Whitespace

Tabs are four spaces. It's the Xcode default. Deal with it.

### Naming

As per the “Swift Programming Language” types should be [upper camel case][1] (example: “VehicleController”).

[1]: https://en.wikipedia.org/wiki/Studly_caps

Variables and constants should be lower camel case (example “vehicleName”).

You should rely on Swift modules to help namespace your code and not use Objective-C style class prefixes for Swift code (unless of course interfacing with Objective-C).

Do not use any form of [Hungarian notation][1] (e.g. k for constants, m for methods), instead use short concise names and use Xcode's type Quick Help (⌥ + click) to discover a variable's type. Similiarly do not use [SNAKE_CASE][2].

[1]: https://en.wikipedia.org/wiki/Hungarian_notation
[2]: https://en.wikipedia.org/wiki/Snake_case

The only exception to this general rule are enum values, which should be uppercase (this follows Apple's "Swift Programming Language" style):

```swift
enum Planet {
    case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
```
Needless contractions and abbreviations should be avoided where at all possible, you can actually type out the characters "ViewController" without any harm, rely on Xcode's autocompletion to save you typing in the future. Extremely common abbreviations such as URL are fine. Abbreviations should be represented all uppercase ("URL") or all lowercase "url" as appropriate: use the same rule for types and variables: if url was a type it would be uppercase, if url was a variable it would be lower case.

## Comments

Comments should _not_ be used to disable code. Commented out code is dead code and pollutes your source. If you want to remove code but keep it around in case it's useful in the future you should be relying on git and/or your bug tracker.

## Type inference

Where possible use Swift’s type inference to help reduce redundant type information. For example, prefer:

```swift
var currentLocation = Location()
```

to:

```swift
var currentLocation: Location = Location()
```

### self inference

Let the compiler infer self in all cases where it is able to. Areas where self should be explicitly used includes setting parameters in init, and non-escaping closures. For example

```swift
struct Example {
	let name:String

	init(name:String) {
		self.name = name
	}
}
```

## Capture List inference

Failure to infer type inside a capture list could lead to a rather verbose line of code. Only specify types if needed.

```swift

let people = [
    ("Mary", 42]),
    ("Susan", 27),
    ("Charlie", 18),
]

let strings = people.map() {
	(name:String, age:Int) -> String in
	return "\(name) is \(age) years old"
}
```

If at all possible remove the types if the compiler can infer them:

```swift

let strings = people.map() {
	(name, age) in
	return "\(name) is \(age) years old"
}
```

Using the numbered parameter names ("`$0`") further reduces verbosity, often elliminating capture lists completely. Only use the numbered form when the parameter names add no further information to the closure (e.g. very simple maps and filters).

Apple can and will change the parameter types of closures (optionals removed or changed to auto-unwrapping etc). Intentionally under-specifying your optionals and relying on Swift to infer the information it needs, reduces the risk of the code breaking under these circumstances.

(see also: http://www.russbishop.net/swift-capture-lists)

## Constants

Constants used within type definitions should be declared static. For example:

```swift
class PhysicsModel {
    static var speedOfLightInAVacuum = 299_792_458
}

class Spaceship {
    static let topSpeed = PhysicsModel.speedOFlightInAVacuum
    var speed:Double

    func fullSpeedAhead() {
        speed = Spaceship.topSpeed
    }
}
```

Making the constants static allow them to be refered to without instances of the type. They also allow other code to access the constants.

Constants at global level should be avoided except for singletons.

## Computed properties

Use the short version of computed properties if you only need to implement a getter. For example, prefer this:

```swift
class Example {
    var age {
        return arc4random()
    }
}
```

to this:

```swift
class Example {
    var age {
        get {
            return arc4random()
        }
    }
}
```

If you add a set or a didSet to the property then you will need to explicitly provide a get.

```swift
class Person {
    var age {
        get {
            return arc4random()
        }
		didSet {
			print("Time flies like a banana")
		}
    }
}
```

## Converting Instances

When creating code to convert instances from one type to another use either "to" methods, e.g.:

```swift
struct Mood {
	func toColor() -> NSColor {
		return NSColor.blueColor()
  }
}
```

Or `init()` methods:

```swift
extension NSColor {
	convenience init(_ mood:Mood) {
		super.init(color:NSColor.blueColor)
	}
}
```

While you might be tempted to use a getter, e.g.g:

```swift
struct Mood {
	var color: NSColor {
		return NSColor.blueColor()
    }
}
```

getters should generally be limited to returning components of the receiving type. For example returning the area of a `Circle` instance is well suited to be a getter, but converting a `Circle` to a `CGPath` is better as a "to" function or an `init()` extension on `CGPath`.

## Singletons

Singletons are simple in Swift:

```swift
class ControversyManager {
    static let sharedInstance = ControversyManager()
}
```

The Swift runtime will make sure that singleton is created and accessed in a a thread-safe manner.

Singletons should generally just be named "sharedInstance" unless you have a compelling reason to name it otherwise

Because singletons are so easy in Swift and because consistent naming saves you so much time you will have even more chances to complain about how singletons are an antipattern and should be avoided at all costs. Your fellow developers will thank you.

## Extensions for code organisation

Extensions should be used to help organise code.

Methods and properties that are peripheral to an instance should be moved to an extension. Note that not all property types can be moved to an extension - do the best you can within this limitation.

You should use extensions to help organise your instance definitions. One good example of this is a view controller that implements table view data source and delegate protocols. Instead of mixing all that table view code into one class, put the data source and delegate methods onto extensions that adopt the relevent protocol.

Inside a single source file feel free to break down a definition into whatever extensions you feel best organise the code in question. Don't worry about methods in the main class or struct definition referring to methods or properties inside extensions. As long as it's all contained within one swift file it is all good.

Conversely the main instance definition should not refer to elements defined in extensions outside of the main swift file..

## Chained Setters

Do not use chained methods as a more "convenient" replacement for property setters:

Prefer:

```
instance.foo = 42
instance.bar = "xyzzy"
```

to:

```
instance.setFoo(42).setBar("xyzzy")
```

Traditional setters are far easier and require far less boilerplate code than chainable setters.

## Error handling

Swift 2's do/try/catch mechanism is fantastic. Use it. (TODO: provide examples)

## Avoid try!

In general prefer:

```
do {
    try somethingThatMightThrow()
}
catch {
    fatalError("Something bad happened.")
}
```

To plain `try!`. Even though this form is far more verbose it provides context to other developers and hopefully useful information in the log.

It is ok to use try! as a temporary error handler until a more comprehensive error handling strategy is evolved. But suggest you periodically sweep your code for any errant `try!` that might have snuck past your code reviews and convert them to the more verbose syntax.

## Avoid try? where possible

`try?` is used to "squelch" errors and is only useful if you truly don't care if the error is generated. In general though you might want to catch the error and at least log the failure.

## Early Returns & Guards

## Reference vs value types

## Async Closures

## unowned vs weak

## Cocoa Delegates

## Immutable structs

## Instance initialisation

## Logging

## print/println

## log.debug statements

## Computed Properties vs Functions

## Value types and equality
