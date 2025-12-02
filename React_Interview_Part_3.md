nishes in 1s
p3 → finishes in 2s
3️ Waits for ALL of them to finish
Final result comes after the slowest promise (here: 3 seconds)
4️ If all resolve → returns an array of their results
Output is:
[val1, val2, val3]
➡️ Order is the same as input,
even though p2 finished first.
5️ If ANY one promise rejects → whole Promise.all rejects
Even if other promises succeeded.

🌟 Small Example 
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
const p2 = new Promise(res => setTimeout(() => res("B"), 1000));
const p3 = new Promise(res => setTimeout(() => res("C"), 2000));

Promise.all([p1, p2, p3]).then(result => {
  console.log(result); // ["A", "B", "C"]  → after 3s
});



As soon as the first promise gets rejected, the whole promise.all throws the error object containing the error
Even though Promise.all stops caring about them
→ p1 and p3 will still continue running in the background
BUT their results are ignored.
JavaScript has no built-in promise cancelation.
Promise.allSetteled
Waits for all promises to get settled (resolve or reject) then only it shows output array containing result of all the promises.

1️⃣ Takes an array of promises
Promise.allSettled([p1, p2, p3])
2️⃣ Waits for ALL promises to settle
"Settled" means:
fulfilled ✔
or rejected ❌
It does not stop on error.

3️⃣ It always waits for the SLOWEST promise
If:
p1 → 3 sec
p2 → 1 sec
p3 → 2 sec
Final output comes after 3 seconds.

4️⃣ Returns an array of result objects
Each item tells:
status: "fulfilled" or "rejected"
value (for fulfilled)
reason (for rejected)

🌟 Output format (very important for interviews)
[
  { status: "fulfilled", value: val1 },
  { status: "rejected",  reason: err2 },
  { status: "fulfilled", value: val3 }
]
➡️ Unlike Promise.all, it never fails, even if some promises reject.

⭐ Short Example (simple)
Promise.allSettled([
  Promise.resolve("A"),
  Promise.reject("Error"),
  Promise.resolve("C")
]).then(console.log);
Output:
[
  { status: "fulfilled", value: "A" },
  { status: "rejected", reason: "Error" },
  { status: "fulfilled", value: "C" }
]

Promise.race
Whoever settled first will be the winner. It gives result of first settled (resolve, reject )promise

1️⃣ Takes an array of promises
Promise.race([p1, p2, p3])
2️⃣ Returns the result of the FIRST promise that settles
"Settles" = either resolves OR rejects.
Not the fastest positive result —
the fastest any result.

Your diagram example
p1 → resolves in 3s
p2 → resolves in 5s
p3 → rejects in 2s
🔥 The FIRST promise to settle is p3 in 2 seconds, and it fails.
So Promise.race:
✔ Rejects immediately (after 2s)
✔ With the reason from p3
❌ Does NOT wait for p1 or p2

⭐ Interview One-Liner
Promise.race returns whichever promise settles first—whether it's a resolve or a reject. The first completed promise wins the race.

🌟 Short Example (clean)
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
const p2 = new Promise(res => setTimeout(() => res("B"), 5000));
const p3 = new Promise((res, rej) => setTimeout(() => rej("Error from p3"), 2000));

Promise.race([p1, p2, p3])
  .then(console.log)
  .catch(console.error);
Output (after 2 seconds):
Error from p3

Promise.any
Waits for first success promise

If all the promises get rejected then the final result will be aggregated list of all error
1️⃣ Takes an array of promises
Promise.any([p1, p2, p3])
2️⃣ Waits for the first successful (fulfilled) promise
It ignores rejections unless all promises reject.

Your diagram example
p1 → resolves in 3s
p2 → rejects in 1s
p3 → resolves in 2s
🔥 The first success happens at 2 seconds → p3
So the final output is:
val3
p2’s error doesn't matter because Promise.any is only looking for the first success.

⭐ What happens if ALL promises fail?
Then Promise.any rejects with a special error:
AggregateError: All promises were rejected

🌟 Short Example (clean)
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
const p2 = new Promise((res, rej) => setTimeout(() => rej("Fail"), 1000));
const p3 = new Promise(res => setTimeout(() => res("C"), 2000));

Promise.any([p1, p2, p3])
  .then(console.log)   // "C"
  .catch(console.error);
Output (after 2 seconds):
C

🎯 Interview One-liner
Promise.any returns the first fulfilled promise and ignores all rejections. It only rejects if all promises fail (AggregateError).
Note: settled means either resolved or rejected

Summary

all → wait for all, fail fast
allSettled → wait for all, never fail
race → first finished wins (success or error)
any → first success wins
resolve → make resolved promise
reject → make rejected promise
finally → cleanup after everything

Async/Await
Async always returns a promise. If we don’t return a promise then asynch wraps the value inside the promise and returns it.
Await is used inside the async only.
Note: we can handle erorrs using try catch inside async – await 
//returns a promise
async function getData() {
  return "Hello";
}

// resolving the promise
const data = getData().then((val) => console.log(val));


difference between promise and async await
const p = new Promise((resolve, reject) => {
  if (true) {
    resolve("resolved");
  } else reject("rejected");
});

function getData() {
  p.then((data) => console.log(data));
}
getData();

async function handlePromise() {
  // instead of p.then ==> await p;, it will return the settled value
  const data = await p;
  console.log(data);
}
handlePromise();


await will hold the execution untill the promise is settled. While normal promise can’t do that.
const p = new Promise((resolve, reject) => {
  if (true) {
    resolve("resolved");
  } else reject("rejected");
});

function getData() {
  p.then((data) => console.log(data));
  console.log("after code"); //cant hold execution
}
// getData();
/*output
after code -- cant hold execution
resolved
*/

async function handlePromise() {
  // instead of p.then ==> await p;, it will return the settled value
  const data = await p;
  console.log(data);
  console.log("after async code"); //-- hold execution
}
handlePromise();
/*output
resolved  
after async code -- hold execution
*/


Set time out confusion - important
If we have multiple set time outs, then they will be registered at the time when the function is executing, then they both will wait for 1000ms, and as there timer is same they will get executed by event loop from callback/macrotask  queue to callstack.
const p = new Promise((resolve, reject) => {
  if (true) {
    setTimeout(() => {
      resolve("resolved");
    }, 1000);
  } else reject("rejected");
});

async function handlePromise() {
  const data = await p;
  console.log(data);
  console.log("one"); //-- hold execution

  const data2 = await p;
  console.log(data);
  console.log("two"); //-- hold execution
}

handlePromise();
/*output after 1000ms - all poped at the same time
resolved
one
resolved
two

*/


One more example with different timers:
If data is holding promise p1 having timer of 5000 ms and 
Data2 is holding promise p2 having timer of 1000ms
Then aslo p1 will executes first even though the timer of p2 expires as the await keyword holds the execution untill the current promise gets settled
Behind the scene how things are working
JS is a synchornous, single threaded language. It executes code line by line.
As the JS encounters await keyword it don’t freeze the js execution. It moves the function out of the execution context.  Once the promise is setteled then that function pushed back to execution context and start execution from where it left.
await does not block the single thread. It only pauses the async function, allowing JS to continue doing other work like running timers.
Timers are registered as the promise is created not when await settles the promise
Fetch
Fetch returns a promise, we need to wait for that promise to get settled, using await
Fetch returns a response which is a readable stream. Store that result in data variable
We need to convert that readable stream into JSON
That JSON is also returns a promise, we need to settle that also, store that in response variable
Fetch()  response.JSON  jsonValue
In GPT terms
1️ fetch() returns a Promise
It starts an HTTP request in the background
We need to await the promise to get the response
2️ The resolved value of fetch() is a Response object
This response is NOT JSON yet
It is a readable stream (data coming in chunks)
3️ To read the body, we convert the stream to JSON
Using .json() method on the response
This method also returns a Promise
4️ We await that promise to get actual JSON data
fetch()  → returns Promise → resolves to Response object  
Response.json() → returns Promise → resolves to actual JSON data  
const GITHUB_API = "https://api.github.com/users/mojombo";

// fetch returns a promise
const data = await fetch(GITHUB_API); //returns readble stream
const response = await data.json(); // convert that readable stream using JSON, which also returns promise

console.log(response);



using fetch direclty using promise chain
// or you can do this also

fetch(GITHUB_API)
  .then((res) => res.json())
  .then((response) => console.log(response));



Error handling in async await
Using try, catch
const GITHUB_API = "https://api.github.com/users/mojombo";

async function getData() {
  try {
    const data = await fetch(GITHUB_API);
    const response = await data.json();
    console.log(response);
  } catch (err) {
    console.log("got some error: ", err);
  }
}

getData();


or we can use .catch as async function returns promise
const GITHUB_API = "https://api.github.com/users/mojombo";

async function getData() {
  const data = await fetch(GITHUB_API);
  const response = await data.json();
  console.log(response);
}

getData().catch((err) => console.log("got some error: ", err));

 note: we can use await without the async keyword in some cases at top level of module
Normally:
await is ONLY allowed inside an async function
Using it outside causes a SyntaxError
BUT modern JS (ES2022+) added something called:
⭐ Top-Level Await (TLA)
Which means:
You CAN use await at the top level of a module file (in ES modules).
 Note: async – await are just syntactic sugar over then and catch.
This keyword
In global object: 
will have global object. Inside browser, global object is window, inside node js it is global
Inside the normal function: 
In non strict mode: it is global object
In strict mode: it is undefined
This substitution: if the value of this is undefined or null then this keyword will be replaced by global object. This substitution is happens only in non-strict mode
The value of this depends on how the function is called
Fun(): valye of this is undefined, in strict mode
Window.fun(): value of this is global object
In non-strict mode this substitution happens
Inside the object
In methods (function inside the object), the value of this becomes the object itself
Call, apply and bind
call
const student = {
  name: "abc",
  printName: function () {
    console.log(this.name);
  },
};

student.printName();

const person = {
  name: "xyz",
};

// the value of this becomes person inside student object
student.printName.call(person);


apply and bind left
inside arrow function
arrow functions don’t have there own this binding. They take the value of this from there lexical environment/parent environment
this inside the HTML:
referene to HTML element
 will be the html button if it is inside the button tag
Call/Apply/Bind
Note: arrow function ignores call/apply/bind bcz they take this from lexical scope

This: the object that calls the function
const user = {
  name: "abc",
  sayHi() {
    console.log(this.name);
    console.log(this); //{ name: 'abc', sayHi: [Function: sayHi] }
  },
};

user.sayHi(); //abc


why we need call/apply/bind?
Sometimes we want manually decide what ‘this’ should be.
function show() {
  console.log(this);
}

show(); //global obj

const obj = {
  name: "abc",
};

show.call(obj);//{name:"abc"}


call
Calls function immediately
Sets this to given obj
Passes argument individually
function greet(a, b) {
  console.log(this.name, a, b);
}

const obj = { name: "abc" };
greet.call(obj, 5, 10); //abc 5 10


Apply
Calls function immediately
Sets this to given obj
Passes arguments as an array
function greet(a, b) {
  console.log(this.name, a, b);
}

const obj = { name: "abc" };
greet.apply(obj, [5, 10]); //abc 5 10


note: passing individual elements gives type error
Bind
Does not calls function immeditely
Returns a new function with ‘this’ permanent binding
Takes argument individually
Useful in callbacks and event handlers
function greet(a, b) {
  console.log(this.name, a, b);
}

const obj = { name: "abc" };
const fn = greet.bind(obj, 5, 10); //abc 5 10
fn(); //abc 5 10

note: passing arguments as array will not give error, it will be treated as single argument then
function greet(a, b) {
  console.log(this.name, a, b);
}

const obj = { name: "abc" };
const fn = greet.bind(obj, [5, 10]);  //[5,10] will be treated as single argument
fn(); //abc [ 5, 10 ] undefined

Note: arrow function ignores call/apply/bind bcz they take this from lexical scope
greet = (a, b) => {
  console.log(this.name, a, b); //undefined 5 10
  console.log(this); //{}
};

const obj = { name: "abc" };
const fn = greet.bind(obj, 5, 10);
fn(); //undefined 5 10 as this is module/global obj here

console.log("this: ", this); //{}


another example
const user = {
  name: "Leo",
  show: () => {
    console.log(this);
  },
};

user.show.call({ name: "Not Leo" }); //{}


very important interview traps
Loosing ‘this’ 
const user = {
  name: "Leo",
  show() {
    console.log(this.name);
  },
};

const ref = user.show;
console.log(ref); //[Function: show] – ref is just a function now, no obj calls it
ref(); // undefined or error


Ref is storing the whole function, but here we looses this binding
So when we call the function using ref(), we have no this binding and this is now global/module obj {} in case of node, so we get undefined.
Fix this: using call/apply/bind
const user = {
  name: "Leo",
  show() {
    console.log(this.name);
  },
};

const ref = user.show;
ref.call(user); // leo


Call/bind inside setTimeOut: loosing ‘this’ context
const obj = {
  name: "Leo",
  hi() {
    setTimeout(this.say, 0); //this.say is passed as callback function, loosing this context
  },
  say() {
    console.log(this.name);
  },
};

obj.hi(); //undefined


fix: using bind (as it binds this and returns a function)
const obj = {
  name: "Leo",
  hi() {
    setTimeout(this.say.bind(obj), 0); //this.say is passed as callback function, loosing this context, and bind returns a function
  },
  say() {
    console.log(this.name);
  },
};

obj.hi(); //Leo


note: this.say.bind(obj) and this.say.bind(this) both are same here
antoher example: 
var name = "Global";
const obj = {
  name: "Leo",
  say: function () {
    console.log(this.name);
  },
};

setTimeout(obj.say, 0); //undefined --> as obj.say is passed as callback function loosing its this context


fix: use bind to bind the this context with obj and return the function with preserve this context binding
setTimeout(obj.say.bind(obj), 0); //Leo

antother question
function f() {
  console.log(this);
}
f.call(null);


In non-strict → global object
In strict → this = null
Data types in JS
8 data types
Number
String
Boolean
Null
Undefined
Symbol
bigInt1
object

Spread vs Rest operator
Spread : unpacking
Rest: packing, collecting
Remember one rule: top level coppied by value, deep level copied by reference
Spread operator
const a = [{ x: 1 }];
const b = [...a]; //b = [{x:1}]

b[0].x = 99;
console.log(a); //[{ x: 99 }]; --> shallow coppy, nested level coppied by reference
console.log(b); //[{ x: 99 }]; --> shallow coppy, nested level coppied by reference


spread with non-premitive
const a =1;
const b = "hello";
console.log(...b); // h e l l 0
console.log(...a); //cant spread non-premitives except string

spread with function
function add(a, b, c) {
  return a + b + c;
}
const nums = [1, 2, 3];
console.log(add(...nums));//spread at argument level


rest with functions
// rest operator -- parameter level --> collecting all the values passed
function add(...rest) {
  return rest.reduce((item,acc)=>item +=acc);
}
const nums = [1, 2, 3];
console.log(add(1,2,3,4,5));


shallow copy : copied by value at first level, changing one cant change another
const a = {name:"user", age:22};
const b ={...a};

// shallow copy at first level
b.name = "newUser";
console.log(a);//{ name: 'user', age: 22 }
console.log(b);//{ name: 'newUser', age: 22 }

shallow copy: copied by reference at nested level, changing one leads to change another
const a = {name:"user", age:22, val:[1,2,3]};
const b ={...a};

// shallow copy at first level
b.name = "newUser";
b.val[0] = 100;
console.log(a);//{name:"user", age:22, val:[100,2,3]};
console.log(b);//{name:"newUser", age:22, val:[100,2,3]};

spread merges overwiting later keys, last value will be used
const a = { x: 1 };
const b = { x: 2 };

console.log({ ...a, ...b }); //{ x: 2 }


note: undefined and null cant be spread or rest
Rest operator
Note: rest has to be last parameter
spread with function
function add(a, b, c) {
  return a + b + c;
}
const nums = [1, 2, 3];
console.log(add(...nums));//spread at argument level


rest with functions
// rest operator -- parameter level --> collecting all the values passed
function add(...rest) {
  return rest.reduce((item,acc)=>item +=acc);
}
const nums = [1, 2, 3];
console.log(add(1,2,3,4,5));


rest with array destructuring
const [a, ...rest] = [1, 2, 4, 5];
console.log(a);//1
console.log(rest);//[2,4,5]


rest can be empty
const [a, ...rest] = [1];
console.log(a);//1
console.log(rest);//[]


note: rest also do shallow copy like spread, i.e., copies by value only top level and nested levels are copied by reference

you can re-arrange order of element to extract values
const obj = { a: 1, b: 2, c: 3 };
const { b, ...rest } = obj;

console.log(rest); // {a:1, c:3}


important react bug – interview 
const user = { name: "Leo" };
const list = [user, user]; //[{ name: "Leo" },{ name: "Leo" }]

const newList = [...list]; //[{ name: "Leo" },{ name: "Leo" }]

newList[0].name = "Updated"; //will update both as both the obj refernece are same
console.log(list[1].name); //updated




React
React redux
Predicatable state management liberary
Flux was developed by facebook for state management
Central flow, predictable state flow, easy debugging, global access without prop drilling
Install: npm install @reduxjs/toolkit and npm install react-redux
Flow: - for sync
Component dispatches an action
Action goes to reducer
Reducer updates store
Updated state flows back to components
Flow: - for async
Component dispaches action
Thunk calls API
RTK handles pending, fulfilled, rejected
UI updates
Slice: state + reducers + actions in one file
Slice contains: 
name, 
initialState, 
reducers (sync logic), ‘
auto-generated actions, 
extra reducers for async logic
Reducer: 
a reducer takes (state, action) and returns the new state
reducer function must be pure
we can write code that mutates the state directly. RTK uses immer to turn those mutation into immutable updates


[UI Button] --dispatch --> [Action: increment()] --> [Slice reducer updates store] --> [Store] --> [UI reads new state via useSelector] --> re-render

Step 1: create slice
Note: slice creates reducers
Basic slice structure
import { createSlice } from "@reduxjs/toolkit";

const intialState = {
  value: 0,
};

const counterSlice = createSlice({
    name: "counter",
    initialState,
    reducers:{
        
    }
})

Complete code of slice
import { createSlice } from "@reduxjs/toolkit";

// initial state
const intitialState = {
  value: 0,
};

// contains name, intialState, reducers(logic)
// returns pure reducer function & actions
const counterSlice = createSlice({
  name: "counter", // used to prefix action types: "counter/increment"
  intitialState, //slice's intial state

  //synchrounous reducers - mutaing code bcz of immer
  reducers: {
    increment(state) {
      state.value += 1;
    },
    decrement(state) {
      state.value -= 1;
    },
    addBy(state, action) {
      // action.payload expected to be number
      state.value += action.payload;
    },
    reset(state) {
      state.value = 0;
    },
  },
});

// Named exports: action creators we can dispatch from components
export const { increment, decrement, addBy, reset } = counterSlice.actions;

// Default export: slice reducer to plug into the store
export default counterSlice.reducer;


Step 2: create store
   [Slices] ---> [STORE] ---> [React App]
Note: store combines reducers into one global state
configureStore creates store with: default values, built-in middleware like Thunk & immer

import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./mySlice/CounterSlice"; //reducer generated by RTK, we can give it any name

const store = configureStore({
  reducer: {
    // key: slice name, value: its reducer
    counter: counterReducer,
  },
});

export default store;


Step 3: connect store using Provider
Provider makes the store available to the entire app
Provider gives access to 
useSelector  it read state 
& useDispatch  it dispatches the action
import React from "react";
import Home from "./Home";
import { Provider } from "react-redux";
import store from "./Store";
const RTK = () => {
  return (
    // provider connects redux store to React
    <Provider store={store}>
      <Home />
    </Provider>
  );
};

export default RTK;



Step 4: implement RTK
useSelector: 
It access state.
It takes callback function which has access to slice: state.sliceName.variableName
useDispatch
It dispatches the action, it triggers the action that needs to be performed on state
Dispatch(action()). Note: dispatch is a variable that is accesing the useDispatch hook
import React, { useState } from "react";
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement, addBy, reset } from "./mySlice/CounterSlice";

const Home = () => {
  //   select value from store
  const value = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  const [amount, setAmount] = useState(0);

  return (
    <div>
      <h1>Count: {value}</h1>
      <div style={{ display: "flex", gap: 8, marginBottom: 12 }}>
        <button onClick={() => dispatch(increment())}>Increment</button>
        <button onClick={() => dispatch(decrement())}>Decrement</button>
        <button onClick={() => dispatch(reset())}>Reset</button>
      </div>

      <div>
        <input
          type="number"
          value={amount}
          onChange={(e) => setAmount(e.target.value)}
        />
        <button
          onClick={() => {
            const num = Number(amount) || 0;
            dispatch(addBy(num));
            setAmount(0);
          }}
        >
          Add By{" "}
        </button>
      </div>
    </div>
  );
};

export default Home;



React optimization
Shallow and deep comparison
In shallow comparison only reference is check for non premetive data types and for premetive data types value is checked
But in deep comparison each nested value is checked
In react everthing relly on shallow comparison as it is faster

const a = 10;
const b = 10;
const c = "10";

const o1 = { name: "a" };
const o2 = { name: "a" };

// console.log(a==b);//t
console.log(a === b); //t

console.log(a == c); //t
console.log(a === c); //f

// two different obj are never equal - shallow comparison
console.log(o1 == o2); //f
console.log(o1 === o2); //f

//deep comparison

console.log(JSON.stringify(o1) == JSON.stringify(o2)); //t
console.log(JSON.stringify(o1) === JSON.stringify(o2)); //t


Note:
All relly on shallow comparison, that means even if the value is same and we are passing a new object each time then also react re-renders as it is fast
React.memo
useCallback
useMemo
useEffect
RTK

useMemo or memo
useMemo prevents re-rendering if the props don’t change.
Example of useMemo:
Counter component: it don’t have any props
here if we don’t wrap Counter componet in React.memo while exporting then it will re-render even though its props are not changing
import React, { useRef } from "react";

const Counter = () => {
  const count = useRef(0);
  return (
    <>
      <p>Render count: {count.current++}</p>
    </>
  );
};

export default React.memo(Counter);


Parent component: MemoHome
import React, { useState } from "react";
import Counter from "./Couter";

function MemoHome() {
  const [val, setVal] = useState(0);
  return (
    <>
      <p>Value from parent: {val}</p>
      <button onClick={() => setVal((prev) => prev + 1)}>Increase value</button>
      <Counter />
    </>
  );
}

export default UseMemoHome;

Note: use React.memo only when it is necessary as it has cost. React has to compare previous props with current props using shallow comparison which has cost
✔ useCallback — memoizes a function reference (stable function)
✔ useMemo — memoizes a computed value (stable value)
✔ React.memo — memoizes a component (skip re-render if props unchanged)
React virtualization/windowing
We usevirtualization when we need to render a list or something that is really huge (like 1 million records in a list)
Re – rendering these list will freeze the UI, so we use virtualization
When size of item is fixed like list then use react-window
If size of item is not fixed like reddit blogs then use react-virtualized
How it works: it renders only those items that are visible in the given area, and as we scroll the older items unmounts
Used: infinite scrolling, rendering long list like netlix renders thumbnails as we scroll
Without external liberary also we can implement virtualization but it will be lengthy

Code:
npm install react-window
note: will cover later
infinte scrolling
there are multiple ways to do so but we will do the easiest way
there are two types of pagination: server side(using url when we fetch it required data only) and client side pagination(when we fetch all data and render only required data)
Custome hook
Custome hooks cant be async as reacts expects hooks to run in sync during render
Fetch must be used inside useEffect. As fetch is async and react needs to know whne to run it
Note: we cant use aync code inside custome hook, if we are using then use inside useEffect, else the async code will start working in the background that react don’t allow. React works syncronously

Render (fast, synchronous)
↓
useEffect triggers (async allowed)
↓
Fetch happens here
↓
State updates
↓
React re-renders with actual data
import React, { useState } from "react";
import Card from "./Card";
import useFetch from "./useFetch";
const URL = "https://jsonplaceholder.typicode.com/posts";
const PaginationHome = () => {
  const { value, loading, err } = useFetch(URL);
  const [startInd, setStartInd] = useState(0);
  const [endInd, setEndInd] = useState(5);

  const handlePrev = () => {
    setEndInd((prev) => prev - 5);
    setStartInd((prev) => prev - 5);
  };

  const handleNext = () => {
    setStartInd((prev) => prev + 5);
    setEndInd((prev) => prev + 5);
  };

  return (
    <div>
      <h1> I am paginatyion again</h1>
      {loading ? (
        <h1>I am still loading...</h1>
      ) : err ? (
        <h1>Error in fetching data</h1>
      ) : (
        value.slice(startInd, endInd).map((item) => {
          return <Card key={item.id} val={item} />;
        })
      )}

      <button onClick={handlePrev} disabled={startInd == 0 ? true : false}>
        Prev
      </button>
      <button onClick={handleNext} disabled={endInd == 30 ? true : false}>
        Next
      </button>
    </div>
  );
};

export default PaginationHome;



import React from "react";

function Card({ val }) {
  //   console.log(item);
  return (
    <div style={{ border: "2px solid black", padding: "5px", margin: " 5px" }}>
      <span>{val.id}</span>
      <p>{val.title}</p>
    </div>
  );
}

export default Card;


//fetching data using custom hook
import { useEffect, useState } from "react";

const useFetch = (URL) => {
  const [value, setValue] = useState([]);
  const [loading, setLoading] = useState(true);
  const [err, setErr] = useState(null);

  async function getData() {
    try {
      const data = await fetch(URL);
      const response = await data.json();
      setValue(response);
    } catch (err) {
      setErr(err);
    } finally {
      setLoading(false);
    }
  }

  //   getData must call inside useEffect as side effects(fetch) always comes inside useEffect or useLayoutEffect
  useEffect(() => {
    getData();
  }, [URL]);

  return { value, loading, err };
};

export default useFetch;



infinite scrolling using client side
scrollTop  how far you have scrolled from top
clientHeight → Visible height of the container, here 400px
scrollHeight → Total height of the scrollable content
import React, { useState } from "react";
import Card from "./Card";
import useFetch from "./useFetch";
const URL = "https://jsonplaceholder.typicode.com/posts";
const PaginationHome = () => {
  const { value, loading, err } = useFetch(URL);
  const [visibleCount, setVisibleCount] = useState(5);

  const handleScroll = (e) => {
    const { scrollTop, clientHeight, scrollHeight } = e.target;
    if (scrollTop + clientHeight >= scrollHeight - 5) {
      setVisibleCount((prev) => prev + 5);
    }
    // console.log(e.target);
  };

  return (
    <div
      onScroll={handleScroll}
      style={{
        height: "400px",
        overflowY: "auto",
        border: "2px solid red",
      }}
    >
      <h1> I am paginatyion again</h1>
      {loading ? (
        <h1>I am still loading...</h1>
      ) : err ? (
        <h1>Error in fetching data</h1>
      ) : (
        value.slice(0, visibleCount).map((item) => {
          return <Card key={item.id} val={item} />;
        })
      )}
    </div>
  );
};

export default PaginationHome;

rest of the code is same as pagination

//some advance part that I don’t know  using the advance way
// using 