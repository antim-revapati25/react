s, Route } from "react-router-dom";
import Contact from "./Contact";
import "./index.css";
const ReactRouter = () => {
  return (
    <>
      <Routes>
        <Route path="/contact/:id" element={<Contact />} />
      </Routes>
    </>
  );
};

export default ReactRouter;

Contact.jsx
import React from "react";
import { useParams, useLocation } from "react-router-dom";
const Contact = () => {
  const location = useLocation();

  console.log("location: ", location);
  const { id } = useParams();
  return (
    <div>
      <p>Contact us at: {id}</p>
      <p>location:{location.pathname}</p>
    </div>
  );
};

export default Contact;



1️⃣2️⃣ useMatch
1️⃣3️⃣ Search Params (?q=hello)
1️⃣4️⃣ Redirects using <Navigate />
1️⃣5️⃣ 404 NotFound route

🟧 ADVANCED LEVEL
1️⃣6️⃣ Nested routes (most important)
1️⃣7️⃣ Layout routes (<Outlet />)
1️⃣8️⃣ Protected Routes (authentication guard)
1️⃣9️⃣ Relative Routing
2️⃣0️⃣ Index Routes
2️⃣1️⃣ Catch-All routes
2️⃣2️⃣ Active link advanced styling
2️⃣3️⃣ Loader & Action (React Router Data API)
2️⃣4️⃣ Error boundaries (v6.4+)
2️⃣5️⃣ Code Splitting with lazy + Suspense





Transpiler
Error bounderies - left
Life Cycle of React components
  








Javascript
Event loop

After the global execution context (GEC) finished executing, call stack is empty and then if the timer of a callback funciton is finised then that callback will be inside the callback queue.
Event loop will keep checking for the function inside callback queue, if it is there then that callback function will be pushed to call stack by event loop for execution
Event loop job: to continuously monitor callback queue for function to execute
We need callback queue to better manage the callback functions, as there are lots of event listeners in our program
Microtask queue
Microtask queue has higher priority than callback queue.
Functions inside the microtask queue will be executed before function in callback queue.

In microtask queue: all the functions which comes from promises & mutation observer will goes inside microtask queue.
Mutaion observer keeps checking if there is some mutaion in the DOM tree, if yes then mutation observer will execute the callback function
Note: callback queue is also known as task queue.
Note: once all the task from microtask queue are finished executing then only callback queue will be given a chance for execution by event loop
Starvation of the task iunside the callback queue: when task inside the microtask queue generates more task then callback queue will never get change for execution
JS concurrency model: JavaScript is single-threaded (only one line of code runs at a time),
but
it achieves concurrency through the Event Loop, Task Queues, and Web APIs.

Important example:
async function test() {
  console.log("X");
  await null;
  console.log("Y");
}

console.log("Z");
test();
console.log("W");

// output
/*
prints Z: GEC code
test() : sees x, prints X
then await null is promise resolved, goes to microtask queue --> y is in microtask queue
prints W
prints Y as GEC finished
*/

1. Run entire synchronous GEC (global code)
Everything not async executes here.

2. Run all microtasks (Promise callbacks, async/await continuations, queueMicrotask, MutationObserver)
✔ Microtasks run before ANY macrotask
✔ Microtasks run until queue becomes empty

3. Run next macrotask (setTimeout, setInterval, DOM events, I/O)
✔ Only ONE macrotask runs per cycle
✔ After it finishes → go back to microtasks again

Render/update UI (optional step depending on browser)
Antoher important example
console.log("A");

setTimeout(() => console.log("B"), 0);

fetch("https://jsonplaceholder.typicode.com/todos/1").then(() =>
  console.log("C")
);

Promise.resolve().then(() => console.log("D")); //promise.resolve runs before fetch.then

console.log("E");

/*
A E D C B
*/



JS engine
Inside the JS runtime we have JS engine. Inside js runtime environment we have eveythiung that is required to run JS code
Chrome has V8, firefox has spidermonkey (first js engine, was evolved to this)
V8 was written in c++
Js engine takes high level code and convert it into machine level code for execution
Code  parsing   compilation  execution
Parsing : code is broken down into tokens. Syntax parser takes code and convert it into AST(abstract Syntax Tree) 
Compilation: JIT compilation (Just in time comiler). 
Interpreter: code is executed line by line. Code is fast executed.
Compiler: code is compiled first and then the optimized/compiled code runs. We have more efficiency
JS can behave both interpreted and compiled using JIT
AST goes to interpreter and the code is executed. While interpreter going to compiler for optimization. Here compiler will compiler the hot code (frequently used code) and store its compiler version byte code for faster execution next time
AOT: ahead of time compilation. It takes code that is going to be executed next time and optimize it as creates byte code
Execution: compilation and execution happens parallel. Memory heap and call stack plays an important role in execution.
We have automatic garbage collector inside the js which uses mark and sweep algorithm.


V8 has : ignition interpreter and turbo fan optimizing compiler





Set time out
Set time out callback will be executed only after the GEC has finished executing the entire code even though we have 0 sec timer. 


Higher Order Function (HOC)
A function that takes a function and returns another function is known as higher order function

const radius = [1, 2, 3, 4];

const caculateArea = function (radius) {
  let area = [];
  for (let i = 0; i < radius.length; i++) {
    area.push(Math.PI * radius[i] * radius[i]);
  }
  return area;
};
console.log(caculateArea(radius));

/*
show answer upto 2 decimals only
toFixed returns string, to convert it into number we used Number

area.push(Number(( Math.PI*radius[i]*radius[i]).toFixed(2)));
*/

// concept: extract the repeated logic and put it inside the single code like calculate

const area = function (radii) {
  return Math.PI * radii * radii;
};

const circumference = function (radii) {
  return 2 * Math.PI * radii;
};

const calculate = function (radius, logic) {
  const result = [];
  for (let i = 0; i < radius.length; i++) {
    result.push(logic(radius[i]));
  }
  return result;
};

console.log(calculate(radius, circumference));


this is kind of implementation of own map
console.log(radius.map((area)));

map filter reduce
these are HOF
Map
Map takes a callback function and calls that function for every item in array (in which map called on)
Note: don’t forget to return value from map else it will be undefined. No need to return if you are using arrow function and having only one line, just don’t use curly braces there

const arr = [1, 2, 3, 4, 5];

const double = function (item) {
  return item * 2;
};

// takes callback function and runs that callback function for every element in arr array
const output = arr.map(double);
console.log(output);

const tripple = arr.map((item)=> item*item*item);
console.log(tripple)

const twice = arr.map((item)=>{
    return 2*item;
})
console.log(twice)

Filter
Used to filter values in an array based on callback function /logic
function isOdd (item){
    return item%2!==0;
}

const odd = arr.filter(isOdd);
console.log(odd)

Reduce
Used to accumulate array values. 
Reduce takes all the array values and return single value based on some logic/callback function
The callback function in reduce takes two parameters – one is accumulator, and current array value
The reduce takes two parameters – callback function/logic and initail value of accumulator, defualt is 0


// without reduce
const sum = function (item) {
  let total = 0;
  for (let i = 0; i < item.length; i++) {
    total += item[i];
  }
  return total;
};
console.log(sum(arr));

// with reduce - reduce takes a callback function(which takes two parameters - accumulator(which holds total) and currect value of array ) and a intial value

let findSum = arr.reduce((accumulator, curr) => {
  return accumulator + curr;
}, 10);

// note: here 10 is the initial value of accumulator, default is 0. so now sum will start from 10
console.log(findSum);

 



Important example for revision: imp
const arr = [1, 2, 3, 4, 5];

// map
/*

const double = function (item) {
  return item * 2;
};

// takes callback function and runs that callback function for every element in arr array
const output = arr.map(double);
console.log(output);

const tripple = arr.map((item)=> item*item*item);
console.log(tripple)

const twice = arr.map((item)=>{
    return 2*item;
})
console.log(twice)

*/

// filter

/*
function isOdd (item){
    return item%2!==0;
}

const odd = arr.filter(isOdd);
console.log(odd)
*/

// reduce

/*
// without reduce
const sum = function (item) {
  let total = 0;
  for (let i = 0; i < item.length; i++) {
    total += item[i];
  }
  return total;
};
console.log(sum(arr));

// with reduce - reduce takes a callback function(which takes two parameters - accumulator(which holds total) and currect value of array ) and a intial value

let findSum = arr.reduce((accumulator, curr) => {
  return accumulator + curr;
}, 10);

// note: here 10 is the initial value of accumulator, default is 0. so now sum will start from 10
console.log(findSum);

// finding max using reduce
let max = arr.reduce((acc, curr) => {
  if (curr > acc) {
    acc = curr;
  }
  return acc;
}, 0);

console.log(max);
*/

// find list of full names
const users = [
  { firstName: "abc", lastName: "xyz", age: 26 },
  { firstName: "donald", lastName: "trump", age: 75 },
  { firstName: "elon", lastName: "musk", age: 50 },
  { firstName: "deepika", lastName: "padukone", age: 26 },
];

// list of full names
const full_name = users.map((item) => {
  return item.firstName + " " + item.lastName;
});

console.log(full_name);

// list of ages by group
const ages = users.reduce((acc, curr) => {

    // age already present
    if (acc[curr.age]) {
      acc[curr.age] = acc[curr.age] + 1;
    } else {
      // insert the age with count 1
      acc[curr.age] = 1;
    }

  return acc;
}, {});

console.log(ages);

// list of first names whose age is <30

const minor = users
  .filter((item) => item.age < 30)
  .map((item) => item.firstName);

console.log(minor);

// or use using reduce

// polyfills of map/filter/reduce - what is this, how this works



Lexical environmnet
It is the local memory along with refernce to its parents memory/variables & functions
Lexical means hierarcy/in a sequence
Scope chain: the way of finding variables in local environment and then in parent’s lexical environment.

Hoisting
Hoisting means JavaScript moves variable and function declarations to memory before the code starts executing.
JavaScript scans your code
Creates memory space for variables & functions
BEFORE the execution line starts running

Let/Const/Var
var
Hoisted
Memory allocated
Initialized with undefined
Accessible before declaration (but value is undefined)
Has global/functional scope
let & const
Hoisted
Memory allocated
NOT initialized
Stay in TDZ until the line where they are declared
Accessing before declaration → ReferenceError
Has block scope
TDZ: temporal dead zone: let & const stays in TDZ until they are initialized with some value.
The time between hoisting and actual initialization of let/const variables.
Note: in case of syntax error code will not get executed at all.
In case of let and const, you cannot reclare. It will give syntax error
Also for const you cannot reinitialze the variable
But for var you can redeclare and reinitialize both

Errors
Syntax error: Syntax Error occurs when the code breaks JavaScript grammar rules, so the JS engine cannot parse it.
It happens before execution starts.
Reference error: ReferenceError occurs when you try to access a variable that exists in the scope chain but is not accessible yet (e.g., TDZ), or it does not exist at all.
Case 1: Variable not declared
console.log(x); // ❌ ReferenceError: x is not defined
Case 2: let / const in TDZ
console.log(a); // ❌ ReferenceError (TDZ)
let a = 10;

Type error: TypeError occurs when the value is valid, but the operation you are performing on that value is not allowed
❌ Reassigning const
const a = 10;
a = 20; // ❌ TypeError: Assignment to constant variable
❌ Calling a non-function
let x = 5;
x(); // ❌ TypeError: x is not a function
❌ Accessing property of null/undefined
let z = null;
z.toString(); // ❌ TypeError: Cannot read property...

Block in js
{} this is known as block 
Blocks allow us to group multiple statements where only one statement is expected.
Example: if, for, while, function, etc. 
Just like in if(){} 
Block scope follows lexical scope.
Let & const have block scope.
Var – not block scoped, only function scoped
Shadowing
Shadowing occurs when an inner variable has the same name as an outer variable.
Inside the block:
the inner variable hides (shadows) the outer variable
outer variable is still present but not accessible inside the block

✅ Shadowing Behavior
✔ 1. var shadowing
var inside a block is NOT block-scoped →
It reassigns the outer variable (because both refer to the same memory).
var a = 10;

{
  var a = 20;  // same memory → overwrites outer
}

console.log(a); // 20
✔ This is allowed
✔ This is normal behavior
⚠ Not recommended in real code

✔ 2. let/const shadowing
Inner let or const creates a new block-scoped variable,
so they do NOT overwrite the outer variable.
let a = 10;

{
  let a = 20;  // new variable → block scope only
  console.log(a); // 20
}

console.log(a); // 10

❌ Illegal Shadowing
This happens when the inner variable breaks scoping rules.
Your example:
let a = 10;
{
  var a = 20; // ❌ illegal shadowing
}
❗ Why illegal?
let is block scoped (inner scope)
var is function/global scoped
So var a tries to redeclare the same name in the outer/global scope
→ JS throws SyntaxError

✅ Legal Shadowing
var a = 10;  
{
  let a = 20;  // ✔ legal shadowing
}
✔ Why legal?
var a exists in outer scope
let a exists in block scope
No overlap → so legal
Shadowing behaves same in function and block scope
There are some illegal shadowing also
You cant do 
Let a = 10;
{
Var  a =20 //illegal – bcz var has global scope and it is crossing its boundary, so it will interfere with it
}
But you can do this
var a = 10;
{
let  a =20 //illegal – bcz let is in its boundary, block scope so it wont interfere with var
}

Confustion between [], dot and “” – in object variable accessing
const arr = [1, 2, 3, 4, 5, 1, 2, 4, 2, 1, 2, 5, 8];
const ans = {};

for (let i = 0; i < arr.length; i++) {
  let curr = arr[i];
  if (ans[curr]) {
    ans[curr] = ans[curr] + 1;
  } else {
    ans[curr] = 1;
  }
}
/*
use [] in all cases. but specially when you dont know if the current key is there or not

bcz using obj.key will give error if key is not there in obj

also use obj[key] or obje["key"] are same

obj["key"]: if key has space or number
*/




Closures
A closure is formed when a function remembers its lexical scope even when the function is executed outside that scope.
Closure = function + its lexical environment (variables from parent function).
function x() {
  let a = 1;
  function y(){
    console.log(a);
  }
  return y;
}
let z = x(); //function along with its lexical scope is returned
console.log(z)

z();


another example
function x() {
  let a = 1;
  function y(){
    console.log(a);
  }
  a =100; //updated in memory
  return y;
}
let z = x(); //function along with its lexical scope is returned
console.log(z)

z(); //100 - since reference of a was stored so it will get updated value, i.e.100

Because the inner function captures the reference to variables, not the value


Examples of closures and set time out

function x() {
  var i = 1;
  setTimeout(function () {
    console.log(i);
    console.log("one");
  }, 3000);
  console.log("two");
}
x();
// output: two  1  one


Important  //printing 1 to 5 after each seconds
function x() {
  console.log("one");
  for (var i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), i * 100);
  }
  console.log("two");
}
x();
/* output
one
two
5
5
5
5
5
*/


Note: you will get 5 s after 100, 200, 300, 400, 500 ms not after 500ms all the 5 will apprear at once bcz delay is decided at the moment setTimeout is registered, Not when the callback runs
But just using let instead of var, things changes.
Bcz in case of var, they are refering to the same memory space and we know set time out runs only aftet the GEC finished its code. 
So at the end for loop has ended runing and now the value of i is 5 at the end and at last set time out will print the same value of i that is 5

But in case of let, a new block scope of i is created with the updated value. So now each callback method has closure of updated i 
function x() {
  console.log("one");
  for (let i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), i * 100);
  }
  console.log("two");
}
x();
/* output
one
two
0
1
2
3
4
*/


Or we can use closures to achieve the same
We are passing a new copy of variable each time to the function.
In this way we are passing i to the function which is stored in x and has separate copy 
function x() {
  console.log("one");
  for (var i = 0; i < 5; i++) {
    function close(x) {
      setTimeout(() => console.log(x), x * 100);
    }
    close(i);
  }
  console.log("two");
}
x();
/* output
one
two
0
1
2
3
4
*/


Summary
✔ Why var prints 5 five times:
var is function scoped, so the same i is shared
loop completes before timeouts fire
final value of i = 5
all callbacks print the same shared value

✔ Why timings are different:
Each setTimeout is registered with a delay of i * 100
delay comes from old i value
callback prints new i value

✔ Why let works:
let is block scoped
Each iteration gets a new copy of i
Each callback closes over its own copy

✔ Closure fix (IIFE/extra function):
manually creates a new variable (x)
each callback closes over its own x
works even with var

Exmaple same as setTimeout with normal functions
var arr = [];

for (var i = 0; i < 3; i++) {
  arr[i] = function () {
    console.log(i);
  };
}

arr[0](); //3 --> as var has function scope as all i refers to the same updated value that is 3
arr[1](); //3
arr[2](); //3


same can be fixed using let as it has block scope

Closure: a function along with reference of its outer environment. Function + lexical scope
Each function has access to its outer variables, so even when the function is executed in other scope then also it will have the access to its original lexical variable
Closure = Function + Its Lexical Environment (variables it can access).
A closure forms when:
A function is defined inside another function
It remembers the variables from its outer function
Even after the outer function has finished execution
And even if the inner function is executed in a different scope
Example:
function outer(){
    function inner(){
        console.log(x);
    }
    let x =10;  // even though let has block scope, it is accessible outside bcz of clousers. 
    // x can be declared anywhere, just it has to be above return statement
    return inner;
}

outer()(); //curying //10

closure with currying

function outest() {
  function outer(val) {
    function inner() {
      console.log(x, val);
    }
    let x = 10;
    return inner;
  }
  return outer;
}

// passing argument to second level function
outest()("value")(); //10 value


change in values of x – game of scopes
function outest() {
  function outer(val) {
    function inner() {
      console.log(x, val);
    }
    let x = 10;
    return inner;
  }
  return outer;
}

let x = 100; 
outest()("value")(); //10 value- even after x=100, as it is new variable to the function, outest function has copy of refernce of inner variable and it will refer to that only


but if x is not present in inner scope then:
function outest() {
  function outer(val) {
    function inner() {
      console.log(x, val);
    }
    // let x = 10;
    return inner;
  }
  return outer;
}

let x = 100; 
outest()("value")(); //100, as the x was not found in the inner scope, JS will look for x at outer scope

using closures to implement encapsulation
//just a mistake you might do
function counter() {
  let count = 0;
  function increment() {
    count++;
  }
  console.log(count); // value is getting initalized, as clg is after function, outisde
  return increment;
}

let counterfun = counter(); //0 as the value is getting initalized
counterfun(); 
counterfun();
counterfun();


//real example of encapsulation
function counter() {
  let count = 0;
  function increment() {
    count++;
    console.log(count); // value is incrementing inside
  }
  return increment;
}

let counterfun = counter(); 
counterfun();//1 -- as you are executing increment function repeatedly
counterfun();//2
counterfun();//3


we can create mulitple counters using the same code
function counter() {
  let count = 0;
  function increment() {
    count++;
    console.log(count); // value is incrementing inside
  }
  return increment;
}

let counterfun = counter();
counterfun(); //1 -- as you are executing increment function repeatedly
counterfun(); //2
counterfun(); //3

let counterfun2 = counter(); // a new counter is created, unaffected by above counter
counterfun2(); //1
counterfun2(); //2


using constructors we can make this scalable
function counter() {
  let count = 0;
  this.increment = function () {
    count++;
    console.log(count);
  };
  this.decrement = function () {
    count--;
    console.log(count);
  };
}

let counterfun = new counter();
counterfun.increment(); //1
counterfun.increment();//2
counterfun.decrement();//1

Important interview closure output question
function createIncrement() {
  let count = 0;

  function increment() {
    count++;
  }

  let message = `Count is ${count}`;

  function log() {
    console.log(message);
  }

  return [increment, log];
}

const [inc, log] = createIncrement();

inc();
inc();
inc();

log(); //0 --> as the message was created at the time of execution and later we update count but message is not recreated, even though count is 3


most important interview trap
function setup() {
  let data = { value: 1 };

  return {
    inc() {
      data.value++;
    },
    replace() {
      data = { value: 100 };
    },
    print() {
      console.log(data.value);
    },
  };
}

const obj = setup();

obj.inc(); // data.value = 2
obj.print(); // output 2

obj.replace(); // data replaced with new object --> value: 100

obj.inc(); // data.value = 100+1 ==101
obj.print(); // output 101


some important traps points to remember for closures

Advantages of Closures (Point Form, Interview-Ready)
1. Data Privacy
Enables creation of private variables
Protects data from being accessed or modified directly
Helps simulate “private” fields like in OOP
2. Persistent State
Function can remember values between calls
Maintains internal state even after outer function finishes
3. Encapsulation & Abstraction
Exposes only necessary functionality
Hides internal logic and implementation details
Supports clean modular design
4. Module Pattern
Helps structure large programs
Avoids polluting the global scope
Allows creation of public and private APIs
5. Function Factories
Allows creation of functions with preset data
Supports generating multiple customized functions
6. Useful in Asynchronous Programming
Callbacks, promises, and timers can remember outer variables
Necessary for maintaining state in async flows
7. Supports Currying
Closures allow splitting a function into smaller chained functions
Enables advanced functional programming patterns
8. Enables Memoization & Caching
Remembering previous inputs
Improves performance by storing computed results
9. Foundation of Higher-Order Functions
Used heavily in frameworks like React (hooks)
Disadvantages: overconsumption of memory, garbage collection issue as clousure variables are not garbage collected till program expires, can lead to memory leak, browser freez as the memory is accumulated

Garbage collector
Garbage Collection (GC) is a process in the JavaScript engine that automatically frees memory by removing variables and objects that are no longer reachable or used by the program.
JS uses mark and sweep algorithm for garbage collection
But since closures remembers the reference of its lexical environment variables, they cant be garbage collected
function a() {
  var a = 10,
    b = 20;
  return function () {
    console.log(a);
  };
}

var y = a();
y(); //10
console.log(a);//accesible: [Function: a]
console.log(b); //not accessible, garbage collected: ReferenceError: b is not defined

// b is garbage collected as b was not used anywhere 
// since a was used inside the function so is was not garbage collected


First class & anonymous functions
Function statement/declaration and function expression
a(); // function statement is hoisted and can be accessed before declaration

b(); //error -> hoisted based on data type, cant access here. b is variable here not a fuction

function a() {
  console.log("function statement/declaration");
}

let b = function () {
  console.log("function expression");
};


Anonymous functions
// anonymous function -- function without a name
// anonymous functions are used when function are used as a values

function (){
    console.log("anonymous function")
}

Named function expressiong
// named function expression

var a = function fun() {
  console.log("named fun expression", fun); //fun can be accessed inside
};

a(); // valid --> named fun expression [Function: fun]
fun(); // error, fun is not defined, cant access outside


parameter vs arguments
function fun(param){
    console.log(param);
}

fun("argument");

First class functions/citizens
The ability of functions to be used as values, to be passed from another function
we can pass a function to function , return a function 

callback function: 
when we pass a function to another function
they gives access to asynchronous world
we give responsibility to another program to call callback function
setTimeout(fun,1000); //calling fun using async programming

function fun(){
    console.log("callback function")
}

We should not block the main thread, i.e, use aync programming to execute task that takes time
Event listeners are heavy, they forms closures. Garbage collector cannot remove event listeners so we need to remove event listeners when we are not using them





Async programming
Callback hell/pyramid of doom: when there are nested callbacks inside each other.
Inversion of control: we as a developer lose control over program as we are using callbacks. We don’t know if our functions will be called or not, how many times they will be called, what if there are some errors while calling the callback functions. 
Promises
A promise is an object representing the eventual completion or failure of an asynchronous operation.
We attach the callback function with promises.
const cart = ["a", "b", "c"];

// passing the callback function
createOrder(cart, function (orderId) {
  proceedToPayment(orderId);
});

// attaching the callback function

const promise = createOrder(cart);

promise.then(function (orderId) {
  proceedToPayment(orderId);
});


Promises gives us the guarantee that our callback function will be called definetly and only once.
Promise guarantees that it will be resolved only once.
const GITHUB_API = "https://api.github.com/users/mojombo";

const user = fetch(GITHUB_API);

console.log(user);//fetch returns promise -- it is pending right now

promise has 3 states: pending, fulfilled(resolved), rejected
A JavaScript Promise has 3 States
1️ Pending
Initial state
Promise is still running
The async operation hasn’t finished yet
Neither fulfilled nor rejected

2️ Fulfilled (Resolved)
Operation completed successfully
Promise now has a value
.then() handlers will run

3️ Rejected
Operation failed
Promise now has a reason (error)
.catch() handlers will run
After a promise is settled (fulfilled or rejected), its state cannot change, it becomes immutable.
Creating the promise
We create promise using promise constructor.
Promise takes a callback function. Which has two parameters, resolve and reject.
We can resolve and reject promise only once.
we attach callback functions with promise.
// const cart = ["a","b","c"];
const cart = [];

// accepts cart and returns a promise
function createOrder(cart) {
  // creating the promise
  const pr = new Promise((resolve, reject) => {
    if (cart.length == 0) {
      const err = new Error("emtpy cart");
      reject(err);
    } else {
      const msg = "success";
      resolve(msg);
    }
  });

  return pr;
}

// consuming the promise
createOrder(cart)
  .then((data) => console.log(data))
  .catch((err) => console.log("promise fail: ", err));



Promise chain
Note: don’t forget to return from a promise chain to consume it to next level in hierarchy
const cart = ["a", "b", "c"];
// const cart = [];

// accepts cart and returns a promise
function createOrder(cart) {
  // creating the promise
  const pr = new Promise((resolve, reject) => {
    if (cart.length == 0) {
      reject(new Error("emtpy cart"));
    } else {
      resolve("success");
    }
  });

  return pr;
}

// consuming the promise -promise chaining
createOrder(cart)
  .then((data) => {
    console.log(data);
    const newPr = new Promise((resolve,reject)=>{
        if(true) resolve("level 1 resolved");
        else reject("level 1 reject")
    });
    return newPr
  })
  .then((data)=>console.log(data))
  .catch((err) => console.log("promise fail: ", err));


Catch will handle error at any level of chain, if the promise fails at the level 1 then also the .catch will be called and it will handle error.
We can have mulitple .catch methods. Each .catch will be responsible to handle errors above them. And .then after .catch will be executed normally
Promise API
Promise.all – fail fast
1️ Takes an array of promises
	Promise.all([p1, p2, p3])

2️ Runs all promises in parallel
p1 → finishes in 3s
p2 → fi