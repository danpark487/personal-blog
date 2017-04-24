## JavaScript Basics: Lexical Grammar, Expressions/Operators and Statements
###### _How does the computer understand JavaScript?_

It is extremely important to understand the semantic differences in a JavaScript __statement__ and a JS __expression__. Why? Because JavaScript is filled with quirks that are difficult to reason with unless you understand how the machine will read your code. Imagine someone comes up to you and says, "Is hot water now." As a human listener, you may understand what this person is trying to say, maybe, "It is hot, can I have some water now?" (?) but a computer does not have that kind of interpretive capacity. The computer machine might be extremely fast at reading, but it cannot deviate from a specific set of rules to understand a particular statement. However, there is some flexibility, the machine has been programmed to understand seemingly nonsensical pieces of code which unfortunately creates strange interactions in JavaScript. 

ECMAScript calls these "rules" the [__lexical grammar__](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-language-lexical-grammar) of JavaScript. Much like the grammar of any human language, JavaScript has strict rules in what constitutes an __valid__ statement. __Note, I did not say "strict rules that constitutes an _understandable_ statement."__ In the English language, __understandability__ often trumps __validity__. As in the hot-water statement above, while the statement may be _invalid_ by the rules of English grammar, the statement may be _understandable_ to a listener who might give the person a glass of water! In computer code, a _valid_ statement _is_ an _understandable_ statement! If the computer is not programmed to interpret "Is hot water now" as "It is hot, can I have some water now?", then the computer will throw an error or simply not execute it as you might expect.

> Just as one should try to communicate grammatically in human languages, it should be a priority for developers to understand the lexical grammar of JavaScript to communicate with the computer.

My series on JavaScript Basics is built on a simple idea: Understanding JavaScript leads to better code. It reduces unintended results, mitigates bugs, prevents confusion, and most importantly, grants the user the freedom to exercise the full extent of the language's capabilities! I believe this is a foundational concept that will greatly contribute to that end.

Let's start with __lexical grammar__.

##Lexical Grammar -- The "How" of JavaScript Understanding

According to the [ECMAScript specifications](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-language-lexical-grammar), __lexical grammar__ is the set of rules that determine _how_ the computer will parse (understand) the source code (written by you). This includes line breaks, white space, comments, punctuators, semi-colons, identifiers and more! Every little character (empty space, letter, symbol, or number) has meaning!

See the following code below:
```javascript
var
a
=
3
```
Would it surprise you if I were to tell you that those four lines is _valid_ in JavaScript (read: JS lexical grammar compliant)? Why? Because if you look at the section in the ECMAScript specs called [__Line Terminators__](https://www.ecma-international.org/ecma-262/7.0/#sec-line-terminators), it says that "line terminator code points are used to improve source text readability and to separate tokens (indivisible lexical units) from each other." In essence, line breaks in your code are for you. The computer will interpret the code above as `var a = 3`. 
However, the following line terminator will create a probable unintended result:
```javascript
function foo() {
  var a = 10;
  var b = 10;
  return    // line terminator
  a + b;    // on a separate line
}

foo(); // undefined!
```
You may have thought that invoking `foo` should have returned `20`. However, in a __return statement__, a line terminator "breaks" the return statement. If you must put `a + b` on a separate line, you should use something called a __grouping operator__, which is a set of parentheses, `()`. See below:
```javascript
function foo() {
  var a = 10;
  var b = 10;
  return (
  a + b
  )
}

foo(); // 20.

// However the following return statement would have been invalid
return  // need to start the grouping operator on the same line as the return statement!
( a + b )
```
You might have felt misled. You were led to believe that line terminators, like white spaces, were for _our_ readability! If only things were that simple in JavaScript. In the lines following the quoted section above, the specification states, "In general, line terminators may occur between any two tokens, but there are a few places where they are forbidden by the syntactic grammar. Line terminators also affect the process of automatic semicolon insertion." Ah there it is... __Lexical Grammar__.

Next up, __expressions__ and __statements__ -- where these lexical grammar rules apply in our code!

##Expressions vs. Statements -- The "What" of JavaScript

First, the definitions:
* A valid __expression__ in JavaScript is the combination of _tokens_ that results in a value.
* A __statement__ in JavaScript is the collection of one or more _expressions_ and _keywords_ that completes a valid JavaScript thought.

__Expressions__
Going back to English language analogy, an __expression__ in JavaScript, can be likened to a phrase in English. For example, the sentence "Because the weather is hot, I need a glass of water," can be broken up into two phrases: (1) "The weather is hot" and (2) "I need a glass of water." While those phrases can be broken up further, in the context of the full sentence, they exist as parts, each communicating an idea (in JS, this would be a value) to the overall sentence. Keeping in line with this analogy, the sentence would be the __statement__ in JavaScript.

To be certain, this is an imperfect analogy that falls apart under any level of linguistic scrutiny. The main takeaway is to see that __expressions__ can be distinguished from __statements__ by (1) the _context_ in which they are written or said __AND__ (2) that __expressions__ are usually a smaller subset of a __statement__ (though not always the case as in _expression statements_).

Look at the definition above -- there are 3 key parts to that sentence: (1) validity, (2) tokens, and (3) value.

First, _validity_ is not only in syntax, but by its _context_. For example:
```javascript
var a = 10;
a;          // this is a valid expression because `a` has been declared already
b;          // this is an invalid expression becase `b` has NOT been declared
```
By the strictest definition of an __expression__, `b` is indeed an expression, but it is not a valid one in the lines above. It may be syntactically correct, but it is not grammatically (lexical grammar) correct. And as such, it is more helpful to discuss __valid__ expressions.

Second, _tokens_ ([see here](https://www.ecma-international.org/ecma-262/7.0/#sec-tokens)) describe a single "word" of code. This can be a string/number literal (e.g., `"hello"`, `6`), an identifier (`a`), a punctuator (`=`), etc. When you combine these tokens in a way that results in a value, you have an expression!

Lastly, _value_ means that the expression results in a specific value. This implies that a valid expression can be subsituted by its resulting value without breaking the code. Illustrated below:
```javascript
10;     // results in 10, therefore an expression
var;    // NOT an expression but is a keyword (a type of token), results in a SyntaxError!
a;      // An expression, but an invalid one as we saw in the previous code example
a = 10; // finally a valid expression, results in 10;

function foo(x) {
  console.log(x);
}
                // because `a = 10` is a valid expression, it can be substituted for its value
foo(a = 10);    // 10, invoking the function in this manner is permissible, though not that common
```
The example above demonstrates that a proper JavaScript expression is a particular combination of tokens that produces a value. But we also saw that not just any mix of tokens will achieve that.

So then... what are __operators__? There's a [whole list of them](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-language-expressions)!

__Operators__ are punctuator tokens (e.g., `(`, `[`, `<`, `<=`, `=`, `+`, `-`, `*`, `%`, `++`, etc.) that can combine with string/number literal tokens to form an expression. Some are __unary__ (only one operand required) operators (e.g., `+`, the unary plus operator which converts its operand into a number), some are __binary__ (requiring two operands) like the addition operator (`+`) which adds two numbers together. __Note:__ though the same symbol, the evaluation depends on the context (1 or 2 operands present). There is a single ternary operator (conditional operator) that follows the `condition ? expression : expression` pattern using `? :`.

It may be helpful to think of operators as the articles, conjunctions, and prepositions in English. Just as "for" or "the" lacks value meaning in isolation, when combined with other words, it can convey value/meaning. To be sure, I do not intend to say that "for" or "the" lacks definition value. Of course, these words have a definition. However, by itself, these words does not convey information that can be understood by someone. Likewise, though operators like `+` and `*` has definitions (addition, multiplication) attached to it, the computer does not evalute them as a value on its own.
Code:
```javascript
+;    // SyntaxError! (it expects something after it)
+10;  // 10, now this is an expression using the unary plus operator
```
An __expression__ doesn't necessarily _need_ an operator (`a` is an expression by itself), but even the most simple pieces of code will have them. Just be aware that they can be used to compose more "complex" expressions. There are so much more you can talk about with operators such as _operator precedence_ and the specific definitions of the many operators available, however, this is enough to understand its relevance to this topic on expressions.

__Statements__
Continuing with the English language analogy (imperfect as it may be), __statements__ are the sentences of JavaScript. But imagine if you were only allowed to say or write a predetermined number of sentence types. This predetermined number is far less than the number of valid kinds of English sentences obviously. But even in English, this number is limited. I cannot just say "the slowly man here running" and expect people to understand. Remember, even if we might understand that sentence as humans, computers follow strict rules. We cannot just chain some JavaScript expressions together and expect the computer to execute the code as you had intended.
Say I want to write a line of code that will tell me to plug in my laptop if its battery goes under 10%. I write it like this:
```javascript
is batteryPercent < 10, yes? alert('Plug in your laptop! Low on battery!') // SyntaxErrors galore!
```
Duh! This doesn't work, but why not? Because there is no such statement in ECMAScript. Instead, if we wrote `if (batteryPercent < 10) alert('Plug in your laptop! Low on battery!')` this would work because we are using the `if` statment that ECMAScript gives us! There are other statements like iteration statements (`for`/`while` loops), block statements, `return` statements, and [many more!](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-language-statements-and-declarations)

> A valid JavaScript thought is one that has been enumerated in the ECMAScript specifications as a statement. In a roundabout way, you know it's a statement because the documentation says so!

Going back to the jumbled up English sentence, if we wanted to fix it, we can write "The man is running here slowly" or "The man is slowly running here." Here, we see that there are _patterns_ of sentence construction that has the same meaning. In JavaScript, even these _patterns_ are predefined.

For example, let's say that I demand that a conditional statement must always begin with that condition. This would mean "_If_ you want some water, come here" would be a valid sentence under my rule; however, "Come here _if_ you want some water" would be invalid.
Likewise, there is a specific valid pattern for conditional statements in JavaScript:
```javascript
if (true) {
  console.log('valid if statement')
}

{
  console.log('invalid if statement')
} if (true)
```
This is very obvious, but demonstrates once again that you must not only use an enumerated _kind_ of statement (`if` is being used in both instances), but that use must be _patterned_ correctly as the specs indicate. You cannot just chain together some valid expressions in any order expecting output. __Statements__ in JavaScript are enumerated for the computer so that they may know how to handle a chain of expressions. And just as sentences are broken up with punctuation like periods and commas, JavaScript statements are often divided up by semi-colons and sometimes line terminators.

Now comes the tricky part!

##Expressions here, but statement there?
Check out the code below:
```javascript
var a = 10;
var b = a;  // here, `a` is an expression
a;          // here, `a` is used a statement
```
Notice in the __statement__ definition above, I noted that a statement may contain _one_ or more expressions. Here, we see an example of a single expression being a statement. ECMAScript calls this the [expression statement](https://www.ecma-international.org/ecma-262/7.0/#sec-expression-statement). Again, this comes down to the context in which the expression is written. In line 2, `a` is an expression because its context/position is after an _assignment operator_ (`=`). [See more about assignment operators here](https://www.ecma-international.org/ecma-262/7.0/#sec-assignment-operators). However, in line 3, `a` is an expression that is also a [valid JS thought](https://www.ecma-international.org/ecma-262/7.0/#sec-expression-statement) on its own.

The most popular example of this is when looking at a named function expression (NFE) and a function declaration (FD):
```javascript
var a = function foo(x) {
  console.log('I am a Named Function Expression');
}

function foo(x) {
  console.log('I am a Function Declaration');
}
```
What's the difference? I plan on going into much greater depth on this specific topic in the following blog post; however, in short, again the context is key. Because the NFE `foo` is after the _assignment operator_, it is evaluated as a function expression. However, in the function declaration, it is on its own, and the computer knows what a function declaration looks like and will evaluate as a statement on its own. Note, it is NOT a function expression at all (therefore not an expression statement there). The specs on expression statement explicitly states, "an ExpressionStatement cannot start with the `function` or `class` keywords because that would make it ambiguous with a FunctionDeclaration." There you have it. The two are distinct, however the determination of which it is depends on the context (or some like to say the "position").

An analogy pulled from the English language would be something like "run" being used as both a noun and a verb. Depending on the context in which "run" is used, it will be identified by the listener/reader as a noun or a verb. Think: "I _run_ to the store" versus "I went on a _run_". Likewise, depending on where and how the `function` keyword is used to create a function object, it can either be a function expression or a function declaration and this distinction has implications for hoisting, stack traces, etc. 

> Remember, context is king.

##Anatomy of the statement
Lastly, I wanted to briefly just take apart a simple statement that would have been confusing to reason with before your read all this, but now, you can slowly parse and make sense of it.

Consider the code below:
```javascript
var a = 10;
var b = 20;
a;
```
Quickly notice that there are 3 separate statements delimited using semi-colons and line breaks.

If we look at the [specs](https://www.ecma-international.org/ecma-262/7.0/#sec-variable-statement), we see that `var` is not only a special keyword, but signifies the beginning of a _variable statement_! The basic syntax of a variable statement is: `var` + identifier + optional assignment expression. The reason why I wrote "optional assignment expression" is because `var a;` would have been a perfectly legitimate variable statement. On the other hand, `var` without an identifier ([another kind of expression](https://www.ecma-international.org/ecma-262/7.0/#sec-identifiers)) is not a complete, valid JS thought because it does not follow the spec's _pattern_ of a proper variable statement.

So in sum, `var a = 10` can be broken up as follows: `var` keyword + `a` (identifier expression) + `=` (assignment operator) + `10` (number literal expression) or simply as, `var` keyword + `a = 10` (assignment expression).

We know that `var` cannot be an expression because it does not itself produce a value thus cannot be substituted for a value.
Look below:
```javascript
function foo (x) {
  console.log(x);
}

foo(var a = 1); // SyntaxError!
foo(a = 1); // 1 <-- but this works
```
This also explains the following code:
```javascript
var a = 10;
function foo (x) {
  console.log(x);
}

foo(a > 3 ? 20 : 50); // 20
```
This works because `a > 3 ? 20 : 50` is a valid expression using the [conditional operator](https://www.ecma-international.org/ecma-262/7.0/#sec-conditional-operator) and as expected, evaluates to a value. However, an `if` statement cannot do the same because statements cannot take the place of an expression.

##Final Thoughts
JavaScript is a tricky language, but like any other language (machine or human), learning the proper grammar and syntax is crucial to communicate effectively. In code, there is a heightened demand for strict adherence to these rules. As such, it is helpful to look at the ECMAScript specs to see what __statements__ are available for you to use and how you can construct those statements using __expressions__ (and expressions using tokens). 

Just remember, __expressions__ evaluates to a value and can be substituted for one. __Statements__ cannot be substituted in the same way. To distinguish the two, always consider the context/position in which the expression is written.

There's a whole lot more to everything I said in this post, but my goal isn't to get you acquainted with all those details (I certainly don't know all the quirks). The sole purpose is to _expose_ you to this world so you can reason with the idiosyncracies of JavaScript. It isn't magic.

__Read more:__
* [MDN: List of Statements and Declarations](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements)
* [MDN: Expressions and Operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)
* [MDN: List of Expressions and Operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators)
* [2ality -- Expressions versus statements in JavaScript](http://2ality.com/2012/09/expressions-vs-statements.html)
* [ECMAScript Specs](https://www.ecma-international.org/ecma-262/7.0/#sec-ecmascript-language-lexical-grammar)
