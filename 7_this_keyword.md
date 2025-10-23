# 🚀 Understanding the `this` Keyword in JavaScript

The `this` keyword in JavaScript is one of the most misunderstood concepts. It behaves differently depending on **where** and **how** it’s used.

Let’s go step by step 👇

---

## 🌍 1. `this` in the Global Scope

```js
// In global space --> points to global object
console.log(this);
```

### ✅ Explanation:

* In **browsers**, the global object is `window`.
* In **Node.js**, the global object is `global`.
* So, `this` in global space refers to the global object.

Example:

```js
console.log(this === window); // true (in browsers)
```

---

## 🧩 2. `this` Inside a Function (Non-Strict Mode)

```js
function fn() {
  console.log(this); // window object
}
fn();
```

### ✅ Explanation:

* In **non-strict mode**, `this` inside a regular function refers to the **global object** (i.e., `window`).
* Why? Because of a rule called **`this` substitution**.

📘 **This Substitution Rule:**

> In non-strict mode, if `this` is `undefined` or `null`, JavaScript automatically replaces it with the global object.

---

## 🔒 3. `this` Inside a Function (Strict Mode)

```js
function fun() {
  "use strict";
  console.log(this); // undefined
}

fun();
window.fun();
```

### ✅ Explanation:

* In **strict mode**, JavaScript **disables this substitution**.
* So when a function is called **without any object reference**, `this` becomes **`undefined`**.
* But when the function is called **as a property of an object** (e.g., `window.fun()`), then `this` refers to that object (`window`).

---

## 🧱 4. `this` Inside an Object

```js
const obj = {
  a: 10,
  x: function () {
    console.log(this); // {a: 10, x: ƒ}
    console.log(this.a); // 10
  },
};
obj.x();
```

### ✅ Explanation:

* When a function is **called as a method of an object**, `this` refers to that **object**.
* Here, `this` → `obj`, and so `this.a` → `10`.

Example:

```js
const car = {
  brand: 'Tesla',
  getBrand() {
    return this.brand;
  }
};

console.log(car.getBrand()); // Tesla
```

---

## 🔁 5. `this` with `call`, `apply`, and `bind`

These methods allow us to **manually control the value of `this`**.

```js
const student = {
  name: "Antim",
  printName: function () {
    console.log(this.name);
  },
};

const person = { name: "Alex" };

student.printName(); // Antim
student.printName.call(person); // Alex
```

### ✅ Explanation:

* `call()` → calls the function immediately with a **custom `this`**.
* `apply()` → same as `call()` but takes **arguments as an array**.
* `bind()` → returns a **new function** with a permanently bound `this`.

Example:

```js
const greet = function() { console.log(`Hello ${this.name}`); };
const user = { name: "Leo" };

const boundGreet = greet.bind(user);
boundGreet(); // Hello Leo
```

---

## ⚡ 6. `this` Inside Arrow Functions

```js
const ob = {
  a: 10,
  x: () => {
    console.log(this); // window
  },
};
ob.x();
```

### ✅ Explanation:

* Arrow functions **do not have their own `this`**.
* They take `this` from their **lexical environment** (the surrounding scope where they were created).
* Since `x` is defined in the global scope, `this` refers to the **global object (window)**.

Another example:

```js
const obje = {
  a: 10,
  x: function () {
    const y = () => {
      console.log(this.a);
    };
    y();
  },
};

obje.x(); // 10
```

### 💡 Trick:

> Arrow functions look for their **nearest parent with a `this` binding** — this is called their **enclosing lexical context**.

---

## 🧠 7. `this` Inside the DOM

```html
<button onclick="console.log(this)">Click Me</button>
```

### ✅ Explanation:

* In event handlers, `this` refers to the **HTML element** that received the event.

Example:

```html
<button id="btn">Click</button>
<script>
  document.getElementById("btn").addEventListener("click", function() {
    console.log(this); // <button id="btn">Click</button>
  });
</script>
```

---

## 🧾 Summary Table

| Context                 | Value of `this`                  |
| ----------------------- | -------------------------------- |
| Global Scope (Browser)  | `window`                         |
| Global Scope (Node.js)  | `global`                         |
| Function (Non-Strict)   | `window` (due to substitution)   |
| Function (Strict)       | `undefined`                      |
| Method (Object)         | That Object                      |
| `call`, `apply`, `bind` | Explicitly Set Object            |
| Arrow Function          | Lexical `this` (from parent)     |
| DOM Event Handler       | The Element That Triggered Event |

---

## 🎯 Final Takeaway

* `this` is **not determined at definition time**, but **at call time**.
* Arrow functions inherit `this` from their **surrounding scope**.
* In strict mode, if `this` is not set, it remains **`undefined`**.
* In the DOM, `this` refers to the **element that invoked the handler**.

> 💬 Mastering `this` is essential for understanding how JavaScript handles context and object references.
