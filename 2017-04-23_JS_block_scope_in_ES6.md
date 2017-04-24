##JavaScript Basics: Block Scope in ES6
######`let` and `const`, and the TDZ

__Note:__ I recommend reading my previous blog post on [JavaScript Execution Context and Lexical Environments](https://medium.com/dailyjs/javascript-basics-the-execution-context-and-the-lexical-environment-3505d4fe1be2) first.

We actually take for granted that pre-ES6, variables were almost exclusively "function scoped" (the other side being scoped globally). If a variable was set anywhere in the function (e.g., inside a `for` loop or an `if` statement), that variable would be accessible within that function. In ES6, there is now __block scope__.

##Block Scope
Block scope is not an unfamiliar concept to those who come from other languages (C++, Java, Perl, etc.). However, in JavaScript, it was made available through the `let` and `const` keywords in ES6.

In short, __block scoped__ variables are accessible via identifier resolution anywhere within the _block_ but not outside it.

To understand that definition, it is helpful to understand what a "block" is. Quite simply, a __block__ is anything within curly braces: {}.

See below:
```javascript
function foo() {  // start Block1
  if (...) {      // start Block2
    //...
    for(...) {    // start Block3
      //...
    }             // end Block3
  }               // end Block2
}                 // end Block1
```

As you can see, blocks can be nested (and quite often so). The important aspect of block scoping and how it affects identifier resolution is that as the code is being run line-by-line, when it encounters a block, it enters into a _new_ `LexicalEnvironment`. When it leaves the block, it goes back to the _surrounding_ `LexicalEnvironment`. [See here] (https://www.ecma-international.org/ecma-262/7.0/#sec-block-runtime-semantics-evaluation).

Within this newly created `LexicalEnvironment`, a _declarative Environment Record_ is created (remember this?). The abstract operation for this procedure is called `BlockDeclarationInstantiation` ([ECMAScript 13.2.14](https://www.ecma-international.org/ecma-262/7.0/#sec-blockdeclarationinstantiation)). It is within _this_ environment record that our soon-to-be-introduced `let` and `const` keywords are bound.

__Food for thought__: What happens when both `let` and `var` is used within the same block? How does `var` persist outside the block scope if upon leaving the block, it exits the `LexicalEnvironment`?

Check out the code below:
```javascript
function foo() {
  if (...) {
    let a = 10;
    var b = 20;
  }
  console.log(b);   // 20
  console.log(a);   // ReferenceError: a is not defined!
}
```
The reason why `b` is "remembered" is because `b` was never bound to the `LexicalEnvironment`. Instead, it was bound to the Execution Context's `VariableEnvironment`. Pre-ES6, there was no distinction between the two Lexical Environments; however, now, `var` statements are bound to the `VariableEnvironment` for `var`-identifier resolution. This is obviously because `var` is still scoped at the function level.

Again, remember, for __lexically scoped declarations__ (`let` and `const`), identifier resolution allows scope chain lookup as usual _as long as_ the block has not yet been exited (i.e., leaving the current `LexicalEnvironment`). This is why inner blocks can access `let` and `const` declarations outside its block. However, you cannot access declarations _inside a block_ from the outside looking in. 

This is block scope in a nutshell. But `let` and `const` is not done with you just yet. They have particular nuances from `var` outside the block scoping.

##`Let` and `Const` keywords
Generally speaking, __`let`__ can replace `var` in most cases. In fact, `let` gives greater flexibility in variable declarations because it is scoped _closer_ to the actual operation. For example:
```javascript
// using 'var'
for (var i = 0; i < 10; i++) {
  ...
}

// using 'let'
for (let j = 0; j < 10; j++) {
  ...
}

console.log(i); // 10
console.log(j); // ReferenceError: j is not defined!
```
As seen above, since `j` is scoped to the block, you can use `j` elsewhere in your function without consequence to the `for` loop. The reason why you should favor `let` in these use-cases is for the same reason why programmers avoid declaring global variables. This is simply an extension of the [Principle of Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) (i.e., only have access to info as necessary).

__`const`__ is actually a very special keyword introduced with ES6. It creates an __immutable binding__ to a particular value or object. The term "immutable _binding_" is specific because it does not prevent mutations on the object bound to the `const` keyword, but _does_ prevent re-assignment.

See below:
```javascript
const a = 10;
a = 2; // SyntaxError: Assignment to constant variable!

// However, to an object
const b = [];
const c = { bar: 10 };

b.push('2');
c.foo = 20;
c.bar = 30;

console.log(b); // [ '2' ]
console.log(c); // { bar: 30, foo: 20 }
```
__Note:__ if you need a refresher on why the first example (a = 10 to a = 2) is considered a re-assignment (forbidden) and not a mutation (permissible), check out my previous blog post: [Evaluation Strategy: by value vs. by reference](https://medium.com/@danparkk/javascript-basics-evaluation-strategy-by-value-vs-by-reference-ee681dbaa527).

It is important to distinguish between _true immutability_ (cannot mutate the object) from the immutable _binding_ that `const` gives us to avoid misconceptions. As such, `const` should be used when you know that the assignment/binding is permanent to that block scope (`const` is still a block scoped declaration). If this explanation hasn't made it clear, but `const` _must_ be assigned to something when it is declared (i.e., `const a;` will not work!)

__Now here's the weird part.__

Remember hoisting and its effect on your code?
Look!
```javascript
function foo() {
  console.log(bar);
  var bar = 10;
};

foo(); // undefined
```
You will get `undefined` because `bar` is hoisted during the Creation Phase of the Execution Context.
However, the same code with `let`:
```javascript
function foo() {
  console.log(bar);
  let bar = 10;
};

foo(); // ReferenceError: bar is not defined!
```
Yeah, yeah, what's going on now. JavaScript doing its thing again. Let me just get this out there: this is not because `let` declarations are not hoisted! In fact, they are hoisted to the top of the block scope. But then why the `ReferenceError`?

Enter the __Temporal Dead Zone__. No really, this is a thing.

##Temporal Dead Zone, TDZ for short
The naming convention aside, this rule is actually simpler in practice than theory.

In short, it is because of the __temporal dead zone__ that trying to access `let` and `const` statements before they have been declared results in a `ReferenceError`.

The contrast between `var` and `let`/`const` can be seen during the Creation Phase of the Execution Context.

During the Creation Phase:
* `var` is hoisted then initialized as `undefined`.
* `let`/`const` is hoisted _but_ is __not__ initialized!

It is only during the Execution Phase where `let`/`const` receive their assignments!
* `var` is assigned the value (if there is one).
* `let` is assigned the value (if no value, `undefined`).
* `const` is assigned the value (but requires the assigned value).

The TDZ is truly a _temporal restriction_ __not__ a lexical restriction. See below:
```javascript
function foo () {
  console.log(bar);
}

let bar = 'funny';
foo();   // 'funny'
``` 
Essentially, during the Execution Phase of the Execution Context, when variables are typically assigned their values, the TDZ ends when it reaches the `let`/`const` declaration.

The question now is: why the temporal dead zone?

There are two oft-cited reasons (the second reason more important than the first):
1. TDZ prevents reliance on hoisting to make code function (poor coding style). It throws errors to alert the programmer to a possible unintended access before declaration situation.
2. TDZ allows for better semantic reasoning surrounding `const`. Allen Wirfs-Brock, the project editor for ES6, notes, "the motivating feature for TDZs is to provide a rational semantics for `const`. There was significant technical discussion of that topic and TDZs emerged as the best solution." [See Link](https://esdiscuss.org/topic/performance-concern-with-let-const).

The second reason makes sense when you consider what `const` is _supposed_ to do -- that is, create an immutable _binding_. If `const` can only be bound once, then it would not make sense for the `const` variable to follow the "`var` assignment to `undefined`" pattern during the Creation Phase. `const` should not and cannot be bound to `undefined` then later changed to the proper binding!

To maintain consistency, `let` was also affected by TDZ though `let` does not "require" TDZ to maintain semantic rationale.

##Conclusion
There are many interesting consequences of ES6 that affect identifier resolution and scope. Particularly with the introduction of block scoping through the `let` and `const` keywords, identifier resolution through the Lexical Environments are more nuanced than before. It is important to keep up with these changes to understand some errors that may be thrown as a result of misconceptions of block scope.

Again, my hope is to communicate a more digestable block of text than the formulaic script of the ECMAScript specifications. I plan on covering some React/Redux posts in the future as well. I don't plan on departing from the hole that is the ECMAScript docs forever, but it may be good to change things up!

__Read more:__
* [ES6: Let and Const Declarations](https://www.ecma-international.org/ecma-262/7.0/#sec-declarations-and-the-variable-statement)
* [Let, Const, and Temporal Dead Zone in Depth](https://ponyfoo.com/articles/es6-let-const-and-temporal-dead-zone-in-depth)
* [You Don't Know JS: Function vs. Block Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md)
