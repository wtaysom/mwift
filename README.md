# A Little Swift Review

Two words?  Would use.

One more?  Willingly.

Welcome to the 21st century.  Four years ago LLVM architect [Chris Lattner](http://nondot.org/sabre/) began design on [Swift](https://developer.apple.com/swift/).  Conceived with compilation in mind, Swift gears abstractions toward optimization (attention to aliasing, ARC for predictable instance life) and avoids features with performance penalties (pointers, garbage collection, exceptions).

> Swift combines the best in modern language thinking with wisdom from the wider Apple engineering culture. The compiler is optimized for performance, and the language is optimized for development, without compromising on either.

So Apple [claims](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/index.html).  And so it seems after reading [The Swift Programming Language](https://itunes.apple.com/us/book/the-swift-programming-language/id881256329?mt=11).  The bar for a better Apple platform language was low.  [SwiftDevs](https://twitter.com/SwiftDevs) announce:

> Good riddance for Objective-C, whose only good feature was "not as bad as C++".

David Gewirtzis is [hopeful](http://www.zdnet.com/why-apples-swift-might-be-the-new-basic-and-thats-no-small-thing-7000030186/):

> Swift has the potential to both revolutionize professional app development while at the same time opening the door to more recreational and educational programming.
> 
> This dual nature, of both high-performance resultant code and highly interactive development, is something we haven't seen before. If Apple can pull it off effectively, it will be something special.
> 
> Back in 2012, in my True confessions of a former iPhone developer, I described Objective-C as "It's a lot like someone welded two separate programming languages together and forgot to grind down the rough edges."

Swift likes rounded corners: conservative syntax, Objective-C object orientation, functional functional programming features.  For [some](https://news.ycombinator.com/item?id=7838664):

> This is what annoyed me most about the whole Swift presentation. Swift is not in any way shape or form innovative, in terms of language features or design. It is however, another language that aims to be as productive as possible.

Sounds like the perfect Apple [product](http://zurb.com/article/744/steve-jobs-innovation-is-saying-no-to-1-0):

> Innovation is saying "no" to 1,000 things.

### Big Idea Bullets

What makes Swift swift?

* Conceived for Compilation
* Safe Syntax
* Platform Integration

### Wat

A Break with legacy permits improved clarity and simplify.  What remains baffling?

* "Immutable" Arrays
* Implicitly Unwrapped Optional Properties
* Closure Capture Lists

### What else?

Let's take a quick look at the [syntax](#syntax), [semantics](#semantics), especially [Automatic Reference Counting](automatic-reference-counting), [tooling](#tooling), [object orientation](#object-orientation), and [functional programming](#functional-programming).

## Syntax

> Actually, I'm trying to make Ruby natural, not simple. -- [Yukihiro "Matz" Matsumoto](http://marc.info/?l=ruby-talk&m=96398337725662&w=2)

The surface syntax for a programming language serves as its user interface.  Syntax makes some constructs easy, others difficult, avoids one class of bugs, enables another.  Apple wants Swift's syntax to be clean, conventional, and safe.

> Swift is the Helvetica of programming languages. -- [John Gruber](http://daringfireball.net/thetalkshow/2014/06/06/ep-083)

In standard Apple style, clean means opinionated.  For instance:

> Unless you need the specific behavior of `i++`, it is recommended that you use `++i` and `--i` in all cases, because they have the typical expected behavior of modifying `i` and returning the result.

Swift aims for a certain kind of neutrality: standard statement/expression dichotomy, limits on error prone combinations, C-style curly braces, Tiger type `<T>` generics, strong typing with no implicit conversions.  You'll learn more about a person from their reaction to Swift's syntax than you will learn about Swift itself:

> FWIW: it seems quite verbose to me: in the couple of chapters I've read already, there doesn't seem to be much syntactic sugar. But I suppose verbose means Apple can sell us all bigger monitors :-)

Compare [this fellow](http://arstechnica.com/apple/2014/06/a-fast-look-at-swift-apples-new-programming-language/):

> Right now, a lot of Swift code seems like it could be difficult to follow, but we expect that most of it will become second nature with use. Most programmers will likely be happy to take the relatively compressed syntax of Swift, but the language's flexibility may mean that people will end up unintentionally producing perfectly viable code that other Swift users will struggle to read.

I guess when you're used to Objective-C:

~~~ objective-c
UITableView *myTableView = [[UITableView alloc]
	initWithFrame:CGRectZero style:UITableViewStyleGrouped];
~~~

Swift is relatively compressed:

~~~ swift
let myTableView = UITableView(frame: CGRectZero, style: .Grouped)
~~~

A typical method call will be exactly the same length:

~~~ objective-c
[receiver setFlag: flag forKey: key withAttributes: attributes]
~~~

just with different punctuation:

~~~ swift
receiver.setFlag(flag, forKey: key, withAttributes: attributes)
~~~

easier to read than Apple's [JavaScript bridge](https://developer.apple.com/library/mac/documentation/AppleApplications/Conceptual/SafariJSProgTopics/Tasks/ObjCFromJavaScript.html):

~~~ javascript
receiver.setFlag_forKey_withAttributes(flag, key, attributes)
~~~

Anyone can appreciate keyword external parameters after seeing enough obscure many argument method calls. Here's the one I saw yesterday:

~~~ ruby
problem = Problem.new(
	ai,
	51,
	assignment,
	domain_bids,
	1)
~~~

Of course in Ruby you can write:

~~~
problem = Problem.new(
	algo_input: ai,
	highest: highest,
	assignment: assignment,
	domain_bids: domain_bids,
	iteration: 1)
~~~

However, Ruby [hash destructing](http://tony.pitluga.com/2011/08/08/destructuring-with-ruby.html) is a little inconvenient.

Also like Ruby, Swift tends to trade consistency for convenience:

> The default behavior of local names and external names is different for functions and methods. ... The default behavior described above mean that method definitions in Swift are written with the same grammatical style as Objective-C, and are called in a natural, expressive way.

Recall that in Ruby, blocks, procs, lambdas, and methods all handle parameters differently.  You don't recall?  That's because the defaults are basically what you want.  Swift tries to do the same.  With time we'll see whether Swift's defaults are right.

Swift favors explicit over implicit:

> Methods on a subclass that override the superclass's implementation are marked with `override` -- overriding a method by accident, without `override`, is detected by the compiler as an error. The compiler also detects methods with `override` that don't actually override any method in the superclass.

My experience recommends explicit overrides.  Swift's most avant garde feature comes with a billion dollar experience recommendation.

### Optional Chaining

> I call null references my billion-dollar mistake. -- [Tony Hoare](http://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)

C# fixes the mistake by having [nullable types](http://msdn.microsoft.com/en-us/library/1t3y8s4s.aspx):

~~~ c#
int? num = null;
if (num.HasValue)
    {
        System.Console.WriteLine("num = " + num.Value);
    }
    else
    {
        System.Console.WriteLine("num = Null");
    }
~~~

Swift's does the same more cleanly:

~~~ swift
num: Int? = nil

if let value = num {
	println("num = \(value)")
} else {
	println("num = nil")
}
~~~

Swift ventures into new territory by allowing chains of possible nulls:

~~~ swift
if let johnsStreet = john.residence?.address?.street {
	println("John's street name is \(johnsStreet).")
} else {
	println("Unable to retrieve the address.")
}
~~~

The territory is far from virgin.  Ruby's underused [andand gem](http://rubydoc.info/gems/andand/1.3.3/frames) accomplishes much the same:

~~~ ruby
if johnsStreet = john.andand.residence.andand.address.andand.street
	puts "John's street name is #{johnsStreet}."
else
	puts "Unable to retrieve the address."
end
~~~

Ultimately inspired by [applicative functors](http://hackage.haskell.org/package/base-4.7.0.0/docs/Control-Applicative.html) in Haskell:

~~~ haskell
maybe
	(putStrLn "Unable to retrieve the address.")
	(\johnsStreet -> putStrLn "John's street name is " ++ johnsStreet ++ ".")
	(street <$> address <$> residence john)
~~~

Haskell may do it all backwards, but to the same effect.

### Warts

Bryan Feeney points out a [few](http://studentf.wordpress.com/2014/06/03/swift-not-quite-there-but-too-far-gone-too/):

> Also why does one use the `static` modifier with structs and enums, but a syntactically separate but semantically identical `class` modifier with classes? Why not use the same modifier everywhere?

Yeah, I don't know.

> And why is `override` a keyword when `@final` is an annotation -- shouldn't they all be annotations? Both can trigger compiler errors.

Well `@final` is a more rare annotation just like `@IBOutlet`.

I have one complaint of my own:

> The type of a Swift array is written in full as `Array<SomeType>`, where `SomeType` is the type that the array is allowed to store. You can also write the type of an array in shorthand form as `SomeType[]`. Although the two forms are functionally identical, the shorthand form is preferred, and is used throughout this guide when referring to the type of an array. ...
>
> Swift's dictionary type is written as `Dictionary<KeyType, ValueType>`, where `KeyType` is the type of value that can be used as a dictionary key, and `ValueType` is the type of value that the dictionary stores for those keys.

Having picked a poor shorthand for arrays, there is no good shorthand for dictionaries.  Considering the literals:

~~~ swift
let array = [1, 2, 3]
let dictionary = [1: 1, 2: 4, 3: 9]
~~~

and their empty versions:

~~~ swift
let emptyArray = []
let empty Dictionary = [:]
~~~

better type shorthands seem obvious:

~~~ swift
// Not Swift but should be.
let arrayTypeShorthand: [SomeType]
let dictionaryTypeShorthand: [KeyType: ValueType]
~~~

Done and done.  Let's talk about generic types.

### Generics

Generics are a mistake. This is not a problem based on technical disagreements. It's a fundamental language design problem. ... Writing generified classes is rocket science -- [Ken Arnold](https://weblogs.java.net/blog/2005/06/27/generics-considered-harmful)

I never understood generics, but I hope that in the last decade of Java, C#, Haskell with its type classes, and ML with its modules, some thorny issues have been sorted out.  Syntactically, Swift imitates Java.  Here's the one interesting example from the book:

~~~ swift
func anyCommonElements<T: Sequence, U: Sequence where
	T.GeneratorType.Element: Equatable,
	T.GeneratorType.Element == U.GeneratorType.Element>
(lhs: T, rhs: U) -> Bool {
	for lhsItem in lhs {
		for rhsItem in rhs {
			if lhsItem == rhsItem {
				return true
			}
		}
	}
	return false
}

anyCommonElements([1, 2, 3], [3])
~~~ swift

The same in Java (assuming that `Object.equals` comes instead from an `Equatable` interface):

~~~ java
public static <E extends Equatable<? super E>> boolean
		anyCommonElements(Iterable<E> lhs, Iterable<E> rhs) {
	for (E lhsItem : lhs) {
		for (E rhsItem : rhs) {
			if (lhsItem.equals(rhsItem)) {
				return true;
			}
		}
	}
	return false;
}
~~~

Swift seems to sidestep some Java oddness via its named associated `GeneratorType.Element` types.  `Sequence` feels like an ML module in this respect though Haskell's type classes seems especially well fit for this definition:

~~~ haskell
anyCommonElements :: (Foldable f, Foldable g, Eq a) => f a -> g a -> Bool
anyCommonElements xs ys = any (\x -> any (x ==) ys) xs

anyCommonElements [1, 2, 3] [3]
~~~

The Haskell one-liner relies on

~~~ haskell
any :: Foldable t => (a -> Bool) -> t a -> Bool
~~~

which we can define in Swift too:

~~~ swift
func any<T: Sequence where T.GeneratorType.Element: Equatable>
(xs: T, p: T.GeneratorType.Element -> Bool) -> Bool {
	for x in xs {
		if p(x) {
			return true
		}
	}
	return false
}
~~~

Then Swift gets its own one-liner:

~~~ swift
func anyCommonElements<T, U where T: Sequence, U: Sequence,
	T.GeneratorType.Element: Equatable,
	T.GeneratorType.Element == U.GeneratorType.Element>
(xs: T, ys: U) -> Bool {
	return any(xs) { x in any(ys) { x == $0 }}
}
~~~

Here I show off all of Swift's syntactic sugar for defining closures.  Notice that the function arguments are outside of parenthesis.  Notice that `x` does not require a type declaration, and finally notice the anaphoric variable `$0`.

With sugar like that, we may not see [CoffeeScriptization](http://coffeescript.org/) of Swift for some time: just enough sugar to tame the temptation.

## Semantics

With Swift appearing safely conventional, I began to wonder whether there were any unique, powerful ideas at work.  Any substance beneath the appearance?  Any pervasive theme, any overarching goal that will make Swift stand out for years to come?  I think there is a theme, exemplified starting with the second code snippet in the book:

~~~ swift
var myVariable = 42
myVariable = 50
let myConstant = 42
~~~

Swift cares about mutability.  Every name is annotated as being a constant or a variable.  Enums and structs are passed by value whereas function closures and objects of classes are passed by reference.  C is similar but with pointers instead of references.  However, the point here is that Swift makes special effort to promote values and constants by allowing a little bit of flexibility.  Consider:

> If you need to modify the properties of your structure or enumeration within a particular method, you can opt in to mutating behavior for that method. The method can then mutate (that is, change) its properties from within the method, and any changes that it makes are written back to the original structure when the method ends. The method can also assign a completely new instance to its implicit self property, and this new instance will replace the existing one when the method ends.

They give the example:

~~~ swift
struct Point {
	var x = 0.0, y = 0.0
	mutating func moveByX(deltaX: Double, y deltaY: Double) {
		self = Point(x: x + deltaX, y: y + deltaY)
	}
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveByX(2.0, y: 3.0)
println("The point is now at (\(somePoint.x), \(somePoint.y))")
// prints "The point is now at (3.0, 4.0)"
~~~

Self assignment reminds me of Smalltalk's [become:](http://gbracha.blogspot.hk/2009/07/miracle-of-become.html).

Swift extends value semantics to its built-in array and dictionary collections.  Again with surprise flexibility:

> Immutability has a slightly different meaning for arrays, however. You are still not allowed to perform any action that has the potential to change the size of an immutable array, but you are allowed to set a new value for an existing index in the array. This enables Swift's Array type to provide optimal performance for array operations when the size of an array is fixed.

Trading for performance, people find this [confusing](http://stackoverflow.com/questions/24081009/is-there-a-reason-that-swift-array-assignment-is-inconsistent-neither-a-referen).  Those accustomed to languages which pass everything by reference (Ruby, JavaScript) are going to have a hard time.

> Swift, a language that is naturally designed to let you shoot your foot in the most elegant way possible, courtesy of Apple.

Speaking of which, let's talk about:

## Automatic Reference Counting

Swift does not have garbage collection.  Instead:

> Swift uses Automatic Reference Counting (ARC) to track and manage your app's memory usage. In most cases, this means that memory management "just works" in Swift, and you do not need to think about memory management yourself. ARC automatically frees up the memory used by class instances when those instances are no longer needed.

Apple tried Garbage Collection for Objective-C.  It never worked out.  For memory constrained, near realtime apps, ARC proved to use resources more predictably.  Just like ARC, Good garbage collecting systems try to determine object life so that they can free memory as soon as it is dereferenced.  Cycles create garbage though.

> Swift provides two ways to resolve strong reference cycles when you work with properties of class type: weak references and unowned references.

The simple cases are simple, but things can get complicated:

> The examples for weak and unowned references above cover two of the more common scenarios in which it is necessary to break a strong reference cycle.
>
> The Person and Apartment example shows a situation where two properties, both of which are allowed to be nil, have the potential to cause a strong reference cycle. This scenario is best resolved with a weak reference.
>
> The Customer and CreditCard example shows a situation where one property that is allowed to be nil and another property that cannot be nil have the potential to cause a strong reference cycle. This scenario is best resolved with an unowned reference.
>
> However, there is a third scenario, in which both properties should always have a value, and neither property should ever be nil once initialization is complete.  In this scenario, it is useful to combine an unowned property on one class with an implicitly unwrapped optional property on the other class.
>
> This enables both properties to be accessed directly (without optional unwrapping) once initialization is complete, while still avoiding a reference cycle.

They have fully thought through the challenges.  They understand the complexities:

> A strong reference cycle can also occur if you assign a closure to a property of a class instance, and the body of that closure captures the instance. This capture might occur because the closure's body accesses a property of the instance, such as self.someProperty, or because the closure calls a method on the instance, such as self.someMethod(). In either case, these accesses cause the closure to "capture" self, creating a strong reference cycle.
>
> Swift provides an elegant solution to this problem, known as a closure capture list.

Closure capture lists may not end up being known as an elegant solution, but they are a solution, and capture lists are better than the fixes we employed over the many years of the Internet Explorer's [JScript closure leak](http://msdn.microsoft.com/en-us/library/bb250448(v=vs.85).aspx).

## Tooling

If ARC isn't going to clean up garbage, who is?  You will, but Apple helps.  Their [Leaks profiling Instrument](https://developer.apple.com/library/mac/documentation/developertools/conceptual/instrumentsuserguide/MemoryManagementforYouriOSApp/MemoryManagementforYouriOSApp.html) marks your garbage so that you can go back and clean it up.

I like Apple's general approach here: sanity check at compile time, clean up with runtime profiling, release when it's perfect.  Instruments keeps getting better.  The [User Interface Instrument](https://developer.apple.com/library/ios/documentation/AnalysisTools/Reference/Instruments_User_Reference/UserInterfaceInstrument/UserInterfaceInstrument.html) brought automated testing to the tap-and-swipe world, and now Swift adds [Playgrounds](https://developer.apple.com/library/prerelease/ios/recipes/xcode_help-source_editor/ExploringandEvaluatingSwiftCodeinaPlayground/ExploringandEvaluatingSwiftCodeinaPlayground.html):

> Without requiring you to compile and run a complete project, a playground provides quick feedback for the results of your coding experiments.

I haven't gotten a chance to try them yet.  Doing so would require the Xcode 6 beta, which requires the OS X Yosemite beta.  Ain't nobody got time for that.  So instead here's what Lattner says:

> The Xcode Playgrounds feature and REPL were a personal passion of mine, to make programming more interactive and approachable. The Xcode and LLDB teams have done a phenomenal job turning crazy ideas into something truly great. Playgrounds were heavily influenced by Bret Victor's ideas, by Light Table and by many other interactive systems. I hope that by making programming more approachable and fun, we'll appeal to the next generation of programmers and to help redefine how Computer Science is taught.

Already, Khan academy has a Victor inspired tool for its [programming](https://www.khanacademy.org/computing/cs) though [Scratch](http://scratch.mit.edu/) remains my strong favorite for its cleverly structured editing blocks, subtly remarkable concurrency model, and easy sharing.

Moving these interactive tools from the playground to the workshop is no small task.  With Light Table, Chris Granger made it his mission to take this seriously:

> The only thing that makes me sad is that because of their [Apple's] focus on secrecy, they're doomed to relearn all that we learned along the way. Having played with the Swift playground stuff, just an hour long conversation could've made a big difference.
Such is the way of Apple though.
> 
> Let's say you take direct inspiration for something you're working on and you know someone's been there and thought a lot about it. Wouldn't it make sense to ask them about it? It's certainly true that some mistakes are better learned by making them yourself, but a whole lot aren't. Hell, half the time it's just stuff you're too close to see anymore.
> 
> The Swift playground is strikingly similar to many of the things we've done in Light Table. It's wonderful that Apple is taking that and running with it and I want these things to end up out there and make things better for devs. But I could've helped them skip some of the crap along the way and I've worked with other large organizations to help them do exactly that.
> 
> I have a unique perspective in this particular case, one that no one else will have, as the creator of one of the things they were "heavily influenced" by. I'd rather they took advantage of that so that they can continue to push things even further and not fall into some traps that we did at Microsoft and with LT itself.

If you really want to see where the Swift Playground could be headed, check out [Light Table](http://www.lighttable.com/).  I donated to the Kickstarter early on, did some experimenting, and would really love to come back to it.

## Object Orientation

Classes are here stay.  On Apple platforms, easy Objective-C interoperation is absolutely mandatory.  Swift delivers.  Swift classes use the Objective-C runtime, so you get the same overhead, same capabilities, and the same limitations.  This means single inheritance with protocols (Java interfaces) as well as extensions (open classes, adding methods to existing classes), but nothing more: no mixins, no traits.  Traits would suit Swift and could be implemented within the constraints of the Objective-C runtime, yet other reuse patterns may emerge.  We will see.

For now, Swift cuts cruft adding convenience and safety.  Often you want an initializer to simply set an object's properties (fields).  For this default, you don't write a single line.  When an object does have a complicated initialization process, the compiler ensures that properties are initialized before they are referenced.

## Functional Programming

> The academic functional language conferences this year are going to feel like a series of victory laps. Good times, people. -- Bryan O'Sullivan

Swift has first class functions.  That's the low bar.  Parametric polymorphism?  Generics go beyond.  Currying?  Yes.

Compare Haskell:

~~~ haskell
compose :: (b -> c) -> (a -> b) -> a -> c
compose f g x = f (g x)
~~~

with Swift:

~~~ swift
func compose<A, B, C>(f: B -> C)(g: A -> B)(x: A) -> C {
	return f(g(x))
}
~~~

A good start.  What about algebraic data types?  Enums fit the bill.  Consider the least interesting recursive algebraic data type, the lowly list.

In Haskell:

~~~ haskell
data List a =
	Nil |
	Cons a (List a)
~~~

In Swift:

~~~ swift
enum List<T> {
	case Nil
	case Cons(T, List<T>)
}
~~~

Looks good.  But it doesn't compile.  That's a (bug)[https://twitter.com/jopamer/status/473590251168874496]:

> Carlos Scheidegger: Hey, Swift looks really nice - wondering if enums can have recursive defs.
> Joe Palmer: Thanks, Carlos! Not quite yet, but only because of a bug. I'm currently tracking it, and I'll look into addressing it soon.

Pattern matching remains as the final piece.  Define a function with pattern matching in Haskell:

~~~ haskell
append :: List a -> List a -> List a
append Nil ys = ys
append (Cons x xs) ys = Cons x (append xs ys)
~~~

Do the same in Swift:

~~~ swift
func append<T>(xs: List T, ys: List T) -> List T {
	switch xs {
	case .Nil:
		return ys
	case let .Cons(x, xs):
		return Cons(x, append(xs, ys))
	}
}
~~~

Swift counts a functional language though the line count is a little large.

Maybe Swift could use some more syntactic sugar.  Hmm, swift with macros.  Macro Swift.  Mwift.  Let the CoffeeScripting begin!