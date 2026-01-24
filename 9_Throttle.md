# Throttle in JavaScript — Explained in *My Way*

> **Use case:** Slow mode in YouTube live chat
> 
> Goal: **Allow an action only once in a fixed time window**, no matter how many times the user tries.

---

## The Exact Code (unchanged)

```js
// use case: slow mode in you tube live sessions, it will disable the comments for something 60 seconds

//throttling: execute the function only after certain delay. it doesn't matter how many times user has interupted

function throttle(fn, delay){
    let prev = 0;
    return(msg)=>{
        if(Date.now()-prev<delay) return; //as the timer has not completed
        prev = Date.now();            
        fn(msg); //else call the function
    }
}

function sendMessage(msg){
    console.log(`Sending Message: ${msg}`);
}

let throttledMode = throttle(sendMessage,1000);

throttledMode('hi');
throttledMode('hi hello');
throttledMode('hi again');
throttledMode('hello again');
throttledMode('hello its me');
throttledMode('hi spamming');
````

---

## First: What problem are we solving?

Imagine a **live chat / comment system**:

* Users spam messages rapidly
* Backend gets flooded
* Chat becomes unreadable

In **slow mode**, the rule is simple:

> "You can send **only one message per X seconds**."

That’s exactly what **throttling** enforces.

---

## Core idea (plain English)

> "I don’t care how many times you try.
> I will execute this function **at most once per fixed time window**."

This is the opposite mindset of debounce.

---

## Step-by-step explanation (your logical flow)

### 1️⃣ `throttle` is a higher-order function

```js
function throttle(fn, delay)
```

* Accepts a function (`sendMessage`)
* Accepts a delay (`1000ms`)
* Returns a **controlled version** of that function

---

### 2️⃣ `prev` stores the last execution time

```js
let prev = 0;
```

* This variable **persists across calls** (closure)
* Stores the timestamp of the **last successful execution**

This is the memory that throttle relies on.

---

### 3️⃣ Returned function runs on every user attempt

```js
return (msg) => {
```

Every time the user tries to send a message, this function runs.

---

### 4️⃣ Time check (the gatekeeper)

```js
if(Date.now() - prev < delay) return;
```

Logic:

* `Date.now()` → current time
* `prev` → last allowed execution time

If:

* **current time − last time < delay**

👉 We **block** the call.

This is the slow-mode rule in code.

---

### 5️⃣ Update timestamp & execute

```js
prev = Date.now();
fn(msg);
```

* Update `prev` to current time
* Execute the function

This marks the start of the next time window.

---

## What happens in your example

### Calls fired rapidly:

```js
throttledMode('hi');         // executes
throttledMode('hi hello');  // ignored
throttledMode('hi again');  // ignored
throttledMode('hello again'); // ignored
...
```

Only the **first call** in the 1-second window executes.

If user waits **1 second**, next message is allowed.

---

## Why throttle is different from debounce

| Debounce               | Throttle                  |
| ---------------------- | ------------------------- |
| Waits for user to stop | Limits execution rate     |
| Executes last action   | Executes first action     |
| Search input           | Live chat, scroll, resize |

---

## Why this is SDE-2 important

Throttle is used in:

* chat systems
* scroll handlers
* resize events
* rate-limiting UI actions

If done wrong → performance bugs.

---

## Mental model (remember this)

> **Throttle = “Not more than once per window.”**

User spams → ignored.
Timer passes → allowed.

---

## Common mistake to avoid

❌ Resetting `prev` incorrectly
❌ Using `setTimeout` when timestamp logic is simpler
❌ Forgetting closure (moving `prev` inside returned function)

---

## One-line summary

> Throttle ensures a function executes **at most once per fixed interval**, regardless of how many times it’s triggered.



```
```
