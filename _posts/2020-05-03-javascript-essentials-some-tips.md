---
title: Javascript Essentials, some tips 
author: Dimitris Georgoulas
date: 2020-05-03 11:33:00 +0800
categories: [Web-Development, Javascript]
tags: [javascript, tips]
toc: true
---

# Javascript engine and runtime environment
JavaScript is an interpreted language. This means that the source code isn’t compiled into binary code prior to execution. 
Instead, it needs a special program, the interpreter, which reads over the JavaScript code, interprets each line in machine instructions, and executes them. 

Javascript code runs within a “container” program. Web browsers and node.js are examples of such container programs. 
The interpreter is part of these "container" programs and is called the **Javascript Engine**. It is responsible for translating javascript code 
into runnable machine instructions so it can be executed by the CPU of the host machine. There are a couple 
of different Javascript engines. Chrome and node.js use V8 while Firefox uses SpiderMonkey. 

The container program, part of which is the engine, acts as a **Javascript runtime environment** 
(the environment within which javascript is executed) and provides your 
scripts with utility libraries that can be used during execution and that allow you to interact with that environment. 
For example, things like the _Array_, _Function_, _Object_ etc are part of the Core Javascript library but web browsers 
provide the _window_ object, the _DOM_ objects etc. while node.js provides _require_, _buffers_ and _processes_. 
These objects are not part of the core javascript library. They are exposed by the runtime environment and javascript code 
can use them.

# Scope
The "scope" is a special internal javascript object called LexicalEnvironment, to which variables and functions are added as properties. 
The top-level LexicalEnvironment in a web browser is the window object. It is also called the global object or the global scope. 
Functions have their own local scope, their own LexicalEnvironment object. Blocks on the other hand, do not have a 
scope. Any code inside a javascript block (if, for, while etc.) does not have 
scope, it is “stored” in its containing function's scope or in the global scope. 

> ***Note***: All functions have a hidden _[[Scope]]_
property which references the scope in which they live (not their own local scope). This property is added during the 
initialization phase. You can access it with **_function_name.[[Scope]]_**. 

# Initialization and running phase
Running a javascript file is a two step process. In the first phase, the **initialization phase**, the engine 
parses the code, checks the syntax, collects function and variable definitions and adds them to the global scope. 
The code is not executed in this phase but 
on the second one, the **running phase**, in which the code is parsed again from the beginning and is executed top to bottom.

In the initialization phase, the engine scans the code and adds to the global scope first the function declarations (not function expressions) 
and then the global variables (defined with var). At this point, variables get the value of "undefined". 
The actual value assignment happens later during the running phase when the execution reaches them. 
So, you can call a function before you define it, since the global scope would contain the function as 
one of its properties. Have a look in the following example:
```javascript
show(a)  // prints undefined instead of an error 
var a = 0
function show(arg){
  console.log(arg)
}
show(a)  // prints 0
```
During the initialization phase the global scope is populated with the function _show_ and the variable _a_. 
```javascript
// after initialization phase
global-scope = Top-level LexicalEnvironment = window = show.[[Scope]] = {show: function, a: undefined}
```
During the running phase when the _show(a)_ call is to be executed, both the _show_ function and the _a_ variable 
exist in the global scope despite the fact that they haven't been defined yet. At this point the value of _a_ is 
_undefined_. 

> ***Note***: The fact that that the variable is added to the scope during initialization phase with value undefined, or in other 
words that the declaration of the variable is done in the initialization phase separately of the assignment 
to its value, is also called **hoisting** (the declaration of the variable is hoisted to the top). 
 
# Function declaration and function expression
There are two ways to define a javascript function. Either using a function declaration or a function expression. 
The big difference between the two is that with function declaration the function is stored in the scope during the 
initialization phase while with function expression it doesn't (so you can't call it before its definition in the code). 

**Function declaration**  
We have a function declaration whenever the definition of a function starts with the _function_ keyword.
```javascript
// function declaration
function meaning(){
    console.log(42);
}
```
During the initialization phase a variable which has the same name with the function will be automatically defined. 
This variable will reference the declared function object and is the variable that is stored in the scope. So, in this example 
the scope would be _{ meaning: function }_.    

**Function expression**  
A function expression creates a new function at runtime. The big difference with the function declaration method is that 
during the initialization phase, the function defined with a function expression is not stored in the scope. 
It is created directly at runtime. 
In this method, a function is defined as a part of a larger expression syntax, typically a variable assignment. 

Functions defined via function expressions can be either named or anonymous. 
```javascript
// named function expression
var yo = function meaning(){
    console.log(42);
}

// anonymous function expression
var yo = function() {
    console.log(42);
}
```
Another implementation of a function expression is the self invoking anonymous function expression.
```javascript
// self invoking function expression
(function meaning() {
    console.log(42);  
})();
```
Since a function expression can't start with the _function_ keyword, we must surround it with parentheses. 
These surrounding parentheses are called the _Grouping operator_ and force the function to be an expression. 
The trailing initiating braces at the end _();_ make the function self executable. At runtime phase, it will be 
defined and then immediately executed.

> ***Note***: There are alternative ways for defining self invoking function expressions. If you come 
>across them rest assured that they are just self invoking function expressions.
>```javascript
>// alternative syntax for a self invoking function expression
>(function meaning(){
>     console.log(42);
>());
>
>// alternative syntax for a self invoking function expression
>+function meaning(){
>     console.log(42);
>}();
>```

# Arguments are passed by value
In JavaScript, all function arguments are always passed by value. This means that changes to the value of an argument 
inside the function, are not reflected to the variable (that is the argument) outside of the function. The reason is 
that the arguments are added to the function's scope as new local variables. Whatever happens to the arguments happens 
to these local variables. After the function finish its execution, its scope is destroyed. 
If function arguments were passed by reference and not by value, the changes would have been 
reflected outside the function.

```javascript
// Arguments are passed by value
function increment(counter){
  counter += 1
}
var counter = 0
increment(counter)
console.log(counter)  // prints 0
```

> ***Note***: Javascript object properties are references. This means that if you pass an object as argument to a function 
>and modify the value of a property of the object (and not the object itself) inside the function, then this change will persist in the object 
>outside of the function.   
```javascript
function increment(object){
  object.counter += 1
}
var object = {"counter": 0}
increment(object)
console.log(object)  // prints {counter: 1}
```
But, if you change the value of the object as a whole, then the outer object is not affected at all since as we said, 
arguments are passed by value, not by reference.  
```javascript
function increment(object){
  object = {"counter": object.counter + 1}
}
var object = {"counter": 0}
increment(object)
console.log(object)  // prints {counter: 0}
```