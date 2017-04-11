#JavaScript Basics: Class-based inheritance vs. Prototype-based inheritance
######or is it __Delegation__?

There are a lot of confusing aspects of JavaScript for both new and experienced developers. For new developers, the pitfall lies in the nuances surrounding its flexibility. For more experienced developers, it seems to be around the semantics describing those nuances. See [here](https://davidwalsh.name/javascript-objects), [here](http://aaditmshah.github.io/why-prototypal-inheritance-matters/), [here](http://stackoverflow.com/questions/383402/is-javascripts-new-keyword-considered-harmful), and [here](https://ericleads.wordpress.com/2013/02/11/fluent-javascript-three-different-kinds-of-prototypal-oo/). Believe me, there is a lot more out there. Tons. If you spend time reading the comments of those articles, you can almost feel the heat of the debate. If you read the comments closely, the root cause of the debate is often a misunderstanding of definitions. Many experienced developers chime in with preconceived definitions over what X _is_ or _should_ be. And perhaps there is no better place to start than the name: JavaScript. 

Kyle Simpson, the author of the [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/preface.md) series, explains:
> "JavaScript" is as related to "Java" as "Carnival" is to "Car".

"JavaScript" sounds descriptive, but as you probably know, it actually tells nothing of the language itself. And perhaps most frustratingly to experienced developers, many of whom come from class-based languages like Java or C++, [the official ECMAScript Standard](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-overview) states "that [JavaScript] syntax intentionally resembles Java syntax."

To this idea, Simpson remarks:
> "But as a language, [JavaScript] has perpetually been a target for a great deal of criticism, owing partly to its heritage but even more to its design philosophy. Even the name evokes, as Brendan Eich once put it, "dumb kid brother" status next to its more mature older brother "Java". But the name is merely an accident of politics and marketing. The two languages are vastly different in many important ways."

Just how big the gap is between JavaScript and other languages that it is modeled after is the focal point of the debate. This post will discuss the distinctive and relatively unique implementation of _inheritance_ in JavaScript and why its characteristics mislead both new and experienced developers alike. With the introduction of the `class` keyword in ES6, there really is no better place to begin this discussion.

> __Note:__ This post will not attempt to convince you to favor one over the other. There are plenty of resources (at the bottom) written by developers with more experience that speaks to this flavor.

##The familiar: Class-based Inheritance
As I mentioned in passing, JavaScript is relatively young compared to some languages, not in terms of its origin but rather in its maturation into a serious language. JavaScript today is no longer the "dumb kid brother" of Java. In fact, the familial metaphor is inaccurate altogether.

But the consequences of having certain syntactical similarities (usage of `this`, `instanceof`, `new`, and now ES6 `class`) meant that programmers were trying to understand JavaScript in light of Java, not as a completely separate entity.

It is no secret that JavaScript has no true concept of "classes." Instead, the language uses "prototypes" (an object). _What's the difference?_

In __class inheritance__ languages (C++, Java, etc.), there is a distinction between __classes__ and __objects__.
The popular metaphor is that the "class" is the _blueprint of a car_ while the "object" is the instance of the blueprint, or the _car itself_.

Classes can also be "__extended__" (a term with meaning in class theory) to other classes where the extended (child) class _inherits_ directly from the extending (parent) class. Again, using the analogy above, one might have a Vehicle blueprint (the parent class) that specifies a characteristic common to all vehicles (e.g., engines). The Car class can be an extension of the Vehicle class as a result. This creates a __class hierarchy__ between the classes and the instances "born" from those classes. 

This "class hierarchy" creates what Eric Elliott, a JS guru, calls the [Tight Coupling Problem](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3). He writes, "The coupling between a child class and its parent is the tightest form of coupling in [Object-Oriented] design... [because] making small changes to a class creates rippling side-effects that break things that should be completely unrelated."

To demonstrate this point, check out the code snippet below (using ES6 classes):
```javascript
class Vehicle {
  constructor() {
    this.fuel = 'gas';
  }

  whatFuel() {
    console.log(this.fuel);
  }
}

class Car extends Vehicle {
  constructor(model, year) {
    super();
    this.model = model;
    this.year = year;
  }

  whatModel() {
    console.log(this.model);
  }

  whatYear() {
    console.log(this.year);
  }
}

let beatle = new Car('Volkswagon', 2005);
let camry = new Car('Toyota', 2012);



```