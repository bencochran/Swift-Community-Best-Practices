# Swift-Best-Practices

Best practices for software development with Swift.

## Preface

See: https://github.com/schwa/SwiftGraphics/blob/develop/Documentation/Notes.markdown

## Style Guide

This document is (generally) not a style guide. Use the style Apple has defined within their “[The Swift Programming Language][1]” book. This document will contain further clarifications where necessary.

[1]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html

### Naming

As per the “Swift Programming Language” types should be upper camel case (example: “VehicleController”). Variables and constants should be lower camel case (example “vehicleName”).

The only exception to this general rule are enum values, which should be uppercase:

	enum Planet {
	    case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
	}

### self

Let the compiler infer self in all cases where it is able to. Areas where self should be explicitly used includes setting parameters in init, and non capturing closures. For example

	struct Example {
		let name:String

  	init(name:String) {
	    	self.name = name
		}
	}

## Needless abbreviations

## Type inference

Where possible use Swift’s type inference to help reduce redundant type information. For example prefer:

    var currentLocation = Location()

to:

    var currentLocation: Location = Location()

## Constants

Constants used within type definitions should be declared static. For example:

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

Making the constants static allow them to be refered to without instances of the type. They also allow other code to access the constants.

Constants at global level should be avoided.

Constants at the function level are fine.

## Early Returns

## Error handling

## Reference vs value types

## Chained Methods

## Async Closures

## unowned vs weak

## Cocoa Delegates

## Immutable structs

## Instance initialisation

## print/println

## Logging

## log.debug statements

## Computed properties

Use the short version of computed properties if you only need to implement a getter. For example, prefer this:

    class Example {
        var age {
            return arc4random()
        }
    }

to this:

    class Example {
        var age {
            get {
                return arc4random()
            }
        }
    }

If you add a set or a didSet to the property then you will need to explicitly provide a get.

## Computed Properties vs Functions

## Value types and equality

## Converting Instances

Converting instances from one type to another can be achieved in one of three ways:

```swift
struct Thing {
	var CGColor: CGColor {
		return CoreGraphics.CGColor()
  }
}
```


