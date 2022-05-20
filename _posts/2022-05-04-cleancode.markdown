---
layout: post
title: "Clean Code"
date: 2022-05-04 00:00:00 +0000
categories: Clean Coding
---

### What exactly does **Clean Code** mean ?

> Abstraction is evil. Code is anti-evil, and clean code is perhaps divine.

Instead of a vague definition, let's show it by Swift examples

## 1. Making your code readable is as important as making it executable

> You should name your variables using the same care with which you name a first-born child.

# Unclean :

```swift
var i : Int // number of carrots in soup
var y : Int // number of fruits in my cart

func xyz(a:Int, b:Int) -> [Abc] {
    // function code
}
```

# Clean :

```swift
var numberOfCarrotsInSoup : Int
var numberOfFruitsInMyCart : Int

func getSoupsWithCaloriesBetween(_ minCalories:Int, _ maxCalories :Int) -> [Soup] {
    // function code
}
```

## 2. Functions should do one thing

Read first rule again.
Then, functions should be small, indented, with few or zero parameters.

# Unclean :
```swift
func peelAndCut(_ myVegetable:Vegetable) {
    // Logic code to peel and cut a vegetable.
}
```

# Clean :

```swift
func peel(_ myVegetable:Vegetable) {
    // Logic code to peel a vegetable.
}

func cut(_ myVegetable:Vegetable) {
    // Logic code to cut a vegetable.
}
```

## 3. Is comment my enemy ?

Spoiler alert : NO ! But ...

> Don't comment bad code - rewrite it.

Do you prefer spending time writing comments to explain the mess you've made ?
Or spend it cleaning that mess ?

# Unclean :
```swift
// Check to see if my vegetable is good to peel
if myVegetable.isRipe && myVegetable.hasSkin {

}
```

# Clean :

```swift
if myVegetable.isGoodToPeel {

}
```

What is good a good comment ?

### legals

```swift
//  Created by Thomas Mary on 11/02/2022.
//  Copyright © 2022 Thomas Mary. All rights reserved.
```

### informative

```swift
// Timezone is UTC, convert it to user locale
let expirationDate = getExpirationDate(myVegetable)
```

At least you know what I am trying to do here.
```swift
func testLongRecipe() {
    // This is my best attemp to get real condtion
    // by creating a lot of threads
    for i in 0 ..< 2500 {
        let thread = Thread(soupRecipe)
        thread.start()
    }
}
```

### Clarification

```swift
func testCompareVegetables() {
    let carrot = Carrot()
    let turnip = Turnip()

    XCTAssertFalse(carrot.compare(turnip) == 0 ) // carrot == turnip
    XCTAssertTrue(carrot.compare(turnip) != 0 )  // carrot != Turnip
}
```

### Warnings

```swift
// - Warning : it will run for hours.
func testWithVeryBigRecipe() {
    for i in 0 ..< 250000 {
        sleep(1)
        addToSoup(Carrot())
    }
}

// - Warning : this action is unreversible
func deleteRecipe(_ named:String) {
  // ...
}
```

### Todo

Use // todo comments for things that have to happen, but you can't do immediately

```swift
// TODO: extract recipe name
func launchRecipe("carrotSoup") {
}
```

And that's all.

Forget about redundant, misleading, mandated, journal log, noise, position, closing brace, attribution, commented out code, html, too long, inobvious comments.

# Unclean :

Best comments ever :

```swift
// Return the number of carrots from a recipe
private func numberOfCarrots(_ in:Recipe) {
  return numberOfCarrots  
}

// Default constructor
init()

// The name
private name:String

if {

} // if
else {

} // else

func peel() {
  let carrot = Carrot()
  // No more turnip to peel
  // let turnip = Turnip()
}
```

## 4. Format your code

It's obvious, right ?

## 5. Clean code must be robust

Test your code.

## 6. First make it work, then make it right

# Unclean : (make it work)

```swift
class Rectangle {
    var width = 0
    var height = 0
}

class Circle {
    var radius = 0
}

class main {
    func calculateArea(shapes:[Any]) -> Double {
        var area = 0.0
        for shape in shapes {
            if shape is Rectangle {
                let rectangle = shape as! Rectangle
                area += Double(rectangle.width) * Double(rectangle.height)
            } else if shape is Circle {
                let circle = shape as! Circle
                area += 3.14 * Double(circle.radius) * Double(circle.radius)
            } else {
                area += 0
            }
        }
        return area
    }
}
```

# Clean : (make it right)

```swift
protocol Shape {
    func getArea() -> Double
}

class Rectangle {
    var width = 0.0
    var height = 0.0
}

class Circle {
    var radius = 0.0
}

extension Rectangle:Shape {
    func getArea() -> Double {
        width * height
    }
}

extension Circle:Shape {
    func getArea() -> Double {
        3.14 * radius * radius
    }
}

class main {
    func calculateArea(shapes:[Shape]) -> Double {
        shapes.reduce(0) {$0 + $1.getArea()}
    }
}
```

Thank you for reading.

Leave the code a little cleaner than you found it before.
