## JavaScript Basics: Null vs. Undefined!

* __undefined__ - the __default__ primitive value of declared variables that have not yet been assigned a value (either in the code or during the creation phase of the execution context)
* __null__ - an __assigned__ primitive value (by someone) to indicate an empty value // never a default value

In other words, __undefined__ simply means that the value of the variable hasn't been defined _yet_ (and we don't know if it will ever be).

There are at least 8 ways to get ```undefined```:
1. A declared variable without assigning any value to it.
2. Default return of functions that lack return statements.
3. Return statements that do not explicitly return anything.
4. Result of looking up non-existent properties in an object.
5. Function parameters that have not passed.
6. Any variable that has been intentionally set as ```undefined```.
7. Using the ```void``` operator.
8. The value of the global variable ```undefined``` (it is a property of the _global object_).

On the other hand, __null__ means that the value is empty or lacks a value that has been _intentionally_ set by a programmer. The machine does not set variables to ```null``` for you. It is a purposed primitive.

##### Checking Equality!
```javascript
typeof null;          // "object" (not "null" because legacy reasons)
typeof undefined;     // "undefined"
null === undefined;   // false
null == undefined;    // true
null === null;        // true
null == null;         // true
!null;                // true
isNaN(1 + null)       // false
isNaN(1 + undefined)  // true
```

Read more:
* [MDN docs -- null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)
* [MDN docs -- undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)
