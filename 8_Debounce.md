# Debounce in JavaScript — Explained in *My Way*

> **Use case:** Search bar (or any input where users type fast)
>
> Goal: **Wait for the user to pause typing** before making an expensive call (API, search, filter, etc.)

---

## The Exact Code (unchanged)

```js
// use case: Search bar
// wait for specific time after user pauses
function debouce(fn, delay){
    let timer; //will form a closure
    //wait for longere pause, specified delay
    return (query)=>{
        clearTimeout(timer); //clear previous calls
        timer = setTimeout(()=>{
            fn(query);
        },delay)
    }
}

const search = (query)=>{
    console.log(`Searching for`, query);
}

const searhingWithDebouce = debouce(search,10);
searhingWithDebouce('j')
searhingWithDebouce('ja')
searhingWithDebouce("jav");
searhingWithDebouce("java");
searhingWithDebouce('javas')
searhingWithDebouce('javasc')
searhingWithDebouce("javascr");
searhingWithDebouce("javascri");
searhingWithDebouce("javascrip");
searhingWithDebouce("javascript");
````

---

## First: What problem are we solving?

Imagine this scenario:

* User types **j → ja → jav → java → javascript**
* If we call an API on **every keystroke**, we waste:

  * network calls
  * backend resources
  * UI performance

👉 We only want to **search once**, when the user **stops typing**.

That’s exactly what **debounce** does.

---

## The core idea (plain English)

> "I will wait for *X milliseconds* after the **last user action**.
> If another action happens before X ms, I reset the timer."

So only the **final action survives**.

---

## Step-by-step explanation (line by line, logically)

### 1️⃣ `debouce` is a higher-order function

```js
function debouce(fn, delay)
```

* Takes a function (`fn`) → `search`
* Takes a delay → `10ms`
* Returns a **new function**

This is important because **we don’t debounce immediately** — we debounce **calls to the function**.

---

### 2️⃣ `timer` lives in a closure

```js
let timer; // will form a closure
```

* `timer` is **not inside the returned function**
* So it **persists between calls**
* Every keystroke shares the same `timer`

This is the heart of debounce.

> No closure = no debounce.

---

### 3️⃣ Returned function runs on every user input

```js
return (query) => {
```

Think of this as:

> "This function is called every time the user types something"

---

### 4️⃣ Cancel the previous timer

```js
clearTimeout(timer);
```

* If user types again **before delay finishes**
* We cancel the previous scheduled call

This line ensures:

> ❌ Old calls don’t execute

---

### 5️⃣ Start a new timer

```js
timer = setTimeout(() => {
    fn(query);
}, delay)
```

* Start a fresh timer
* If user doesn’t type for `delay` ms
* Finally call the original function (`search`)

This ensures:

> ✅ Only the **last input** triggers the function

---

## What actually happens in your example

### Calls fired rapidly:

```js
searhingWithDebouce('j')
searhingWithDebouce('ja')
searhingWithDebouce('jav')
...
searhingWithDebouce('javascript')
```

Each call:

* Clears the previous timer
* Starts a new one

Because calls happen **faster than 10ms**, all previous timers die.

### Final result:

```js
Searching for javascript
```

Only **one log**, not ten.

---

## Why this is SDE-2 level important

* Prevents unnecessary API calls
* Improves frontend performance
* Reduces backend load
* Used everywhere: search, resize, scroll, filters

In real apps, delay is usually:

* `300ms` – `500ms`

---

## Mental model (remember this)

> **Debounce = “Wait… are you done?”**

If user keeps talking → keep waiting.
If user stops → act.

---

## Common mistake to avoid

❌ Creating `timer` inside returned function

```js
return () => {
   let timer; // WRONG
}
```

This breaks debounce because timer resets every call.

---

## One-line summary

> Debounce delays execution until the user **stops triggering the event**, ensuring only the **final intent** is processed.

```
```
