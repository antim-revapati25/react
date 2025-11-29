# React + Redux Toolkit — Complete Guide (Single-file Markdown for GitHub)

---

## Table of Contents

1. [Introduction & Motivation](#introduction--motivation)
2. [What is Redux / Flux and Why Use It?](#what-is-redux--flux-and-why-use-it)
3. [Installation](#installation)
4. [Redux Data Flow — Visuals](#redux-data-flow--visuals)
5. [Sync Flow vs Async Flow (RTK)](#sync-flow-vs-async-flow-rtk)
6. [What is a Slice?](#what-is-a-slice)
7. [Reducers — Theory & RTK specifics](#reducers---theory--rtk-specifics)
8. [Step-by-step Implementation (Code)](#step-by-step-implementation-code)

   * [Step 1: Create a Slice](#step-1-create-a-slice)
   * [Step 2: Create the Store](#step-2-create-the-store)
   * [Step 3: Connect Store using Provider](#step-3-connect-store-using-provider)
   * [Step 4: Implement in React Components (useSelector & useDispatch)](#step-4-implement-in-react-components-useselector--usedispatch)
9. [Extra Notes & Best Practices](#extra-notes--best-practices)
10. [Common Gotchas & Troubleshooting](#common-gotchas--troubleshooting)

---

## Introduction & Motivation

**React + Redux Toolkit** is a robust pattern for predictable state management in medium-to-large React applications. This guide collects your notes, clarifies concepts, and presents them in a single Markdown file ready for GitHub.

Key idea: keep application state predictable, testable, and debug-friendly while avoiding prop drilling and scattered state updates.

---

## What is Redux / Flux and Why Use It?

* **Predictable state management library** — centralizes application state in a single store.
* **Flux** (pattern) was developed by Facebook as an architecture for unidirectional data flow; Redux follows the same core idea and formalizes it.

**Why use Redux / RTK?**

* Centralized state: single source of truth.
* Predictable state flow via actions & reducers.
* Easy debugging (time-travel debug, logging, devtools).
* Global access without prop drilling.

---

## Installation

```bash
npm install @reduxjs/toolkit react-redux
```

> `@reduxjs/toolkit` contains the official, opinionated utilities for using Redux. `react-redux` contains bindings for React (`Provider`, `useSelector`, `useDispatch`).

---

## Redux Data Flow — Visuals

Simple ASCII visual of UI → store flow for synchronous actions:

```
[UI Button] --dispatch --> [Action: increment()] --> [Slice reducer updates store] --> [Store] --> [UI reads new state via useSelector] --> re-render
```

A little more detailed diagram (sync):

```
Component
  └─ dispatch(action)
       ↓
     Action
       ↓
    Reducer(s)
       ↓
     Store (new state)
       ↓
  Components reading state via useSelector -> re-render
```

---

## Sync Flow vs Async Flow (RTK)

**Flow — for sync actions**

1. Component dispatches an action
2. Action goes to reducer
3. Reducer updates store
4. Updated state flows back to components

**Flow — for async actions (using createAsyncThunk / thunks)**

1. Component dispatches action (a thunk)
2. Thunk calls API or performs side effects
3. RTK handles `pending`, `fulfilled`, `rejected` lifecycle
4. UI updates based on state changes

RTK's `createAsyncThunk` automatically generates pending/fulfilled/rejected action types so you can handle them in `extraReducers`.

---

## What is a Slice?

A **slice** in RTK is a modular piece of Redux state: it bundles the slice's `name`, `initialState`, `reducers`, and generated `actions` (and optionally `extraReducers` for async cases) into one place.

**Slice contains:**

* `name`
* `initialState`
* `reducers` (sync logic)
* auto-generated `actions`
* `extraReducers` for async logic

Slices reduce boilerplate by auto-generating action creators and action types.

---

## Reducer — Theory & RTK specifics

* **Reducer** is a function that takes `(state, action)` and returns the new state.
* **Reducer must be pure**: no side-effects, no random values, no API calls inside reducers.
* In plain Redux, reducers must return a brand new state object (immutability enforced manually).
* **RTK uses Immer** internally. That lets you *write "mutating" code* (e.g., `state.value += 1`) while producing immutable updates under the hood.

**Important:** Writing code that *looks* mutating is OK inside `createSlice` reducers, but you are still producing immutable updates.

---

## Step-by-step Implementation (Code)

> We'll implement the exact code you provided, with minor corrections for typos (e.g., `initialState` spelling) and formatting for readability.

### Step 1: create slice

> **Note:** slice creates reducers and action creators for us.

**Basic slice structure**

```js
import { createSlice } from "@reduxjs/toolkit";

const initialState = {
  value: 0,
};

const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    // add reducer functions here
  },
});
```

**Complete code of slice**

```js
// src/mySlice/CounterSlice.js
import { createSlice } from "@reduxjs/toolkit";

// initial state
const initialState = {
  value: 0,
};

// contains name, initialState, reducers (logic)
// returns reducer function & actions
const counterSlice = createSlice({
  name: "counter", // used to prefix action types: "counter/increment"
  initialState, // slice's initial state

  // synchronous reducers - mutating code is allowed because of Immer
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
```

> Save this file as `src/mySlice/CounterSlice.js` (or any path you prefer).

---

### Step 2: create store

**Concept:** the store combines reducers into one global state. `configureStore` sets sensible defaults and includes middleware like thunk and devtools.

```js
// src/Store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./mySlice/CounterSlice"; // reducer generated by RTK

const store = configureStore({
  reducer: {
    // key: slice name, value: its reducer
    counter: counterReducer,
  },
});

export default store;
```

**Visual**: `[Slices] ---> [STORE] ---> [React App]`

---

### Step 3: connect store using Provider

**Provider** makes the store available to the entire app. It gives access to `useSelector` (to read state) and `useDispatch` (to dispatch actions).

```jsx
// src/RTKApp.js
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
```

Mount `RTK` at root (e.g., `index.js`) instead of plain `<App />`.

---

### Step 4: implement RTK in components (useSelector & useDispatch)

**useSelector**

* Access state slice values: `useSelector(state => state.sliceName.variable)`

**useDispatch**

* Returns `dispatch` function which you call with an action (e.g., `dispatch(increment())`).

```jsx
// src/Home.js
import React, { useState } from "react";
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement, addBy, reset } from "./mySlice/CounterSlice";

const Home = () => {
  // select value from store
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
          Add By
        </button>
      </div>
    </div>
  );
};

export default Home;
```

---

## Extra Notes & Best Practices

* Keep slices small and focused (e.g., `counterSlice`, `todosSlice`).
* Prefer `createAsyncThunk` for async operations — it creates pending/fulfilled/rejected action types for you.
* Use `selectors` for complex derived state (memoize with `createSelector` from `reselect` if needed).
* Keep side effects out of reducers; use thunks (or other middleware) instead.
* Use TypeScript types for safety in bigger codebases if possible.

### Example: Async flow with createAsyncThunk (short)

```js
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchTodos = createAsyncThunk("todos/fetch", async () => {
  const res = await fetch('/api/todos');
  return res.json();
});

const todosSlice = createSlice({
  name: 'todos',
  initialState: { items: [], loading: false, error: null },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchTodos.pending, (state) => { state.loading = true; })
      .addCase(fetchTodos.fulfilled, (state, action) => { state.items = action.payload; state.loading = false; })
      .addCase(fetchTodos.rejected, (state, action) => { state.loading = false; state.error = action.error.message; });
  }
});
```

---

## Common Gotchas & Troubleshooting

* **Typos**: `initialState` must be spelled correctly. Otherwise createSlice will not behave as expected.
* **Return vs Mutate**: Inside `createSlice` reducers you can mutate `state` directly; do not return a new state object *and* mutate — pick one pattern.
* **Hook order**: `useSelector` and `useDispatch` must be used inside React function components (not conditionally).
* **Provider**: If your components read `undefined` state, ensure `Provider` wraps them and the correct `store` is passed.
* **String payloads**: When dispatching `addBy`, ensure payload is a number (`dispatch(addBy(Number(value)))`).

---

### A final short recap (your original notes — preserved and explained)

* React redux

  * Predictable state management library
  * Flux was developed by facebook for state management
  * Central flow, predictable state flow, easy debugging, global access without prop drilling
  * Install: `npm install @reduxjs/toolkit` and `npm install react-redux`

* Flow (sync/async) — preserved exactly as you wrote it — explained and code shown above.

* Slice: state + reducers + actions in one file — shown above.

* Reducer: takes (state, action), must be pure; RTK uses Immer, so you can write mutating code which is turned into immutable updates.

---

*End of document.*
