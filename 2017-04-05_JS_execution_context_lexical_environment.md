## Javascript Basics: The Execution Context and the Lexical Environment
###### _What is actually happening when code is executed/functions are invoked?_

A lot of the magic of code happens behind-the-scenes where the code is being compiled and interpreted.

JavaScript is described as an interpreted language rather than a compiled language like C or Java. The distinction between an interpreted language and a compiled language might be a future blog post. However, much of the discussion for new JavaScript developers on this subject tend to be confused over the compilation step.

Semantically, you have two ways of understanding compilation:
1. Compilers take the __entire__ program code and "compiles" it into machine code _before_ the code is executed.
2. Compilation can simply mean making program code digestable for the machine to run it.

The first definition is the traditional definition of what being a compiled language means. After all, if you think about it, if compilation simply means taking program code and making it machine-readable, all languages would be "compiled". It is under the traditional definition, JavaScript is understood as an interpreted language, where Google's V8 engine compiles the program code line-by-line just as it is being run -- called the Just-In-Time (JIT) Compiler. _But more on this in a later post_.

***

####Execution Context

The __Execution Context__ is the _abstract concept_ of the environment in which the current code is being evaluated in. In the ES5 documentations, the execution context "contains whatever state is necessary to track the execution progress of its associated code."

Typically, this "environment" is either: (1) the default __global__ environment or (2) the __function__ environment, code inside a function.
    _Note: ```eval``` code is a distinct environment; however, for the scope of this post, only the global and function environment will be discussed below._

When the JavaScript interpreter "compiles" the code line-by-line before it is executed, it creates a __call stack__. Because JavaScript is a single threaded language, only one thing can happen in the browser at a time, the rest are in this call stack waiting to be run. Imagine a typical stack with the _default global execution context_ at the bottom and any number of _function execution contexts_ stacked on top. The same is true is for function calls _inside_ another function. Simply put, _function-level execution contexts_ can be stacked on top one after another until the code is run through. Think: box on top/within a box.

Going back to the line-by-line compilation of JavaScript code _before_ the code is executed, there is actually something happening behind-the-scenes!

This is called the __creation phase__.

######Phase 1: The Creation Phase
During the microseconds before JavaScript code is ever executed, three things happen during the creation phase:
  1. The __ThisBinding__ State Component is determined.
  2. The __LexicalEnvironment__ State Component is defined.
  3. The __VariableEnvironment__ State Component is defined.

First, the __ThisBinding__ State Component...

This is simply the value of the ```this``` keyword in the current execution context.

In the _global execution context_, the ```this``` keyword is bound to the Global Object (in the browser, this is the Global Window Object).

In _function code_, however, the  ```this``` keyword depends on how the function is called.

For example:
```javascript
  let foo = {
    bar: function () {
      console.log(this);
    };
  };

  foo.bar() // 'this' is bound to the 'foo' object because 'bar' was called with reference to 'foo'

  let baz = foo.bar;
  baz() // 'this' is bound to the Global Window Object because no reference was given when called
```

In summary, if an object reference is given as the function is called, the ```this``` value will point to that object. However, if no object is given (or that reference is ```null``` or ```undefined```), the ```this``` value will point to the Global Window Object.

Second, the __LexicalEnvironment__ State Component...

I think it is helpful to think about why this is important. In short, one has to wonder, how does the machine know where to look for all the variables declared in the code? The ```LexicalEnvironment``` is where this "identifier resolution" happens!

The official ES5 docs defines Lexical Environment (an abstract concept really) as the place where "the association of Identifiers to specific variables and functions based upon the lexical nesting structure of ECMAScript code" is stored. Jargon aside, there are two main takeaways from this definition:
  1. __Identifier associations__ are simply the _binding_ between variable and function declarations with their values. (e.g., ```let x = 10```, 'x' is bound to '10').
  2. __Lexical structure__ is just describing the actual location where the code was written. _See below_.
  ```javascript
      let foo = function (a) {
        let b = 10;             // 'b' and 'bar' would be in the lexical structure of 'foo'
        let bar = function(a) {
          let c = 20;           // however, 'c' would only be in the lexical structure of 'bar'
          return a + b + c;     // because 'c' is written inside the 'bar' function.
        }
      }
  ```
  This about _where_ the code is written and that's how you know which variable and function declarations go into which ```LexicalEnvironment```.

Now, _within_ this Lexical Environment are two components: (1) the __environment record__ and (2) a __reference to the outer environment__.
  1. The _environment record_ is the place where the variable and function declarations are stored.
  2. The _reference to the outer environment_ is the way in which the machine conducts identifier resolution (scope).
  
  * In the _global_ lexical environment, one should expect the built-in Object/Array/etc. prototype functions inside this environment record as well as any user-defined global variables. And the reference to the outer environment would be set to ```null``` (as it is the outermost environment).

  * In _function code_, the user-defined variables inside the function AND the lexical structure (see above!) are stored in the _environment record_. And the reference to the outer environment can be the global environment, or whatever outer function that wraps around the inner function.

To make matters even more confusing, there are again two types of _environment records_...
  1. __Declarative environment record__ handles variables, functions, and parameters. (similar to the ES3 'activation object' concept)
  2. __Object environment record__ describes the way in which identifier associations are defined in the _global context_ (and _with_ statements).

In short,
  * In the _global_ execution context, the environment record is the _object environment record_.
  * For _functions_, it is the _declarative environment record_.

__A quick, but important note:__ for ```function``` code, the _declarative environment record_ also contains an ```arguments``` object that has indexes mapped to arguments as key:value pairings and the _length_ of the arguments passed into the function.

* As implied above, for _global code_, the ```LexicalEnvironment``` is set to the _Global Environment_ (with the _object environment record_ and the reference to the outer environment set to ```null```).

* For _function code_, the ```LexicalEnvironment``` will contain all the variable and function declarations. This is actually where we see the "hoisting" phenomenon -- where declarations are brought to the top of its lexical scope prior to any assignment to a value.

And finally, the __VariableEnvironment__ State Component...

This is actually a copy of the __LexicalEnvironment__. There is actually no difference between the two EXCEPT when working with ```with``` statements. ```with``` statements actually modify the ```LexicalEnvironment``` and not the ```VariableEnvironment```. Again, using ```with``` statements are outside the scope (no pun intended) of this discussion; in fact, some developers argue that the ```with``` statement should be avoided altogether for performance reasons. 

As such, assume the two are the same thing in most cases.

######Phase 2: The Execution Phase

This is the most straightforward section of this entire post. After the V8 Engine has gone through the code, bound the ```this``` keyword and defined the ```LexicalEnvironment``` and the ```VariableEnvironment```, finally the code is executed!

Here, assignments to those variable/function declarations are finally done, code is run and we see the end result of our code!

####Why does any of this stuff matter?

Chances are, you don't have to know any of this to demonstrate understanding of concepts like "hoisting" and scope/identifier resolution to most developers. However, if you are anything like me, and desire to know exactly what is happening at a deeper level (but still more abstract than machine code), then now you can explain exactly what is happening according to the ECMAScript specifications. 

For personal learning, understanding where identifier resolution actually happens (```LexicalEnvironment```) and why "hoisting" occurs (Creation Phase vs. Execution Phase) was illuminating. A lot of articles won't dig this deep because at a certain point, you might as well read the actual documentation. But here I tried to digest that in a way so I can understand it. And I hope it is helpful to any readers out there, who like me loves digging deep.

Read More:
* [Dmitry Soshnikov's post on the Theory of Lexical Environments](http://dmitrysoshnikov.com/ecmascript/es5-chapter-3-1-lexical-environments-common-theory/)
* [Dmitry Soshnikov's post on ECMAScript Implementation of Lexical Environments](http://dmitrysoshnikov.com/ecmascript/es5-chapter-3-2-lexical-environments-ecmascript-implementation/)
* [ES5 Documentation on Code Evaluation and Execution Context](http://es5.github.io/#x10.4)
* [ES5 Documentation on Lexical Environments](http://es5.github.io/#x10.2)
