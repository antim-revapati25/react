# JavaScript Interview Master

# 1. Hoisting

---

## **Q1: What is hoisting in JavaScript?**  
(Theory – answer visible)

Hoisting is JavaScript’s behavior of moving **variable and function declarations** to the top of their scope during compilation.

---

## **Q2: What gets hoisted differently between `var`, `let`, and `const`?**  
(Theory – answer visible)

- `var`: declaration hoisted, initialized to `undefined`
- `let` & `const`: declaration hoisted but **not initialized** (Temporal Dead Zone)
- Function declarations: fully hoisted
- Function expressions: depend on their variable type

---

## **Q3: Output-based hoisting**
```js
console.log(a);
var a = 10;
```

<details>
<summary>Show Answer</summary>

`undefined`  
Because `var a` is hoisted but initialized with `undefined`.

</details>

---

## **Q4: Output-based hoisting with let**
```js
console.log(a);
let a = 10;
```

<details>
<summary>Show Answer</summary>

`ReferenceError: Cannot access 'a' before initialization`  
Because it's in the Temporal Dead Zone.

</details>

---

## **Q5: Function declaration hoisting**
```js
greet();
function greet() { console.log("Hi"); }
```

<details>
<summary>Show Answer</summary>

`Hi`  
Function declarations are fully hoisted.

</details>

---

## **Q6: Function expression hoisting**
```js
say();
var say = function () { console.log("Hello"); };
```

<details>
<summary>Show Answer</summary>

`TypeError: say is not a function`  
`var say` → hoisted as `undefined`.

</details>

---

# 2. Scopes

---

## **Q7: How many types of scopes exist in JavaScript?**  
(Theory – visible)

- Global scope  
- Function scope  
- Block scope (`let`, `const`)  
- Lexical scope  
- Module scope  

---

## **Q8: What is lexical scoping?**  
(Theory – visible)

Lexical scoping means the scope of a variable is determined **by its physical location** in the source code.

---

## **Q9: Output-based scope**
```js
let x = 1;
function test() {
  let x = 2;
  console.log(x);
}
test();
console.log(x);
```

<details>
<summary>Show Answer</summary>

2  
1  

</details>

---

# 3. Execution Context

---

## **Q10: What is execution context?**  
(Theory – visible)

Each JS file/function runs inside an execution context with:
- Creation phase (hoisting)
- Execution phase (line-by-line execution)

---

## **Q11: What are the types of execution contexts?**  
(Theory – visible)

- Global execution context  
- Function execution context  
- Eval execution context  

---

## **Q12: What is the call stack?**  
(Theory – visible)

The stack where execution contexts are pushed/popped.

---

# 4. Closures

---

## **Q13: What is a closure?**  
(Theory – visible)

A closure is a function + its lexical scope, even after the outer function returns.

---

## **Q14: Simple closure example**
```js
function outer() {
  let x = 10;
  return function inner() {
    console.log(x);
  };
}
outer()();
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q15: Closure loop trap**
```js
for (var i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 100);
}
```

<details>
<summary>Show Answer</summary>

4  
4  
4  

Because `var` is function-scoped and the closure captures final value.

</details>

---

# 5. `this` Binding

---

## **Q16: What determines the value of `this`?**  
(Theory – visible)

The **call-site** (how a function is invoked), not where it's defined.

---

## **Q17: 4 rules of `this` binding**  
(Theory – visible)

1. Default binding (global object)  
2. Implicit binding (`obj.method()`)  
3. Explicit binding (`call`, `apply`, `bind`)  
4. `new` binding (constructor)  

---

## **Q18: Arrow function `this`**
```js
const obj = {
  name: "Leo",
  getName: () => console.log(this.name)
};
obj.getName();
```

<details>
<summary>Show Answer</summary>

`undefined`  
Arrow functions do not bind their own `this`.

</details>

---

# 6. Arrow Functions

---

## **Q19: Differences between arrow and normal functions**  
(Theory – visible)

- No own `this`  
- No `arguments`  
- No `prototype`  
- Cannot use `new`  
- Lexically scoped  

---

## **Q20: Arrow + parent arguments**
```js
function test() {
  return () => console.log(arguments[0]);
}
const fn = test(10);
fn();
```

<details>
<summary>Show Answer</summary>

10  
Arrow inherits arguments from parent function.

</details>

---

# 7. Prototypes & Inheritance

---

## **Q21: What is a prototype?**  
(Theory – visible)

A hidden object linked to every JS function/object, enabling inheritance.

---

## **Q22: Prototype chain lookup**
```js
const a = {};
console.log(a.__proto__ === Object.prototype);
```

<details>
<summary>Show Answer</summary>

true

</details>

---

## **Q23: Prototype shadowing**
```js
function A() {}
A.prototype.x = 10;

const a = new A();
a.x = 20;

console.log(a.x);
console.log(A.prototype.x);
```

<details>
<summary>Show Answer</summary>

20  
10  

</details>

---

# 8. Classes

---

## **Q24: Are JavaScript classes real classes?**  
(Theory – visible)

No. They are **syntactic sugar** over prototypes.

---

## **Q25: typeof class**
```js
class A {}
console.log(typeof A);
```

<details>
<summary>Show Answer</summary>

"function"

</details>

---

# 9. Event Loop

---

## **Q26: Microtasks vs macrotasks?**  
(Theory – visible)

- Microtasks: Promises, queueMicrotask  
- Macrotasks: setTimeout, setInterval  

Microtasks run **before** rendering and before next macrotask.

---

## **Q27: Event loop output**
```js
console.log(1);
setTimeout(() => console.log(2));
Promise.resolve().then(() => console.log(3));
console.log(4);
```

<details>
<summary>Show Answer</summary>

1  
4  
3  
2  

</details>

---

# 10. Promises

---

## **Q28: Basic promise chain**
```js
Promise.resolve(1)
  .then(x => x + 1)
  .then(console.log);
```

<details>
<summary>Show Answer</summary>

2

</details>

---

## **Q29: Promise returning a promise**
```js
Promise.resolve(5)
  .then(x => Promise.resolve(x * 2))
  .then(console.log);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

# 11. async/await

---

## **Q30: Return value of async function**
```js
async function f() {
  return 10;
}
console.log(f());
```

<details>
<summary>Show Answer</summary>

`Promise { 10 }`

</details>

---

## **Q31: async/await ordering**
```js
async function f(){
  console.log(1);
  await null;
  console.log(2);
}
console.log(3);
f();
console.log(4);
```

<details>
<summary>Show Answer</summary>

3  
1  
4  
2  

</details>

---

# 12. Error Handling

---

## **Q32: async error handling**
```js
async function test() {
  try {
    await Promise.reject("err");
  } catch (e) {
    console.log("caught");
  }
}
test();
```

<details>
<summary>Show Answer</summary>

caught

</details>

---

## **Q33: setTimeout cannot be caught**
```js
try {
  setTimeout(() => { throw "err"; });
} catch (e) {
  console.log("caught");
}
```

<details>
<summary>Show Answer</summary>

Uncaught "err"  
(setTimeout runs in Web APIs, outside try/catch.)

</details>

---

# 13. Iterators & Generators

---

## **Q34: Basic generator**
```js
function* gen() {
  yield 1;
  yield 2;
}
const g = gen();
console.log(g.next().value);
console.log(g.next().value);
```

<details>
<summary>Show Answer</summary>

1  
2  

</details>

---

# 14. Objects

---

## **Q35: Object.freeze vs Object.seal**  
(Theory – visible)

- freeze → cannot add/remove/modify  
- seal → cannot add/remove, but can modify  

---

## **Q36: defineProperty**
```js
const obj = {};
Object.defineProperty(obj, "a", { value: 10, writable: false });
obj.a = 20;
console.log(obj.a);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

# 15. Arrays

---

## **Q37: Array coercion**
```js
console.log([1,2,3] + [4,5]);
```

<details>
<summary>Show Answer</summary>

"1,2,3" + "4,5"  
→ `"1,2,34,5"`

</details>

---

## **Q38: map without return**
```js
console.log([1,2,3].map(n => { n * 2 }));
```

<details>
<summary>Show Answer</summary>

[undefined, undefined, undefined]

</details>

---

# 16. Functions

---

## **Q39: Currying example**
```js
const add = a => b => c => a + b + c;
console.log(add(1)(2)(3));
```

<details>
<summary>Show Answer</summary>

6

</details>

---

## **Q40: Higher-order function**
```js
function hof(fn) {
  return function (x) {
    return fn(x);
  }
}
```

(Theory – visible)  
A higher-order function returns a function or receives one.


---

# 17. Type Coercion (Advanced)

---

## **Q41: What is type coercion in JavaScript?**  
(Theory – visible)

Type coercion is the automatic or implicit conversion of values from one data type to another (e.g., string → number).  
Happens in:
- Arithmetic operations  
- Loose equality `==`  
- Comparisons  
- String concatenation  

---

## **Q42: Output-based**
```js
console.log("5" - 2);
```

<details>
<summary>Show Answer</summary>

3  
String is converted to number.

</details>

---

## **Q43: Output-based**
```js
console.log("5" + 2);
```

<details>
<summary>Show Answer</summary>

"52"  
String concatenation wins over arithmetic.

</details>

---

## **Q44: Output-based**
```js
console.log([] == "");
```

<details>
<summary>Show Answer</summary>

true  
([] → "")

</details>

---

## **Q45: Output-based**
```js
console.log([] == 0);
```

<details>
<summary>Show Answer</summary>

true  
([] → "" → 0)

</details>

---

## **Q46: Output-based**
```js
console.log([null] == "");
```

<details>
<summary>Show Answer</summary>

true  
([null] → "")

</details>

---

## **Q47: Output-based**
```js
console.log([undefined] == 0);
```

<details>
<summary>Show Answer</summary>

true  
([undefined] → "" → 0)

</details>

---

## **Q48: NaN comparison**
```js
console.log(NaN == NaN);
```

<details>
<summary>Show Answer</summary>

false  
NaN is never equal to itself.

</details>

---

## **Q49: typeof NaN**
```js
console.log(typeof NaN);
```

<details>
<summary>Show Answer</summary>

"number"

</details>

---

# 18. More `this` Scenarios (Advanced)

---

## **Q50: Losing implicit binding**
```js
const user = {
  name: "Leo",
  greet() { console.log(this.name); }
};

const fn = user.greet;
fn();
```

<details>
<summary>Show Answer</summary>

undefined  
Because `fn()` is a plain function call → default binding.

</details>

---

## **Q51: Fix the above issue using bind**
```js
const fn2 = user.greet.bind(user);
fn2();
```

<details>
<summary>Show Answer</summary>

"Leo"

</details>

---

## **Q52: Arrow inside object**
```js
const obj = {
  value: 100,
  show: () => console.log(this.value)
}
obj.show();
```

<details>
<summary>Show Answer</summary>

undefined  
Arrow functions do NOT bind `this`.

</details>

---

## **Q53: Nested function loses `this`**
```js
const obj = {
  name: "Leo",
  show() {
    function inner() {
      console.log(this.name);
    }
    inner();
  }
}
obj.show();
```

<details>
<summary>Show Answer</summary>

undefined  
`inner()` is a plain function call.

</details>

---

## **Q54: Fix nested `this` using arrow**
```js
const obj = {
  name: "Leo",
  show() {
    const inner = () => console.log(this.name);
    inner();
  }
}
obj.show();
```

<details>
<summary>Show Answer</summary>

"Leo"

</details>

---

# 19. Object Deep Concepts

---

## **Q55: What are getters and setters?**  
(Theory – visible)

They allow encapsulation using `get` and `set` keywords to intercept property reads/writes.

---

## **Q56: Getter/Setter output**
```js
const obj = {
  count: 1,
  get value() {
    return this.count;
  },
  set value(v) {
    this.count = v * 2;
  }
};

obj.value = 5;
console.log(obj.value);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q57: Freezing object**
```js
const obj = Object.freeze({ a: 10 });
obj.a = 20;
console.log(obj.a);
```

<details>
<summary>Show Answer</summary>

10  
Frozen objects cannot be modified.

</details>

---

# 20. Arrays – Deep Concepts

---

## **Q58: Flatten array**
```js
console.log([1, [2, [3]]].flat(2));
```

<details>
<summary>Show Answer</summary>

[1, 2, 3]

</details>

---

## **Q59: Array holes**
```js
console.log([1, , 3].length);
```

<details>
<summary>Show Answer</summary>

3  
Array holes still count in length.

</details>

---

## **Q60: Reduce example**
```js
console.log([1,2,3].reduce((a,b) => a + b, 0));
```

<details>
<summary>Show Answer</summary>

6

</details>

---

## **Q61: Mapping without return**
```js
console.log([1,2,3].map(n => { n * 2 }));
```

<details>
<summary>Show Answer</summary>

[undefined, undefined, undefined]

</details>

---

## **Q62: Spread array**
```js
console.log([..."Leo"]);
```

<details>
<summary>Show Answer</summary>

["L","e","o"]

</details>

---

# 21. Prototypes – Deep

---

## **Q63: Shadowing prototype property**
```js
function A(){}
A.prototype.x = 10;

const a = new A();
a.x = 20;

console.log(a.x);
console.log(A.prototype.x);
```

<details>
<summary>Show Answer</summary>

20  
10  

</details>

---

## **Q64: Deleting shadow property**
```js
delete a.x;
console.log(a.x);
```

<details>
<summary>Show Answer</summary>

10  
Now lookup falls back to prototype.

</details>

---

# 22. Classes – Advanced

---

## **Q65: Static fields**
```js
class A {
  static x = 5;
}
console.log(A.x);
```

<details>
<summary>Show Answer</summary>

5

</details>

---

## **Q66: Instance vs static**
```js
class B {
  static x = 10;
  y = 20;
}

const b = new B();
console.log(b.x, b.y);
```

<details>
<summary>Show Answer</summary>

undefined 20

</details>

---

## **Q67: Private fields**
```js
class C {
  #a = 10;
  getA() { return this.#a; }
}
console.log(new C().getA());
```

<details>
<summary>Show Answer</summary>

10

</details>

---

# 23. Event Loop – Deep

---

## **Q68: Microtasks vs macrotasks**
```js
console.log("start");

setTimeout(() => console.log("timeout"));

Promise.resolve().then(() => console.log("promise"));

console.log("end");
```

<details>
<summary>Show Answer</summary>

start  
end  
promise  
timeout  

</details>

---

## **Q69: Nested microtasks**
```js
Promise.resolve().then(() => {
  console.log("p1");
  Promise.resolve().then(() => console.log("p2"));
});
console.log("sync");
```

<details>
<summary>Show Answer</summary>

sync  
p1  
p2  

</details>

---

## **Q70: Loop with promises**
```js
for (let i = 0; i < 3; i++) {
  Promise.resolve().then(() => console.log(i));
}
```

<details>
<summary>Show Answer</summary>

0  
1  
2  

</details>

---

# 24. Promises – Advanced

---

## **Q71: Promise chain order**
```js
Promise.resolve()
  .then(() => console.log(1))
  .then(() => console.log(2));

Promise.resolve()
  .then(() => console.log(3));
```

<details>
<summary>Show Answer</summary>

1  
3  
2  

</details>

---

## **Q72: Returning Promise inside Promise**
```js
Promise.resolve(5)
  .then(x => Promise.resolve(x * 2))
  .then(console.log);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q73: async returning simple value**
```js
async function f(){ return 5; }
console.log(f());
```

<details>
<summary>Show Answer</summary>

Promise { 5 }

</details>

---

## **Q74: async throwing error**
```js
async function f(){ throw 10; }
f().catch(console.log);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

# 25. async/await – Deep

---

## **Q75: Ordering puzzle**
```js
async function test(){
  console.log(1);
  await null;
  console.log(2);
}
console.log(3);
test();
console.log(4);
```

<details>
<summary>Show Answer</summary>

3  
1  
4  
2  

</details>

---

## **Q76: await in loop (bad)**
```js
async function f(){
  for (let i=0;i<3;i++){
    await console.log(i);
  }
}
f();
```

<details>
<summary>Show Answer</summary>

0  
1  
2  
Executes sequentially; slows down.

</details>

---

## **Q77: fix using Promise.all**
```js
await Promise.all([1,2,3].map(async (x) => console.log(x)));
```

(Theory – visible)

---

# 26. Error Handling – Advanced

---

## **Q78: Why setTimeout cannot be caught by try/catch?**  
(Theory – visible)

Because setTimeout executes in Web APIs, not the JS call stack.

---

## **Q79: Fix asynchronous error handling**
```js
setTimeout(() => {
  try {
    throw "err";
  } catch (e) {
    console.log("caught");
  }
});
```

<details>
<summary>Show Answer</summary>

caught

</details>

---

# 27. Iterators & Generators – Advanced

---

## **Q80: Generator return value**
```js
function* gen(){
  yield 1;
  yield 2;
  return 3;
}

const g = gen();
console.log(g.next());
console.log(g.next());
console.log(g.next());
console.log(g.next());
```

<details>
<summary>Show Answer</summary>

{value:1, done:false}  
{value:2, done:false}  
{value:3, done:true}  
{value:undefined, done:true}  

</details>


---

# 28. Advanced Objects & Property Descriptors

---

## **Q81: What is `Object.getOwnPropertyDescriptor()` used for?**  
(Theory – visible)

It retrieves metadata about an object's property such as:
- writable  
- enumerable  
- configurable  
- value  
- get/set functions  

---

## **Q82: Output – writable: false**
```js
const obj = {};
Object.defineProperty(obj, "x", {
  value: 10,
  writable: false
});
obj.x = 20;
console.log(obj.x);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q83: enumerable: false property**
```js
const obj = {};
Object.defineProperty(obj, "x", {
  value: 10,
  enumerable: false
});
console.log(Object.keys(obj));
```

<details>
<summary>Show Answer</summary>

[]  
Because x is not enumerable.

</details>

---

## **Q84: configurable: false**
```js
const obj = {};
Object.defineProperty(obj, "x", {
  value: 10,
  configurable: false
});
delete obj.x;
console.log(obj.x);
```

<details>
<summary>Show Answer</summary>

10  
Non-configurable properties cannot be deleted.

</details>

---

# 29. Map, Set, WeakMap, WeakSet

---

## **Q85: Differences between Map and Object**  
(Theory – visible)

Maps:
- Allow any type as key  
- Ordered  
- Size known via `.size`  
- No key coercion  

Objects:
- Only string or symbol keys  
- No guaranteed order  
- Need manual length tracking  

---

## **Q86: Using Map**
```js
const map = new Map();
map.set("a", 1);
map.set(10, "num");
console.log(map.get(10));
```

<details>
<summary>Show Answer</summary>

"num"

</details>

---

## **Q87: WeakMap behavior**
```js
let obj = {name: "Leo"};
const wm = new WeakMap();

wm.set(obj, 100);
obj = null;

console.log("entry remains?");
```

<details>
<summary>Show Answer</summary>

The WeakMap entry is **eligible for garbage collection**  
WeakMaps do NOT prevent object cleanup.

</details>

---

# 30. Symbols

---

## **Q88: What are Symbols?**  
(Theory – visible)

Symbols are unique identifiers used to avoid property name collisions and create hidden object keys.

---

## **Q89: Symbol as object key**
```js
const sym = Symbol("id");
const obj = { [sym]: 123 };
console.log(obj[sym]);
```

<details>
<summary>Show Answer</summary>

123

</details>

---

## **Q90: Why Symbol keys don’t appear in for-in?**
```js
const s = Symbol("a");
const obj = { [s]: 1 };
console.log(Object.keys(obj));
```

<details>
<summary>Show Answer</summary>

[]  
Symbol keys are non-enumerable in typical iteration.

</details>

---

# 31. Strict Mode

---

## **Q91: What changes in strict mode?**  
(Theory – visible)

- `this` becomes undefined in functions  
- No accidental globals  
- Silent errors become thrown errors  
- Duplicate parameters disallowed  
- Octal literals disallowed  

---

## **Q92: Strict mode `this`**
```js
"use strict";
function f(){ console.log(this); }
f();
```

<details>
<summary>Show Answer</summary>

undefined

</details>

---

# 32. Advanced Functions

---

## **Q93: What is a Higher-Order Function?**  
(Theory – visible)

A function that either:
- accepts another function as a parameter  
- returns a function  

---

## **Q94: Example**
```js
function hof(fn){
  return function(x){
    return fn(x);
  };
}
```

(Theory – visible)

---

## **Q95: Function composition**
```js
const add = x => x + 1;
const square = x => x * x;

const compose = (f,g) => x => f(g(x));

console.log(compose(square, add)(2));
```

<details>
<summary>Show Answer</summary>

9  
(add 2 → 3, square 3 → 9)

</details>

---

## **Q96: Memoization**
```js
function memo(fn){
  const cache = {};
  return function(x){
    if(cache[x]) return cache[x];
    return cache[x] = fn(x);
  }
}
```

(Theory – visible)

---

# 33. Recursion & Stack

---

## **Q97: Simple recursion**
```js
function fact(n){
  if(n === 1) return 1;
  return n * fact(n-1);
}
console.log(fact(4));
```

<details>
<summary>Show Answer</summary>

24

</details>

---

## **Q98: Tail-recursion**
```js
function tailFact(n, acc = 1){
  if(n === 1) return acc;
  return tailFact(n-1, n * acc);
}
console.log(tailFact(4));
```

<details>
<summary>Show Answer</summary>

24  
(Optimized form in strict mode)

</details>

---

# 34. Advanced Event Loop Puzzles

---

## **Q99**
```js
console.log("A");

setTimeout(() => console.log("B"), 0);

Promise.resolve().then(() => console.log("C"));

console.log("D");
```

<details>
<summary>Show Answer</summary>

A  
D  
C  
B  

</details>

---

## **Q100: Promise inside Promise**
```js
Promise.resolve().then(() => {
  console.log("p1");
  Promise.resolve().then(() => console.log("p2"));
});
console.log("sync");
```

<details>
<summary>Show Answer</summary>

sync  
p1  
p2  

</details>

---

## **Q101: Mixing micro + macro tasks**
```js
console.log(1);

setTimeout(() => console.log(2));

Promise.resolve().then(() => {
  console.log(3);
  setTimeout(() => console.log(4));
});

console.log(5);
```

<details>
<summary>Show Answer</summary>

1  
5  
3  
2  
4  

</details>

---

# 35. Inputs, Spread, Rest

---

## **Q102: Spread operator**
```js
const a = [1,2,3];
const b = [...a, 4];
console.log(b);
```

<details>
<summary>Show Answer</summary>

[1,2,3,4]

</details>

---

## **Q103: Rest operator**
```js
function sum(...nums){
  return nums.reduce((a,b)=>a+b,0);
}
console.log(sum(1,2,3));
```

<details>
<summary>Show Answer</summary>

6

</details>

---

## **Q104: Copy vs reference**
```js
const a = [1,2,3];
const b = a;
a.push(4);
console.log(b);
```

<details>
<summary>Show Answer</summary>

[1,2,3,4]

</details>

---

# 36. Deep Copy vs Shallow Copy

---

## **Q105: Shallow copy using spread**
```js
const obj = { a: 1, nested: { b: 2 } };
const copy = { ...obj };
copy.nested.b = 99;
console.log(obj.nested.b);
```

<details>
<summary>Show Answer</summary>

99  
Shallow copy shares nested references.

</details>

---

## **Q106: Deep copy using JSON**
```js
const obj = { a: 1, nested: { b: 2 } };
const deep = JSON.parse(JSON.stringify(obj));
deep.nested.b = 99;
console.log(obj.nested.b);
```

<details>
<summary>Show Answer</summary>

2

</details>

---

# 37. Error Objects & Custom Errors

---

## **Q107: Throw custom error**
```js
try {
  throw new Error("Oops");
} catch(e){
  console.log(e.message);
}
```

<details>
<summary>Show Answer</summary>

"Oops"

</details>

---

## **Q108: finally block**
```js
try {
  console.log("start");
  throw "err";
} catch(e){
  console.log("caught");
} finally {
  console.log("cleanup");
}
```

<details>
<summary>Show Answer</summary>

start  
caught  
cleanup  

</details>

---

# 38. Advanced Generators

---

## **Q109: Bi-directional generator**
```js
function* gen(){
  const x = yield 10;
  yield x * 2;
}
const g = gen();
console.log(g.next());
console.log(g.next(7));
```

<details>
<summary>Show Answer</summary>

{value:10,done:false}  
{value:14,done:false}  

</details>

---

## **Q110: Infinite generator**
```js
function* counter(){
  let i = 0;
  while(true) yield i++;
}
const g = counter();
console.log(g.next().value);
console.log(g.next().value);
```

<details>
<summary>Show Answer</summary>

0  
1  

</details>

---

# 39. Equality & Identity

---

## **Q111: Loose vs strict**
```js
console.log("0" == 0);
console.log("0" === 0);
```

<details>
<summary>Show Answer</summary>

true  
false  

</details>

---

## **Q112: null & undefined**
```js
console.log(null == undefined);
console.log(null === undefined);
```

<details>
<summary>Show Answer</summary>

true  
false  

</details>

---

## **Q113: Object equality**
```js
console.log({a:1} === {a:1});
```

<details>
<summary>Show Answer</summary>

false  
Different references.

</details>

---

## **Q114: Array equality**
```js
console.log([1,2] === [1,2]);
```

<details>
<summary>Show Answer</summary>

false

</details>

---

# 40. BigInt

---

## **Q115: What is BigInt?**  
(Theory – visible)

A numeric type for arbitrarily large integers using `123n` syntax.

---

## **Q116: BigInt example**
```js
console.log(10n + 20n);
```

<details>
<summary>Show Answer</summary>

30n

</details>

---

## **Q117: BigInt cannot mix with number**
```js
console.log(10n + 5);
```

<details>
<summary>Show Answer</summary>

TypeError

</details>

---

# 41. Data Structures in JS

---

## **Q118: What is Set useful for?**  
(Theory – visible)

- Unique values  
- Fast membership checks  
- Removing duplicates  

---

## **Q119: Remove duplicates**
```js
const arr = [1,2,2,3];
console.log([...new Set(arr)]);
```

<details>
<summary>Show Answer</summary>

[1,2,3]

</details>

---

## **Q120: WeakSet behavior**
```js
let obj = { x: 1 };
const ws = new WeakSet();
ws.add(obj);
obj = null;
console.log("cleanup");
```

<details>
<summary>Show Answer</summary>

Object inside WeakSet is **eligible for garbage collection**.

</details>

---


# 42. Very Hard Coercion & Logic Puzzles

---

## **Q121: Comparison Puzzle**
```js
console.log(1 < 2 < 3);
console.log(3 > 2 > 1);
```

<details>
<summary>Show Answer</summary>

true  
false  

Explanation:  
- `1 < 2 < 3` → `true < 3` → `1 < 3` → true  
- `3 > 2 > 1` → `true > 1` → `1 > 1` → false  

</details>

---

## **Q122**
```js
console.log([] + []);
```

<details>
<summary>Show Answer</summary>

"" (empty string)

</details>

---

## **Q123**
```js
console.log({} + []);
```

<details>
<summary>Show Answer</summary>

"[object Object]"

</details>

---

## **Q124**
```js
console.log([] == ![]);
```

<details>
<summary>Show Answer</summary>

true  

([] → "" → false)  
(![] → false)  
false == false → true

</details>

---

## **Q125**
```js
console.log([] == 0);
```

<details>
<summary>Show Answer</summary>

true  
([] → "" → 0)

</details>

---

## **Q126**
```js
console.log("0" == false);
```

<details>
<summary>Show Answer</summary>

true  
("0" → 0) therefore 0 == false → true

</details>

---

## **Q127**
```js
console.log([] == "");
```

<details>
<summary>Show Answer</summary>

true  
([] → "")

</details>

---

## **Q128**
```js
console.log([null] == "");
```

<details>
<summary>Show Answer</summary>

true  
([null] → "")

</details>

---

## **Q129**
```js
console.log([undefined] == 0);
```

<details>
<summary>Show Answer</summary>

true  
([undefined] → "" → 0)

</details>

---

## **Q130: typeof quirks**
```js
console.log(typeof null);
console.log(typeof NaN);
```

<details>
<summary>Show Answer</summary>

"object"  
"number"  

</details>

---

# 43. Hard Event Loop Puzzles (Very Tricky)

---

## **Q131**
```js
console.log("A");

setTimeout(() => console.log("B"), 0);

Promise.resolve().then(() => console.log("C"));

console.log("D");
```

<details>
<summary>Show Answer</summary>

A  
D  
C  
B  

</details>

---

## **Q132**
```js
setTimeout(() => console.log(1));

Promise.resolve().then(() => {
  console.log(2);
  Promise.resolve().then(() => console.log(3));
});

console.log(4);
```

<details>
<summary>Show Answer</summary>

4  
2  
3  
1  

</details>

---

## **Q133**
```js
console.log(1);

setTimeout(() => console.log(2));

Promise.resolve().then(() => { 
  console.log(3);
  setTimeout(() => console.log(4));
});

console.log(5);
```

<details>
<summary>Show Answer</summary>

1  
5  
3  
2  
4  

</details>

---

## **Q134**
```js
console.log("x");

queueMicrotask(() => console.log("y"));

console.log("z");
```

<details>
<summary>Show Answer</summary>

x  
z  
y  

</details>

---

# 44. Advanced Classes, Prototypes, and `new`

---

## **Q135: Behavior of `new`**
```js
function A() {
  this.value = 10;
}
const obj = new A();
console.log(obj.value);
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q136: `new` overriding return value**
```js
function A(){
  this.x = 1;
  return { y: 2 };
}
console.log(new A());
```

<details>
<summary>Show Answer</summary>

{ y: 2 }  
If constructor returns an object, that object is used instead of `this`.

</details>

---

## **Q137: Ignoring return value if not an object**
```js
function A(){
  this.x = 10;
  return 50;
}
console.log(new A());
```

<details>
<summary>Show Answer</summary>

{ x: 10 }  
If constructor returns a primitive, it is ignored.

</details>

---

## **Q138: Class inheritance**
```js
class A { say(){ return "A"; } }
class B extends A { say(){ return "B"; } }

console.log(new B().say());
```

<details>
<summary>Show Answer</summary>

"B"

</details>

---

## **Q139: super keyword**
```js
class A { say(){ return "A"; }}
class B extends A {
  say(){
    return super.say() + "B";
  }
}
console.log(new B().say());
```

<details>
<summary>Show Answer</summary>

"AB"

</details>

---

# 45. async/await – Hardcore

---

## **Q140**
```js
async function f(){
  console.log(1);
  await Promise.resolve();
  console.log(2);
}
console.log(3);
f();
console.log(4);
```

<details>
<summary>Show Answer</summary>

3  
1  
4  
2  

</details>

---

## **Q141**
```js
async function f(){
  return 10;
}
console.log(await f());
```

<details>
<summary>Show Answer</summary>

10

</details>

---

## **Q142**
```js
async function f(){
  await 10;
  return 20;
}
f().then(console.log);
```

<details>
<summary>Show Answer</summary>

20

</details>

---

## **Q143: await inside loop (performance issue)**
```js
async function f(){
  for(let i=0;i<3;i++){
    await console.log(i);
  }
}
f();
```

<details>
<summary>Show Answer</summary>

0  
1  
2  
Executes one by one — slow.

</details>

---

# 46. Advanced Promises

---

## **Q144: Promise.all**
```js
Promise.all([
  Promise.resolve(1),
  Promise.resolve(2)
]).then(console.log);
```

<details>
<summary>Show Answer</summary>

[1, 2]

</details>

---

## **Q145: Promise.race**
```js
Promise.race([
  new Promise(res => setTimeout(() => res(1), 50)),
  Promise.resolve(2)
]).then(console.log);
```

<details>
<summary>Show Answer</summary>

2  
Fastest win.

</details>

---

## **Q146: Promise.allSettled**
```js
Promise.allSettled([
  Promise.resolve(1),
  Promise.reject(2)
]).then(console.log);
```

<details>
<summary>Show Answer</summary>

[
  { status: "fulfilled", value: 1 },
  { status: "rejected", reason: 2 }
]

</details>

---

## **Q147: Promise chaining**
```js
Promise.resolve(10)
  .then(x => x * 2)
  .then(x => x + 5)
  .then(console.log);
```

<details>
<summary>Show Answer</summary>

25

</details>

---

# 47. V8 Internals & Optimization

---

## **Q148: What are hidden classes?**  
(Theory – visible)

Hidden classes are internal engine structures V8 uses to optimize property access and improve performance.  
Changing the "shape" of an object (adding properties in different order) de-optimizes it.

---

## **Q149: De-optimization example**
```js
const obj = {};
obj.a = 10;
obj.b = 20;
delete obj.a;
```

<details>
<summary>Show Answer</summary>

Deleting properties forces V8 to abandon hidden class optimization.

</details>

---

# 48. Final Ultra-Hard Puzzle

---

## **Q150**
```js
console.log([1,2,3] == "1,2,3");
console.log({a:1}.a === {a:1}.a);
console.log({a:1} === {a:1});
```

<details>
<summary>Show Answer</summary>

true  
true  
false  

Explanation:  
- `[1,2,3]` → "1,2,3"  
- `{a:1}.a` is just 1 on both sides  
- `{a:1}` !== `{a:1}` because different references  

</details>



