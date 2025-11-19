# React Interview Questions — Topic‑wise Deep Notes

## 1. Virtual DOM, Fiber & Reconciliation

### 🧩 What is the DOM?

The **DOM (Document Object Model)** is a tree-like structure representing your HTML page. Updating the DOM is expensive because every update triggers:

* Style recalculation
* Layout generation
* Painting/repainting

### 🧩 Why Virtual DOM?

React introduced the **Virtual DOM (VDOM)** to avoid slow direct DOM manipulation.

#### How VDOM works:

1. React creates a **lightweight JS object copy** of the real DOM.
2. When state changes → React creates a *new* VDOM.
3. React compares old VDOM with new VDOM (**Diffing algorithm**).
4. Only the changed nodes are updated in the actual DOM (**Reconciliation**).

### 🚩 Problem with old Virtual DOM

The old VDOM diffing process was **synchronous**:

```
render → diff → commit
```

This caused issues:

* If UI was updating and another update arrived → React had to process everything in order.
* Continuous updates lead to frame drops and sluggish UI.

### ✅ Solution: React Fiber

React Fiber (React 16+) solved this by introducing **incremental rendering**.

### ⚛️ React Fiber — Key Features

* Breaks work into **small units of work (fibers)**.
* Can **pause, resume, or abort** work.
* Can **reuse already computed work**.
* Assigns **priority** to updates:

  * high priority → user typing, animations
  * low priority → background updates

### ☑️ Why React Fiber was needed

| Problem in old React             | Fiber Solution                 |
| -------------------------------- | ------------------------------ |
| Synchronous rendering blocked UI | Work split into chunks (async) |
| No priority                      | Priority-based scheduling      |
| No pausing                       | Pause / resume / drop work     |
| No suspense                      | Added Suspense & transitions   |

### 🧠 Summary

* **React does NOT update the UI immediately**.
* Updates may be batched.
* UI updates get **prioritized**.

---

## 2. Reconciliation Algorithm (Diffing)

Reconciliation = The algorithm React uses to decide **what to update** in the DOM.

### Rules of Diffing

#### 1️⃣ Different element type → Replace entire subtree

```jsx
<div/> → <span/>
// React throws away everything and rebuilds.
```

#### 2️⃣ Same element type → Diff props

```jsx
<div className="a"/> → <div className="b"/>
// Only update changed attributes.
```

#### 3️⃣ List Diffing → Uses **keys**

Keys must be:

* stable
* unique
* predictable

Bad keys = index-based keys
Good keys = unique IDs

### ⚠️ Why React Needs Keys

To understand *which* item:

* stayed
* moved
* updated
* was deleted

---

## 3. How React Rendering Works Internally

React rendering has **three phases**:

### 1️⃣ Render Phase (async)

* Creates a new VDOM
* Can be paused

### 2️⃣ Diff Phase (async)

* Compares old vs new VDOM (Fiber work)

### 3️⃣ Commit Phase (sync)

* Actual DOM updates
* Runs layout effects

```
Render → Diff → Commit
```

---

## 4. Hydration

Hydration = Attaching JS logic to server-rendered HTML.

### CSR (React.js)

* Blank HTML → JS loads → Page appears
* Slow first load
* Weak SEO

### SSR (Next.js)

* Server sends ready-made HTML
* Browser hydrates it → makes it interactive
* Better SEO
* Faster first paint

---

## 5. Hooks

Hooks let you use state and lifecycle in functional components.

Rules of Hooks:

* Only call at top-level
* Only inside components
* Never inside conditions or loops

### 5.1 useState

State updates are **asynchronous** and **batched**.

Example showing batching:

```jsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
// Output = +1 only
```

Correct solution:

```jsx
setCount(prev => prev + 1);
setCount(prev => prev + 1);
setCount(prev => prev + 1);
// Output = +3
```

### 5.2 useEffect

Used for side effects:

* API calls
* Subscriptions
* DOM updates
* Timers

Runs **after render → commit phase**.

#### Dependency rules:

* `[]` → run once
* `[var]` → run when var changes
* no deps → run on every render

#### Infinite loop example:

```jsx
useEffect(() => {
  setCount(count + 1);
}, [count]);
```

Fix with functional update:

```jsx
setCount(prev => prev + 1);
```

### 5.3 useContext

Used to avoid prop drilling.
Re-renders **all components under provider** when value changes.

Not ideal for large apps → use Redux or Zustand.

### 5.4 useReducer

Used when:

* state logic is complex
* multiple related values
* predictable updates needed

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

### 5.5 useMemo

* Memoizes a **value**
* Runs during render
* Avoids expensive re-computations

```jsx
const result = useMemo(() => slowFn(num), [num]);
```

### 5.6 useCallback

Memoizes a **function**.
Useful when passing functions to memoized children.

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

### 5.7 useRef

* Stores mutable values
* Does NOT re-render
* Perfect for timers, DOM refs, previous state

```jsx
const inputRef = useRef();
inputRef.current.focus();
```

---

## 6. React Router

Client-side navigation without page refresh.

### Basic Structure

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
  </Routes>
</BrowserRouter>
```

### Link vs NavLink

* `Link` → simple navigation
* `NavLink` → provides `isActive`, use for styling active routes

### useNavigate

Used for programmatic navigation.

```jsx
const navigate = useNavigate();
navigate("/about");
```

Also supports history navigation:

```jsx
navigate(-1); // back
navigate(1); // forward
```

### URL Params

```jsx
<Route path="/user/:id" element={<User />} />
```

Use:

```jsx
const { id } = useParams();
```

### Route Ranking

| Pattern      | Priority |
| ------------ | -------- |
| Exact static | Highest  |
| :param       | Medium   |
| *            | Lowest   |

### useLocation

Lets you read:

* pathname
* query params
* hash
* state

---

## Next Topics to Add

* useMatch
* Search Params
* `<Navigate />`
* Error Boundaries
* Nested Routes
* Layout Routes (`<Outlet />`)
* Protected Routes
* Relative Routing
* Index routes
* Catch-all routes
* Loaders & Actions (React Router Data API)
* Code Splitting (lazy + Suspense)
* Custom Hooks
* Pure Components
* Class Component Lifecycle

---

### 🔍 Examples, Illustrations & Hands‑on Snippets

Below are practical examples for the major topics already covered above. Use these in interviews or paste into a sandbox to run.

---

#### 1) Virtual DOM / Reconciliation — Minimal diff example

```jsx
// Old VDOM
const oldTree = (
  <ul>
    <li key="a">A</li>
    <li key="b">B</li>
  </ul>
);

// New VDOM — changed text of B and inserted C
const newTree = (
  <ul>
    <li key="a">A</li>
    <li key="b">B updated</li>
    <li key="c">C</li>
  </ul>
);

// React will: update text of key 'b' and append key 'c' — not re-create entire list.
```

**Talking points:** keys matter; if you used indices for keys and reordered items, React may recreate DOM nodes unnecessarily.

---

#### 2) Fiber / Priority — animation vs background update (concept)

```js
// Conceptual pseudocode
// High priority: user typing / input
// Low priority: fetching analytics in background

scheduler.scheduleCallback(HighPriority, () => {
  // urgent DOM update (typing feedback)
});

scheduler.scheduleCallback(LowPriority, () => {
  // fetch & render analytics widgets
});

// Fiber may pause low priority work while it finishes the typing update.
```

**Interview tip:** explain incremental rendering and ability to pause/resume work.

---

#### 3) useState batching & functional updates (live example)

```jsx
import React, {useState} from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  function handleBad() {
    // all three read the same stale `count`
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  }

  function handleGood() {
    // functional updates use the latest value
    setCount(c => c + 1);
    setCount(c => c + 1);
    setCount(c => c + 1);
  }

  return (
    <div>
      <p>{count}</p>
      <button onClick={handleBad}>Bad +3</button>
      <button onClick={handleGood}>Good +3</button>
    </div>
  );
}
```

---

#### 4) useEffect cleanup (timer) — preventing leaks

```jsx
import React, {useEffect, useState} from 'react';

function Timer() {
  const [t, setT] = useState(0);
  useEffect(() => {
    const id = setInterval(() => setT(x => x + 1), 1000);
    return () => clearInterval(id); // cleanup
  }, []);
  return <div>seconds: {t}</div>;
}
```

**Note:** cleanup runs before unmount and before re-running the effect.

---

#### 5) useMemo vs useCallback (practical)

```jsx
const expensive = (n) => { for(let i=0;i<1e8;i++); return n*2; }

function Parent(){
  const [n, setN] = useState(1);
  // useMemo caches the computed value until `n` changes
  const doubled = useMemo(() => expensive(n), [n]);

  // useCallback caches the function ref so memoized children don't re-render
  const onClick = useCallback(() => setN(x => x + 1), []);

  return <Child doubled={doubled} onClick={onClick} />
}
```

---

#### 6) useRef for previous value

```jsx
function PrevExample({value}){
  const prev = useRef(value);
  useEffect(()=>{ prev.current = value; }, [value]);
  return <div>now: {value}, prev: {prev.current}</div>
}
```

---

#### 7) useReducer example (complex state)

```jsx
const initial = {count:0, step:1};
function reducer(state, action){
  switch(action.type){
    case 'inc': return {...state, count: state.count + state.step};
    case 'setStep': return {...state, step: action.step};
    default: return state;
  }
}

function Counter(){
  const [state, dispatch] = useReducer(reducer, initial);
  return (
    <div>
      <p>{state.count}</p>
      <button onClick={()=>dispatch({type:'inc'})}>+{state.step}</button>
      <input type="number" onChange={e=>dispatch({type:'setStep', step: Number(e.target.value)})} />
    </div>
  )
}
```

---

#### 8) Custom Hook — `useWindowSize`

```jsx
import {useEffect, useState} from 'react';
function useWindowSize(){
  const [size, setSize] = useState([window.innerWidth, window.innerHeight]);
  useEffect(()=>{
    const onResize = ()=> setSize([window.innerWidth, window.innerHeight]);
    window.addEventListener('resize', onResize);
    return ()=> window.removeEventListener('resize', onResize);
  },[]);
  return size;
}

// usage
function App(){
  const [w,h] = useWindowSize();
  return <div>{w} x {h}</div>;
}
```

---

#### 9) React Router — nested routes & protected route

```jsx
// App.jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Layout />}>
      <Route index element={<Home/>} />
      <Route path="dashboard" element={<Protected><Dashboard/></Protected>} />
      <Route path="users">
        <Route index element={<Users/>} />
        <Route path=":id" element={<UserProfile/>} />
      </Route>
    </Route>
  </Routes>
</BrowserRouter>

// Protected wrapper
function Protected({children}){
  const auth = useAuth();
  const nav = useNavigate();
  useEffect(()=>{ if(!auth) nav('/login'); }, [auth]);
  return auth ? children : null;
}
```

---

#### 10) Code Splitting — lazy + Suspense

```jsx
const Heavy = React.lazy(()=> import('./Heavy'));
function App(){
  return (
    <Suspense fallback={<div>Loading…</div>}>
      <Heavy />
    </Suspense>
  );
}
```

---

### Quick Interview Cheat‑sheet (TL;DR)

* **VDOM**: JS representation of DOM → diff → patch.
* **Fiber**: incremental rendering, pausing, priorities.
* **Hooks rules**: top-level only, inside components.
* **useState**: batched updates → use functional updates when depending on previous state.
* **useEffect**: runs after commit; cleanup before unmount & before re-run.
* **useMemo/useCallback**: optimize computation & function identity; don’t overuse.
* **useRef**: mutable container, no re-renders.
* **useReducer**: complex state logic.
* **Router**: nested routes, layout routes, programmatic navigation.

---

Let me know if you want these examples extended into runnable CodeSandbox-ready files, or if you want diagrams/ASCII illustrations added for Fiber scheduling.
