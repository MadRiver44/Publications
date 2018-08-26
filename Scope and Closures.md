# Taking a trip to Closure Town
## Understanding Scope and Closures in Javascript

To really dig deep and get the information you need, think like a journalist and ask the six main questions, who, what, why, where, when, and how. If you can answer all these on a particular subject, then you have suceessfully been able to get to the essence of what you need to know.

Before we, get to closures we have to have an understanding of scope.

First, if you know what    [[scope]]  (double bracket scope) is, then this article isn't for you, take your advanced knowledge and move on.

### The what...
 What is __scope__ and why does it matter?

 > Scope is the execution context environment created when a function is invoked. This context defines what other data it has access to.

 Put another way, scope is about access. Does the function have the ability to look up a variable for execution or manipulation, which variables are visible?

There are two types of scope, local and global. Scope resolution, or finding what variables belong where, starts at the innermost context and proceeds outward until the identifier is found. Let's start small...

```
var firstNum = 1;

function number() {
  var secondNum = 2;
  return firstNum + secondNum;
}

number();

```
### The when, why and how... execution context
When function number, is invoked, a new execution context is formed. What is an execution context? Well, just as we have a two types of
scope, we have two types of execution context, a global execution context and a function execution context. The global context is always running, and in the case of a browser environment, only stops when the browser is closed. When we call a function, we place that function's execution context on top of the global execution context, we __stack__ them. Because Javascript is a single threaded language, that means it can do only one thing at a time, when we call a function, the previous execution context is paused and the one currently on the top is then executed. When that finishes, it is popped off the stack and then the older execution context is resumed. This 'stack' of execution is what keeps track of the position of execution in our application. It also is important in looking up identifiers.

So now we have an execution context formed, what's next?

__Each execution context has an associated variable object.__

First, a __Activation Object__ (not accessible by code, yet operates in the background) is formed that is associated with this execution context and this object holds all variables declared within that context (it's scope or accessibility range), including parameters that are passed. Parameters to a function are implicitly defined, therefore they are 'local' to that function's scope. These declared variables are "hoisted", taken to the top of the scope that they belong to. Before I go further, to avoid confusion, in the global execution context, a __Variable Object__ is created, if it is a function, it is an __Activation Object__. They are pretty much identical.

Now when this function is invoked a "scope chain" of these objects is created. Why? The scope chain is a way to link or provide a systematic access to all variables and other functions that the current execution context (function in this case) has access to. [ [Scope] ] is the hidden mechanism that links these variable objects for identifier lookup.

At the head of the scope chain train, if it is a function, is the __Activation Object__ . This activation object has it's own declared variables, arguments and this.

Next, on the scope chain, is the next object from the containing context, (if global, __Variable Object__, if function, __Activation Object__)then the next variable/ activation object from that containing context. This happens until we reach the global context. That is why you can see we start from the innermost context to the outermost, think Russian nesting dolls.

What is the difference between a variable that is declared and one that is undeclared? If the identifier is preceeded by a var, let or const, it is declared explicitly and memory space is allocated for that variable's use. If the identfier is not explicitly declared, then it is implicitly declared in the global scope which we will explore shortly. For purposes of this article I'm sticking with var, no particular reason.

I know, the above was a little technical, and to be honest as I wrote this, I only learned of the Variable and Activation objects myself. Now that you had the deep dive explanation, here's a high angle description...

The scope chain is similar to the protoype chain in that if a variable or property is not declared in a function block but declared outside of it, explicitly or implicitly, the function upon execution, creates a hidden [ [scope] ] (scope chain) reference that links innermost scopes to outtermost scopes. In this case, number's scope chain is linked to the global window object (the containing context that holds function number). This is what allows the engine to look outside of function number to find firstNum and secondNum.

For example, lets take the same function number and change one thing.

```
// global scope  - includes firstNum, secondNum, and the
// function number

var firstNum = 1;

  function number() {
    // local scope for number - only thirdNum is local to number()
    // because it was explicitly declared. secondNum is implicitly declared in the
    // the global scope.

    secondNum = 2;
    var thirdNum = 3;
    return firstNum + secondNum;
  }
// what do we have access to in the global scope?
number(); // 3
firstNum; // 1
secondNum; // 2
thirdNum; // Reference Error: thirdNum is not defined

```
When speaking of global scope, variable declarations, non-nested function declarations and function expressions (still considered a variable definition) are considered in the scope of the global window object in the browser. So as we see above, the window object has a properties firstNum, secondNum, and number added to it. If we proceed along the scope chain looking for a particular identifier, for example, say firstNum was never declared anywhere, we keep looking until we reach the global context's variable object, and if it's not in there, then we get the Reference Error.

```
In a new tab, type "about:blank" in the searchbar. A blank page will open and hit cmd option i to open devtools.

Type the code above and remember, shift enter for a new line!

Now just type "window" and explore all the properties on the the window object.

Look closely and you will see the properties firstNum, secondNum, and number are all available on the window object.

```
When we try to access thirdNum outside of where it was declared, we get a Reference Error, which means that the engine that complies the code, tried to find an identifier in the global scope on the window object and failed. ThirdNum is only availble inside of the function where it was declared. It is encapsulated or private to function number

The question you may have is "Does the global scope has access to everything inside of number?" Again, scope only works from the inside out, the innermost context, local, to the outtermost context, global.
Starting with local scope, we can say that data and variables that are wrapped in a function are only accessible to to members of that function. The scope chain is what links firstNum to number().

When number() is invoked the converstion goes like this...

> Engine: "Number, I'm giving you a new execution context. Let me find what you need to run"

> Engine: "Ok, I see that thirdNum is explicitly declared, you, thirdNum, I'm setting space aside for you, go to the top of number's function block and wait till I call you.
>
> Engine: "Number, I see secondNum, does he belong to you?"
>
> Number: "Nope."
>
> Engine: "Ok, I see you're linked to the global window object, let me look outside of you."
>
> Engine: "Window, I have an identifier name secondNum, does he belong to you?"
>
> Window: "He didn't declare himself explicitly in Number with a var, let, or
> const, so I'll take him and set space aside."
>
> Engine: "Cool. Number, I see firstNum in your function block, does he belong to you?"
>
> Number: "Nope."
>
> Engine: "Window, I see firstNum being used inside of Number, he needs him, does he belong to you?"
>
> Window: "Yes, he was declared."
>
> Engine: "Everyone is accounted for, Now I'm assigning values to variables."
>
> Engine: Number, I'm executing you, ready, go!"

That's pretty much it for understanding scope, The key takeaways are:
  1. Identifier lookup works from the inside out and stops at the first match.
  2. There are two types of scope, global and local
  3. The scope chain is created at function invocation and is based on where variables and/or blocks of code are written.
  4. In Javascript, if an identifier is not preceeded with a var, let, or const, it is implicitly declared in the global scope.
  5. Scope does not go 1 for 1 with a function, it goes 1 to 1 with function invocation. Execute a function 3 times, get 3 different scopes. Why? Because if the execution of a function is finished, it is popped of the execution stack and with it, it's access to other variables via its scope chain. Thus, a new scope is created each time a function is executed. Closures work a little differently!

Let's finish up with a more complex example before we move on to closures.

```
a = 1;
var b = 2;

function outter(z) {
  b = 3;
  c = 4;
  var d = 5;
  e = 6;

  function inner() {
    var e = 0;
    d = 2 * d;
    return d;
  }
  return inner();
  var e;
}
outter(1);

```
1. Before we run anything, hoisting is started at the outside, global level. Therefore we start with a declaration for a variable b, and a function declaration for function object outter. At this point nothing is assigned, we only have these two keys set up in the global scope variable object.

2. Next, we start at __a = 1__, this is an assignment, or a "write to" statement, yet there is no formal declaration for it. So what happens, in the global scope, and if not in "strict mode" is that a will be implicitly declared as belonging to the global scope variable object.
3. We move to the next line and look up identifier b, through hoisting it was accounted for and now we can assign a value, 2, to it.

So far we have...

##### Global Scope Variable Object

| __key__  | __value__ |
| -------- | :--------- |
| b        | 2         |
| outer        | reference to function outer |
| a        | 1         |


4. Since we built the function object outter, at hoisting time, we then jump to execution, outter(1);
5. Remember that upon function invocation, an execution context is first created and with that we create an Activation Object that contains data and variables local to that context. We also form the scope chain.
6. The parameter z is implicitly declared for this function and is assigned 1.

A quick side note, at this time, the function creates it's "this" binding and also an arguments array, which is an array of parameters passed, in this case, z. This is beyond the scope of this article so allow me to glance over it.

7. Now we look for explicit variable declarations in function outter. We have d, and look closely var e is declared after the function inner.
8. Here's some hidden magic, __at this time a hidden [ [scope] ] object for function outter points to the context it was defined in. In this case it points to the Global Scope because the function body of outter is in the Global Variable Object__. You can see here scope extends from the inside out to form this "linking". This is the reference object that allows up to proceed up the scope chain for lookups.

##### Execution context, Scope for function outter

| __key__ | __value__ |
| ------- | :-------- |
| z       | 1
| this    |
|arguments|
|d        |
|e        |
|inner        | reference to function inner

9. We step inside of outter and start at b = 3. Is b declared, nope, so Javascript uses the hidden [ [scope] ] reference/ ponter to move up the scope chain to find a "b". It finds it in the global scope object and assigns  since we are in the body of function outter, we assign b the value 3.

##### Global Scope Variable Object

| __key__  | __value__ |
| -------- | :--------- |
| b        | now assigned to value 3         |
| outer        | reference to function outter object |
| a        | 1         |

10. Next line, c = 4. Since this is a write to identifier c, was c explicitly declared in function outer? No, and therfore it is not found by lookup in outer's Activation Object, so it moves up the scope chain and looks in the global scope Variable Object, not there, but because this is a write to/ assignment operation the global scope will handle it and place it on its Variable Object.

##### Global Scope Variable Object

| __key__  | __value__ |
| -------- | :--------- |
| b        | now assigned to value 3         |
| outer        | reference to function outer object |
| a        | 1         |
| c        | 4         |

11. d = 5. Yes, it is here so we assign it 5

##### Execution context, Scope for function outer

| __key__ | __value__ |
| ------- | :-------- |
| z       | 1
| this    |
|arguments|
|d        | 5
|e        |
|inner        | reference to function inner

12.  e = 6. Remember that straggler var e? It was still declared in the body of f and so we already had a place for it, so we assign it 6. If it wasn't declared like c, we would move up the scope chain for a lookup and since it is a write and not a read operation and not in 'strict mode', it would have been placed in the global's scope.
13. We get to invoking function inner. We start all over like we did with function outter, hoisting, set up an Activation Object and create a hidden [ [scope] ] reference to its containing context. In this case the containing context is function outter, and outter points to the global scope.

##### Execution context, Scope for function inner

| __key__ | __value__ |
| ------- | :-------- |
| e       | 0
| this    |
|arguments|

14. Now with e and in general, variables that are given the same name work like this. Since identifier lookup starts from the innermost scope to the outtermost scope, lookup stops at the first finding of that identifier. In the body of g we see var = 0, done, stop, go no further. The e in the body of function f is "inaccessible". the term that is commonly used is "shadowing". E in function g "shadows" or obscures the e in function f.
15. Next line is d = 2 * d. Before we assign a value to d on the left we have to evaluate the expression on the right, 2 * d. Since d is not local in scope to g, we move up the scope chain to find a variable for d and if it has a value associated with it. We find it in the outter scope in function f and it is there that the value is changed.

##### Execution context, Scope for function outter

| __key__ | __value__ |
| ------- | :-------- |
| z       | 1
| this    |
|arguments|
|d        | 5 changes to 10
|e        |
|inner       | reference to function inner

The important thing here is that __inner is manipulating data in its outter scope!__

16. Function inner returns a value d, 10.
17. Function outter returns the value of function inner.
18. Result is 10;
19. Once function outter has completely finished executing, __garbage collection__ takes place. Garbage collection is the freeing up of resources that is are longer needed. It starts at the global scope and works as far as it has "reachability". The global scope in this example has no handle to function outter or function inner, so whoosh, gone. This is important when we get to closures, because there, we need data and some variables to stick around even after a function has finished running.

# Finally, Let's get some Closure!

### How shall we define a closure?

Let's start with a few definitions, all correct, some more in depth, but get to the same point.

```
1. Closures are functions that have access to variables from another function's scope. This is accomplished by creating a function inside another function.

```

```
2. A Closure is a function that returns another function.

```

```
3. A Closure is an implicit, permanent link between a function and its scope chain.

```
### Why Closures?

Without being able to leverage scope chain rules, async operations would be impossible because there is no guarantee
that data will still be around to use later. Also, because Javascript only has function scope as it's encapsulation mechanism,
closures are the best form of privacy for functions and variables. This is evident in the use of many module patterns in which we
return an object to expose a public api while keeping other methods and variables private. Closures are used in event handling
and callbacks.

An example of a module ...

```
 var Toaster = (function(){
    var setting = 0;
    var temperature;
    var low = 100;
    var med = 200;
    var high = 300;
    // public
    var turnOn = function(){
        return heatSetting();
    };
    var adjustSetting = function(setting){
        if(setting <= 3){
            temperature = low;
        }if (setting >3  && setting <= 6){
            temperature = med;
        }if (setting > 6 && setting <= 10){
            temperature = high;

        }return temperature;
    };
    // private
    var heatSetting = function(adjustSetting){
        var thermostat = adjustSetting;
        return thermostat;
        };
    return{
            turnOn:turnOn,
            adjustSetting:adjustSetting
        };
})();


Toaster.adjustSetting(5);
Toaster.adjustSetting(8);

```
The module Toaster has private locals and a public interface and is written as an Immediately Invoked Function
Expression (IIFE), we create a function, immediately invoke it, and grab the return value.


Another small example,

```
function firstName(first){
    function fullName(last){
        console.log(first + " " + last);
    }
    return fullName;
}
var name = firstName("Mister");
name("Smith") // Mister Smith
name("Jones"); //Mister Jones

```
The inner function fullName( ) is accessing the variable, first, in it's outter scope, firstName( ).
__Even after the inner function, fullName, has returned, it still has access to that variable__.
How is this possible? The inner function's scope chain includes the scope of it's outter scope.

When a function is called, an execution context and a scope chain are created, in addition the function
get's a hidden [ [Scope] ] property. The __Activation Object__for the function is initialized and placed in the chain,
then the outter function's activation object is placed in the chain, and in this case, finally the global
__Variable Object__.

In this example, fullName is defined, a [ [Scope] ] property is created, and the containing functions's activation
object is added to fullName's scope chain in addition to the global variable object. This reference to an outter
function's activation object enables access to all of the containing scopes variables and does not get garbage collected.

__This is most important. The activation object of the outter function, firstName() can not be destroyed once
it is finished executing because the reference still exists in fullName's scope chain. After firstName( )
execution completes, its scope chain for that execution context is destroyed but the activation object will
remain in memory until fullName( ) is destroyed.__ We can do that by setting its reference to null.

The keen observer will note that we return a reference to fullName, not the return value of fullName( )!

This is what we mean by an implicit, permanent link between and function and it's scope chain.

A closure always gets the last value from the containing function because the reference to the variable
object is stored.

For instance ...

```
var myFunctions= [];
function createMyFunction(i) {
    return function() {
           console.log("My value: " + i);
            };
        }
for (var i = 0; i < 10; i++) {
myFunctions[i] = createMyFunction(i);
myFunctions[i]();
}

 My value: 0
 My value: 1
 My value: 2
 My value: 3
 My value: 4
 My value: 5
 My value: 6
 My value: 7
 My value: 8
 My value: 9

```

If we go back to our original scope example and change one thing

```
a = 1;
var b = 2;

function outter(z) {
  b = 3;
  c = 4;
  var d = 5;
  e = 6;

  function inner() {
    var e = 0;
    d = 2 * d;
    return d;
  }
  return inner; // we remove the call operator, now we are returning a reference to function g
  var e;
}
myG = outter(1); // store a reference to function inner in the global scope (the return value of outter)
myG(); // when we execute myG, inner's [ [Scope] ] property is copied to recreate the scope chain,
       //  and that gives it access to the scopes that contain function inner, outter then global. We got inner and inner's got outter.

```
Here are a few more examples

```
function make_calculator() {
    var n = 0;  // this calculator stores a single number n
    return {
      add: function(a) { n += a; return n; },
      multiply: function(a) { n *= a; return n; }
    };
}

first_calculator = make_calculator();
second_calculator = make_calculator();

first_calculator.add(3);                   // returns 3
second_calculator.add(400);                // returns 400

first_calculator.multiply(11);             // returns 33
second_calculator.multiply(10);            // returns 4000

```
Suppose we wanted to execute and array of functions
```

function buildList(list) {
    var result = [];
    for (var i = 0; i < list.length; i++) {
        result.push(function number(i) {
          var item = 'item' + list[i];
          console.log(item + ' ' + list[i])} );
    }
    return result;
}
buildList([1,2,3,4,5]);

 function testList() {
     var fnlist = buildList([1,2,3,4,5]);
     for (var i = 0; i < fnlist.length; i++) {
       fnlist[i](i);
     }
 }
 testList();

```

I hope that this explanation of scope an closures helps. Play around with the patterns you see here, experiment. Actually
writting this article was difficult, but I gained a far deeper understanding than I when I started.

#### Resources
[YDKJS](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)
[dmitry soshnikov, Javascript: Core](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/#variable-object)
[ECMA 262.3](http://dmitrysoshnikov.com/ecmascript/chapter-2-variable-object/)
[StackOverflow](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)
[Nick Zakas](https://www.amazon.com/Professional-JavaScript-Developers-Nicholas-Zakas/dp/1118026691)


