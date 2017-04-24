##JavaScript Basics: Identifier Resolution and Scope!

__Note:__ I recommend reading my previous blog post on [JavaScript Execution Context and Lexical Environments](https://medium.com/dailyjs/javascript-basics-the-execution-context-and-the-lexical-environment-3505d4fe1be2) first.

It isn't really difficult to conceptualize how variable declarations and their assignments are resolved in JavaScript.  As mentioned in my [previous blog post](https://medium.com/dailyjs/javascript-basics-the-execution-context-and-the-lexical-environment-3505d4fe1be2), this is because _identifiers_ are resolved through the `LexicalEnvironment`'s environment record. __Identifiers__ are variables that we assign values or references to. For example, in `var x = 10`, `x` is an identifier. Once a variable has been declared (i.e., an identifier is assigned some value), when that variable is used anywhere in your code, it goes through a process called __identifier resolution__.

##Identifier Resolution
__Definition first:__ __Identifier resolution__ is the machine process that returns the value of a variable by looking up the scope chain.

In the [ES6 specifications](https://www.ecma-international.org/ecma-262/7.0/#sec-getidentifierreference), identifier resolution is accomplished through the formulaic `GetIdentifierReference` function. This "function" is obviously not exposed and is defined as an "abstract operation." 

We won't actually go through the "function" step-by-step. However, it is worth noting that because the Lexical Environments (i.e., `LexicalEnvironment` and `VariableEnvironment`) of the current Execution Context has not only the environment record(_envRec_), but also a __reference to the outer__ Lexical Environment (__outer__), `GetIdentifierReference` is a recursive operation checking not only it's own _envRec_ but also every surrounding Lexical Environment until the __outer__ is `null` or the identifier is found. The "chain" of Lexical Environment records is the __scope chain__.

Check the code below:
```javascript
function foo() {
  var a = 1;
  function bar() {
    var b = 2;
    function baz() {
      var c = 3;
      console.log(a);
    }
    baz();
  }
  bar();
}
foo(); // 1
```
Next, I will attempt to show identifier resolution in pseudo-code:
```javascript
// `foo` invoked
// creates FooLexicalEnv
FooLexicalEnv = {
  a: 1  // foo has the variable `a` in its lexical env
  outerLexEnv: global
}
// `foo` invokes `bar`
// creates BarLexicalEnv
BarLexicalEnv = {
  b: 2
  outerLexEnv: FooLexicalEnv
}
// `bar` invokes `baz`
// creates BazLexicalEnv
BazLexicalEnv = {
  c: 3;
  outerLexEnv: BarLexicalEnv
}
// `baz`'s scope chain looks like this
BazLexicalEnv => BarLexicalEnv => FooLexicalEnv => global
// in contrast, `foo`'s scope chain looks like this
FooLexicalEnv => global

// `baz` console logs `a`
// identifier resolution on currentLexEnv, variable, outerLexEnv
ResolveIdentifier(BazLexicalEnv, a, BarLexicalEnv) // `a` not in BazLexEnv
ResolveIdentifier(BarLexicalEnv, a, FooLexicalEnv) // `a` not in BarLexEnv
ResolveIdentifier(FooLexicalEnv, a, global) // `a` found in FooLexicalEnv!
// `a` is found, it's value (1) is returned, `baz` console logs 1
```
As you can see, the check for the identifier propagates outward from the current Lexical Environment until it finds (or does not find) the variable. Also, you can see that the __scope chain__ of each execution context is unique (i.e., the scope chain of `foo`'s EC is different from `baz`'s).

__Side note__: It may be confusing to refer to both the "Lexical Environment" (observe the space between the two words) and `LexicalEnvironment` (no space). I wrote this to be consistent with the ECMAScript specs. __Lexical Environment__ is the concept, `LexicalEnvironment` and `VariableEnvironment` are the implementation of that concept.

##The Scope Chain/Scope
I mentioned __scope chain__ multiple times already but it is helpful to fine-tune a definition to deter misconceptions.
__Definition:__ The __scope chain__ of an execution context is the internal "chain" of Lexical Environments that is traversed during identifier resolution.

__Key takeaways__ 
First, the scope chain does not exist at author-time (when I write it). It is created when the function is invoked after creating the execution context of that function. This is why I wrote, "The scope chain _of an execution context_..."
Second, it is read-only (internal) to the machine. Browsers sometimes "expose" this read-only scope chain as `[[Scope]]` but you cannot do anything with it. 
Lastly, it is a chain of Lexical Environments that is traversed (see Identifier Resolution above).

It is a lot simpler in practice than to know what is actually happening under the hood.

Finally, you might be wondering if there's a difference between __scope chain__ and __scope__. The short answer is that the two are the same thing. There is just a minor, truly inconsequential difference conceptually. __Scope__ refers to the abstract concept of the execution context's "reach" (I'm grasping for words here -- I want to use "scope") for identifier resolution. __Scope chain__ is just the implementation of that concept in JavaScript. Basically, it is the same thing. __Scope__ just defines what it is, __scope chain__ gives it a name.

##The future is now (ES6)!

Before ES6, checking the `LexicalEnvironment` for identifier resolution accomplished identifier resolution. But with the advent of block scoping (using the `let` and `const` keywords), a not-so-small nuance was added on top of this. [See my blog post on block scope for more details on that]

Because ES6 is now the standard in JS development, `let` and `const` is important to understand. `var` won't go away anytime soon (mostly because legacy code), but it's important to keep up!

__Read more:__
* [Variables and Scoping](http://2ality.com/2015/02/es6-scoping.html)
* [ECMAScript: GetIdentifierReference](https://www.ecma-international.org/ecma-262/7.0/#sec-getidentifierreference)
* [ECMAScript: ResolveBinding](https://www.ecma-international.org/ecma-262/7.0/#sec-resolvebinding)
Still relevant, but now "outdated" terminology describing the Lexical Environment as an activation object:
* [Identifier Resolution and Closures in the JavaScript Scope Chain](http://davidshariff.com/blog/javascript-scope-chain-and-closures/)
