# ⚛️ Understanding `useEffect` in React — Complete Guide

## 🧩 What is `useEffect`?

`useEffect` is a **React Hook** that lets you perform **side effects** in your components.
A “side effect” means *anything that affects something outside the component*, like:

* Fetching data
* Manipulating the DOM directly
* Subscribing/unsubscribing to an event
* Setting up timers (like `setInterval`)

---

## ⚙️ Basic Syntax

```jsx
useEffect(() => {
  // 🧠 effect logic (runs after render)
  return () => {
    // 🧹 cleanup logic (runs before next effect or unmount)
  };
}, [dependencies]);
```

---

## 🧠 The React Lifecycle & `useEffect` Flow

| Stage                      | Description                                                | `useEffect` Behavior                                                         |
| :------------------------- | :--------------------------------------------------------- | :--------------------------------------------------------------------------- |
| 1️⃣ **Render (Mount)**     | React builds the UI based on your component’s JSX.         | `useEffect` **does NOT run yet**.                                            |
| 2️⃣ **Commit Phase**       | The DOM is updated with your UI.                           | Now, **React runs your `useEffect` callback.**                               |
| 3️⃣ **Re-render (Update)** | State or props change → component re-renders.              | React **compares dependencies** — if any changed, the effect runs **again.** |
| 4️⃣ **Cleanup**            | Before running the new effect or unmounting the component. | The **cleanup function runs** (if provided).                                 |

---

## 🧩 Example: Effect without dependencies

```jsx
useEffect(() => {
  console.log("Component rendered!");
});
```

📌 Runs **after every render**, because we didn’t give it a dependency array.

✅ **Use case:** Debugging, animations, or logging renders.
⚠️ **Downside:** Runs too often — can slow things down if misused.

---

## 🧩 Example: Effect with empty dependency array

```jsx
useEffect(() => {
  console.log("Component mounted!");

  return () => {
    console.log("Component unmounted!");
  };
}, []);
```

📌 Runs **only once** — after the component first mounts.
✅ Common for:

* Fetching data once
* Setting up event listeners
* Initializing timers

🧹 The cleanup runs when the component **unmounts**.

---

## 🧩 Example: Effect with dependencies

```jsx
useEffect(() => {
  console.log("Effect ran because count changed!");
}, [count]);
```

📌 Runs whenever **`count` changes**.
✅ Common for:

* Fetching data whenever input changes
* Syncing derived state
* Responding to prop updates

🧹 The cleanup runs **before** the next effect if dependencies change.

---

## 🧠 Visual Timeline

```
+----------------------------+------------------------+
|          STAGE             |        useEffect        |
+----------------------------+------------------------+
| 1️⃣ Component Mounts       | Effect runs after render |
| 2️⃣ Dependency Changes     | Cleanup → Effect runs again |
| 3️⃣ Component Unmounts     | Cleanup runs one final time |
+----------------------------+------------------------+
```

---

## 🔁 Example: API Call (Async + Cleanup)

```jsx
import { useEffect, useState } from "react";

function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    let isMounted = true; // flag to avoid setting state on unmounted component

    async function fetchUsers() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      if (isMounted) setUsers(data);
    }

    fetchUsers();

    return () => {
      isMounted = false; // 🧹 cleanup
      console.log("Cleanup: Component unmounted");
    };
  }, []);

  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### ⚡ What happens:

1. Component mounts → effect runs → data fetched.
2. While waiting, React may re-render.
3. If unmounted before fetch completes → cleanup flag prevents state update → avoids error.

---

## 🧭 Types of Dependencies and Their Effects

| Dependency Array  | Runs When               | Example                |
| :---------------- | :---------------------- | :--------------------- |
| `[]`              | Only once (mount)       | Initial fetch, setup   |
| `[stateVar]`      | When `stateVar` changes | React to user input    |
| None              | On every render         | Debugging or animation |
| `[prop1, state2]` | When either changes     | Re-sync logic          |

---

## 🧹 Cleanup Function — Why It’s Important

Without cleanup, old effects might overlap with new ones — causing:

* Memory leaks
* Multiple event listeners
* Duplicated intervals

Example 👇

```jsx
useEffect(() => {
  const interval = setInterval(() => console.log("Tick"), 1000);

  return () => {
    clearInterval(interval); // 🧹 prevents multiple intervals
  };
}, []);
```

---

## 🎯 Key Takeaways

| Concept               | Meaning                                             |
| :-------------------- | :-------------------------------------------------- |
| **Runs after render** | `useEffect` never blocks rendering.                 |
| **Dependency Array**  | Controls when effect runs.                          |
| **Cleanup Function**  | Runs before next effect or unmount.                 |
| **Side Effects**      | Fetching, subscriptions, DOM, timers.               |
| **Async Logic**       | Wrap inside an async function (not directly async). |

---

### ✅ Summary Visualization

```
Mount → Effect Runs → (Dependencies Change?) → Cleanup → Re-run → Unmount → Final Cleanup
```

---

> 💡 **Tip:** If you ever wonder *when your `useEffect` runs*, log messages inside the effect and cleanup. It’s the easiest way to visualize the flow while coding.
