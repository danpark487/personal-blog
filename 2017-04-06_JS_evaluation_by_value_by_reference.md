#Javascript Basics: Evaluation Strategy: by value vs. by reference
######_variable assignment and function calls -- rebinding and mutation_

A language's __evaluation strategy__ is the set of rules governing __when__ to evaluate the arguments of the function and __what__ value to pass into a function call. In this post, we will be focusing on the latter -- __what__ value is actually passed in?

##Motivation
Consider the following code:
```javascript
// Example 1
var a = 10;
var b = a;
a = 5;
console.log(a); // 5
console.log(b); // 10
```
This seems pretty obvious. But it may be a worthwhile question to ask: Why isn't `b` "following" `a`'s reassignment to 5?

Or consider the example below:
```javascript
// Example 2
function addTwo(x) {
  x += 2;
};
var a = 10;
addTwo(a);
console.log(a); // still 10!
```
Again, perhaps obvious -- but why?

The "why"s keep me up at night... sorta.

##Data Types
To understand how JavaScript handles its evaluation strategy, one has to understand the two different categories of data types - (1) __primitives__ and (2) __reference types__.

Primitives include: (1) booleans, (2) null, (3) undefined, (4) numbers, (5) strings, (6) symbols (ES6).
Out of the seven data types in JS, six are considered primitives. That just means that __objects__ are a reference type (i.e., this includes functions and arrays because in JS, those are also objects).

__An important note:__ primitives in JavaScript are immutable; while objects are mutable. This will play a part in how evaluation strategy works.

The data type is important to consider because there is a slightly different evaluation strategy applied to primitives from reference types.

##By Value vs. By Reference
So what's the difference between __by value__ and __by reference__?

__Assignment/call by value__ typically means that when a variable is assigned to a value, it allocates a set amount of memory in a stack and _copies_ the value at that location. The key concept here is the "copying" aspect. 

The implications of this rule are at least threefold:
1. In "Example 1", when `b` is assigned to `a`, `b` does not care about `a` but only `a`'s value. Any changes to `a`, whether by re-assignment or mutation is irrelevant to `b` because `b` creates a copy of `a`'s value in a separate memory block.
2. Performance concerns would arise if the value being copied over is complex (objects). While primitives _generally_ take up a known amount of space in the memory, reference types (objects) obviously do not have a preset amount of memory that it will occupy (i.e., objects can contain a lot of information).
3. If this is an ironclad rule, variables are _never_ 'watching' another variable. One variable changing course (by re-assignment or by modification) will never affect another variable.

As we mentioned in Rule #2, it is clear that there may be concerns where entire objects are copied into another memory space at function call time. _See below_.
```javascript
// Example 3
var a = { foo: 10, bar: 20, ... }; // big object!
function changeObject(obj) {
  obj = 10 // re-assign object to a number
};
changeObject(a);
console.log(a); // still { foo: 10, ... }!
```
If you can imagine `a` being an extremely large object, if `changeObj(a)` is called, it would be a performance sink to make a copy of `a` and assign it to `obj` inside the function call. It takes up double the memory and the copy process can take up system resources!

So...

__Assignment/call by reference__ means that when a function calls a variable, the assignment process takes in the __reference__, or in other words, the memory address where the value lives. The copy of the actual value is never made in this process.

A fictitious and contrived example below of Example 3:
```javascript
// Example 4
var a = { foo: 10, bar: 20, ... };
function changeObject(obj) {
  obj = { baz: 30 }; 
};
changeObject(a);
console.log(a); // now { baz: 30 }!
```
* An illustration of object mutation: Imagine you are driving a car to a particular location. The car is the object in this case, and your driving is the function executed on the car to direct its steps. In a call by reference paradigm, any decision you make while driving will alter the “state”/position of the car relative to where it was before. Because the driving is acting on the same object (the car) and not a copy of that object (as in the by value paradigm), changes are always reflected on the object.

* Same illustration (re-assignment): Same driver/car scenario. If you switch cars along the way, the old car is gone. Your new car is what you have at that memory moving forward.

* Yet another example, more abstract: Think of a pointer being directed at the actual memory address (the reference!) of the arguments passed into the function. Changes made inside the function would be modifying the data at that address. 

While this is all very interesting, as we saw in Examples 1 and 2, and probably in personal experience, we know that data is not passed by reference in JavaScript.

##JavaScript: which is it?
In short, everything in JavaScript is passed by some sort of value. But the long answer is far trickier.
You might have heard something like, "In JavaScript, primitive data types are passed by value while reference types, objects, are passed by reference." That's technically incorrect!

This confusion arises from the example below:
```javascript
// Example 5, function call
var a = { foo: 10, bar: 20, ... };
var b = a;
function changeObject(obj) {
  obj.foo = 999;
}
changeObject(a);
console.log(a); // { foo: 999, ... }!
console.log(b); // { foo: 999, ... }!!

// Example 6, no function call
var a = { foo: 10, bar: 20 };
var b = a;
a.foo = 100
console.log(a); // { foo: 100, ... }!
console.log(b); // { foo: 100, ... }!!
```
This should be confusing to you given the explanation I gave above. If objects are _passed by value_, how does a modification to `obj` affect `a` _and_ `b`?
This _looks_ like data is being passed _by reference_. But I just told you, that's actually incorrect as Example 4 demonstrates, reassignment did not change the object.

__So here's the crux of the issue:__ the behavior of reference types (objects) in JavaScript seems to depend on whether the object _as a whole_ is reassigned (as in Example 3) or whether the object's inner properties are _mutated_.

This should still strike you as bit of a curious reality -- maybe even inconsistent design.

However, remember! The six primitive data types are still passed __by value__. Variable assignments and calls are _always_ a copy of the value placed in an entirely separate memory space. This means one variable can never alter the contents of another variable. _See below_.
```javascript
// Example 7
var a = 10;
var b = a;
a++;
console.log(a); // 11
console.log(b); // 10
```
There is nothing you can do to `a` that will change `b` unless by directly changing/re-assigning `b` itself!

For objects, however, the value that is being passed in is a copy of the reference to that object (memory address).

__A copy is made, it just isn't the whole object itself as it is with primitives, just the reference.__

It is under _this_ definition of __value__ where some astute JavaScript scholars (heh) have said, "All data in JavaScript is passed by value!" The copy being made is just simply the reference to the object.

##The Twirl
Haha, if you had the patience to read this far, you probably have noticed that I just twirled you around a few times and said, “See, JavaScript objects are NOT passed by reference!” through some word gymnastics. 

In reality, much of the discussion surrounding this topic comes down to semantics. I have yet to find an example of a programming language that is strictly __pass by value__ under the classical definition (complete copy of underlying value) or even strictly __pass by reference__ (all variable references modify the underlying value). It seems a bit inefficient to imagine the former in a language while almost silly to do the same for the latter (error prone!).

We have to remember that these evaluation "strategies" are actually abstract descriptions, much like an Abstract Data Type (ADTs), that are helpful in understanding contrasting concepts. The actual implementation is a bit more nuanced.
---
##Immutability
In JavaScript, there are two concepts that is worth thinking about to reconcile the problem concerning __objects__: the behavior of reference types (objects) seem to depend on whether the object is _reassigned_ or its inner properties _mutated_.

Two concepts:
1. __Rebinding__
2. __Mutation__

* __Rebinding__ is the notion that variables can be assigned and re-assigned to the programmer's liking. If _binding_ is the process in which an identifier (i.e., variable) is associated with an object, __rebinding__ is when you unbind the identifier and bind it to another object.
* __Mutation__ is the concept of altering, or modifying the contents of the object. 

These dual concepts explain why Examples 4 and 5 produce seemingly inconsistent results. 
* The re-assignment in Example 4 does nothing to the object itself, it simply re-directs its "pointer" to another destination. Think about putting a new address inside your GPS. The new destination coordinates do nothing to alter the previous destination.
* However, in Example 5, the object is directly mutated. As we said above, because the `obj` variable in `changeObject(obj)` is "pointing" (via a copy of the address) to the same object as `a` and `b`, a direct mutation to `obj` would affect `a` and `b` who are both also pointing to that object. It is because of this idea that some call this interaction, [call by sharing](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_sharing) (the lesser known evaluation strategy), because all the "pointers" are directed at the same object.

##Summary
A lot of this discussion comes down to semantics as always. It is the tricky line in trying to fit an implementation into a neat abstract category. As such, it would be unfair to say that JavaScript is inconsistent. It simply functions this way. Objects are special, __mutable__ values that are passed in a slightly different way than primitives.

Passing objects behaves as if it had been passed __by reference__ but only when the object is being mutated (not re-assigned). So it would be inaccurate to simply state: “Objects are passed by reference.” That’s implies more than what actually happens. On the other hand, to say objects are passed __by value__ is also a misnomer because that typically means a copy of that object is created at another memory address — which isn’t what is happening either!

Therefore, it would only be technically correct to say, “All data types are passed by value in JavaScript… where the “value” for an object is a copy of the reference to that object, not the object itself.”

Or you could simply say, “In JavaScript, primitives are passed by value, whereas objects are passed by sharing.” Because in sharing, you can always rebind to some other object without altering all the other variables pointing to that object.

In conclusion, the inaccuracies would only exist where the abstract concepts of “pass by value” or “pass by reference” have not been qualified to explain the actual implementation.

__Read more:__
* [Dmitry Soshnikov on Evaluation Strategy](http://dmitrysoshnikov.com/ecmascript/chapter-8-evaluation-strategy/)
* [Dmitry Soshnikov on Rebinding/Mutation](http://dmitrysoshnikov.com/ecmascript/es5-chapter-3-1-lexical-environments-common-theory/#name-binding)
* [Wikipedia on call by sharing](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_sharing)
