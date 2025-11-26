## 1. React Philosophy & Basics

### **Q1: What is React? Explain its core design principles.**  
 

React is a **declarative**, **component-based** UI library for building user interfaces. Core design principles:

- **Declarative UI**: describe what the UI should look like for a given state; React updates the DOM to match.
- **Component composition**: UI is built from small, reusable components.
- **One-way data flow**: state flows top → down via props; changes propagate predictably.
- **Virtual DOM**: React keeps an in-memory representation (VDOM) and diffs it to compute minimal DOM updates.
- **Unidirectional rendering model & pure functions**: components are pure functions of props/state (ideally).

**Interview Tip:** emphasize tradeoffs — React is a *library* (view layer), not a full framework.

---

### **Q2: What problems does React solve compared to direct DOM manipulation?**  
 

- Simplifies UI updates by expressing UI as functions of state instead of manually mutating the DOM.
- Minimizes layout thrashing and expensive DOM operations via batched updates and VDOM diffing.
- Encourages composability and testable components.
- Provides an ecosystem for routing, data fetching, state management, and build tooling.

---

## 2. JSX & Compilation

### **Q3: What is JSX? Why use it and what does it compile to?**  
 

JSX is syntax sugar for `React.createElement(...)` calls — a compact way to write element trees inside JS. It improves developer ergonomics, enables static analysis and tooling, and co-locates markup and logic.

**Example compile idea:**
`<div id="x">Hi</div>` → `React.createElement('div', { id: 'x' }, 'Hi')`.

**Pitfalls:** remember JSX is JavaScript (expressions), not HTML. `className`, `htmlFor`, fragments, and keys are special considerations.

---

### **Q4: JSX Expression Output**
```js
const name = "Leo";
const el = <div>{name ? <span>{name}</span> : null}</div>;
console.log(typeof el);
```

<details>
<summary>Show Answer</summary>

`object` — JSX evaluates to a React element object (not a DOM node). In runtime, it's an object describing type, props, and children.

</details>

---

## 3. Components (Functional vs Class)

### **Q5: Functional vs Class components — differences, and why functional is preferred today?**  
 

- **Functional components**: plain functions returning JSX. With hooks they can manage state, side effects, refs, and lifecycle-equivalent behavior.
- **Class components**: ES6 classes extending `React.Component`. Historically necessary for local state and lifecycle methods (`componentDidMount`), but hooks made functional components more powerful.
- Functional are preferred for simpler syntax, easier testing, and hooks composability.

**Interview Tip:** mention Error Boundaries remain class-based (until hook-based alternatives are standard).

---

### **Q6: Component identity and pure components**
```js
function Child({value}) {
  console.log("Child render");
  return <div>{value}</div>;
}
const MemoChild = React.memo(Child);

function Parent() {
  const [count, setCount] = React.useState(0);
  const obj = { n: 1 };
  return (
    <>
      <MemoChild value={obj} />
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
    </>
  );
}
```

<details>
<summary>Show Answer</summary>

Even though `MemoChild` is memoized, it will re-render every `Parent` render because `obj` is a new object literal each render (reference changes). To prevent, memoize `obj` or pass a stable primitive.

</details>

---

## 4. Props, State & Data Flow

### **Q7: Props vs State — responsibilities and best practices.**  
 

- **Props**: read-only inputs to a component; owned by parent; immutable from child’s perspective.
- **State**: local, private to a component; change via setters; triggers re-renders.
- **Best practices**: lift state to the nearest common ancestor when siblings must share data; avoid overusing global state; keep state minimal and canonical (avoid duplicated/derived state).

---

### **Q8: Lifting State Example**
```js
function Child({value, onChange}) {
  return <input value={value} onChange={e => onChange(e.target.value)} />;
}
function Parent() {
  const [name, setName] = React.useState("");
  return <Child value={name} onChange={setName} />;
}
```

<details>
<summary>Show Answer</summary>

This shows lifting: the `Parent` owns `name`, passes down value + setter; `Child` is controlled and stateless.

</details>

---

## 5. Rendering, Reconciliation & Keys

### **Q9: How does reconciliation work at a high level?**  
 

React compares previous and next VDOM trees to compute the smallest set of DOM mutations. It uses heuristics like:
- same component type → update props
- lists rely on keys to match items; otherwise fallback uses index-based heuristic
- fiber architecture allows incremental updates & prioritization (React 16+)

**Interview Tip:** explain importance of keys and stable identity.

---

### **Q10: Keys & List Reordering (Output)**
```js
function List({items}) {
  return (
    <ul>
      {items.map((it, i) => (
        <li key={i}>
          <input defaultValue={it} />
        </li>
      ))}
    </ul>
  );
}
```

<details>
<summary>Show Answer</summary>

Using index as key will break input focus when `items` are reordered or spliced. Use stable unique ids (`key={it.id}`) to preserve component identity.

</details>

---

## 6. Lifecycle (Mapping hooks ⇄ class lifecycles)

### **Q11: Map class lifecycle methods to hooks equivalents.**  
 

- `componentDidMount` → `useEffect(() => {...}, [])`
- `componentDidUpdate` → `useEffect(() => {...}, [deps])`
- `componentWillUnmount` → cleanup function from `useEffect`
- `getDerivedStateFromProps` → rarely needed; use derived data in render or memoization
- `componentDidCatch` → Error Boundary (class)

---

### **Q12: useEffect dependency pitfalls (Output)**
```js
function Comp({query}) {
  React.useEffect(() => {
    fetch(`/search?q=${query}`).then(/*...*/);
  }, []); // intentional bug
  return null;
}
```

<details>
<summary>Show Answer</summary>

Bug: effect depends on `query` but dependency array is empty — will run only once, not on query change. Correct fix: include `[query]`. Also consider cancellation with AbortController to avoid race conditions.

</details>

---

## 7. Hooks — Rules & Rationale

### **Q13: Rules of Hooks — explain and why they exist.**  
 

- Call hooks at the top-level of React functions (no conditional or loop calls).
- Call hooks only from React function components or custom hooks.
- Rationale: hooks rely on stable call order to map internal hook state; breaking rules corrupts internal hook state arrays.

**Interview Tip:** mention ESLint plugin `eslint-plugin-react-hooks` to enforce rules.

---

## 8. Core Hooks — Detailed

### **Q14: `useState` deep explanation (batching, functional updates, lazy initializer).**  
 

- `useState(initial)` returns `[state,setState]`.
- `setState` can accept a value or functional updater `setState(prev => next)`.
- React may **batch** multiple state updates inside event handlers or lifecycle to reduce re-renders.
- Lazy initializer: `useState(() => expensiveInit())` computes init only once.

---

### **Q15: `useState` stale closure trap (Output)**
```js
function Counter() {
  const [count, setCount] = React.useState(0);
  React.useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <div>{count}</div>;
}
```

<details>
<summary>Show Answer</summary>

Bug: closure captures initial `count` (0); setCount(count + 1) always sets to 1. Fix: use functional updater `setCount(c => c + 1)` or include `count` in deps to recreate interval.

</details>

---

### **Q16: `useEffect` deep (cleanup, dependencies, ordering)**  
 

- `useEffect` runs after render and after the browser paints.
- Cleanup returned function runs before next effect invocation and on unmount.
- Ordering: multiple effects run in order they are declared.
- For layout reads/DOM measurements use `useLayoutEffect` — runs synchronously after DOM mutations but before paint.

---

### **Q17: `useRef` practical uses**  
 

- Holding DOM node refs: `ref={el => myRef.current = el}` or `useRef(null)`.
- Holding mutable values that persist across renders without causing re-renders (e.g., timers, previous values).
- Accessing instance-like data in functional components.
- Use `forwardRef` to pass refs down.

---

### **Q18: `useMemo` vs `useCallback` vs `React.memo` (concepts & cost/benefit)**  
 

- `useMemo(() => compute(), [deps])` memoizes computed value.
- `useCallback(fn, deps)` memoizes function identity.
- `React.memo(Component)` shallowly compares props to avoid re-render.
- Overuse is a common anti-pattern: memoization incurs memory & comparison cost — use when there’s measurable performance gain (expensive compute or stable references needed for memoized children).

---

### **Q19: `useCallback` identity example (Output)**
```js
const Parent = () => {
  const [n, setN] = React.useState(0);
  const onClick = () => console.log("clicked");
  return <Child onClick={onClick} />;
};
const Child = React.memo(({onClick}) => {
  console.log("Child render");
  return <button onClick={onClick}>Click</button>;
});
```

<details>
<summary>Show Answer</summary>

Child will re-render every Parent render because `onClick` is a new function each render. Fix: wrap `onClick` in `useCallback(()=>console.log("clicked"), [])`.

</details>

---

### **Q20: `useReducer` when to choose and example**  
(Theory + code)

Use `useReducer` for complex state updates, many state transitions, or when you want reducer-like clarity locally.

```js
function reducer(state, action) {
  switch(action.type) {
    case 'increment': return {count: state.count + 1};
    case 'reset': return {count: 0};
    default: return state;
  }
}
function Counter() {
  const [state, dispatch] = React.useReducer(reducer, {count: 0});
  return <>
    <div>{state.count}</div>
    <button onClick={() => dispatch({type: 'increment'})}>+</button>
    <button onClick={() => dispatch({type: 'reset'})}>reset</button>
  </>;
}
```

---

### **Q21: `useContext` pitfalls & performance**  
 

- Context provides value to any consumer under its provider; value changes re-render all consuming components.
- Avoid putting frequently changing objects directly as provider value — memoize provider value (`useMemo`) or split contexts (e.g., `AuthContext`, `ThemeContext`).
- Use selectors or local state for high-frequency updates.

---

### **Q22: `useImperativeHandle` & `forwardRef` example**
```js
const FancyInput = React.forwardRef((props, ref) => {
  const inputRef = React.useRef();
  React.useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => (inputRef.current.value = '')
  }));
  return <input ref={inputRef} {...props} />;
});

// usage
function Parent() {
  const ref = React.useRef();
  return <>
    <FancyInput ref={ref} />
    <button onClick={() => ref.current.focus()}>Focus</button>
  </>;
}
```

<details>
<summary>Show Answer</summary>

This demonstrates exposing a controlled imperative API from a child — use sparingly; prefer declarative props when possible.

</details>

---

## 9. Custom Hooks & Reusability

### **Q23: What are custom hooks and best practices?**  
 

- Custom hooks are functions prefixed with `use` that compose hook logic for reuse.
- Keep them focused (single responsibility).
- Return stable references (memoize callbacks/values if consumer depends on identity).
- Avoid side-effects during render; use `useEffect` inside hook for effects.

---

### **Q24: Example `usePrevious` custom hook**
```js
function usePrevious(value) {
  const ref = React.useRef();
  React.useEffect(() => { ref.current = value; }, [value]);
  return ref.current;
}
```

<details>
<summary>Show Answer</summary>

`usePrevious` returns the previous render's value. Useful for comparing props/state.

</details>

---

## 10. Performance Patterns

### **Q25: When to optimize rendering?**  
 

- Optimize when there are measurable performance issues (lag, jank, large lists).
- Use profiling tools (React DevTools profiler, browser devtools) to find bottlenecks.
- Common optimizations: memoization (`React.memo`), avoiding unnecessary re-renders, virtualization for lists, code-splitting, lazy loading, and avoiding inline object/func creations when they cause child re-renders.

---

### **Q26: Virtualization concept & example libraries**  
 

Virtualization (windowing) renders only visible items in a list. Libraries: `react-window`, `react-virtualized`, `@tanstack/react-virtual`. Consider variable heights, accessibility, and scroll anchoring.

---

### **Q27: Code-splitting with React.lazy & Suspense (Output)**
```js
const Heavy = React.lazy(() => import('./Heavy'));
function App() {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <Heavy />
    </React.Suspense>
  );
}
```

<details>
<summary>Show Answer</summary>

`React.lazy` dynamically imports a component; `Suspense` provides a fallback while loading. For SSR, Suspense needs server-side support (Next.js or server components).

</details>

---

## 11. Controlled vs Uncontrolled Components & Forms

### **Q28: Controlled vs Uncontrolled form inputs**  
 

- **Controlled**: value bound to state, `value` + `onChange`. Easier validation and synchronous updates.
- **Uncontrolled**: use `ref` to read DOM value. Better for large forms or when minimal re-renders are desired.
- Libraries like `react-hook-form` provide performant controlled/uncontrolled hybrid approaches.

---

### **Q29: Controlled component example**
```js
function Form() {
  const [name, setName] = React.useState('');
  return <input value={name} onChange={e => setName(e.target.value)} />;
}
```

<details>
<summary>Show Answer</summary>

This input is controlled by React state; each keystroke updates state and triggers a re-render.

</details>

---

## 12. Events, Synthetic Events & Pooling

### **Q30: What are Synthetic Events?**  
 

React’s SyntheticEvent wraps native browser events to provide a consistent cross-browser interface. Historically events were pooled (re-used), requiring `event.persist()` to keep them — modern React relaxes pooling but treat events as ephemeral.

---

### **Q31: Event pooling trap (Output)**
```js
function Comp() {
  function handle(e) {
    setTimeout(() => console.log(e.target.value), 100); // potential trap
  }
  return <input onChange={handle} />;
}
```

<details>
<summary>Show Answer</summary>

`e.target` may be null/undefined in older React versions due to pooling. Solution: capture value `const v = e.target.value; setTimeout(()=>console.log(v),100);` or call `e.persist()`.

</details>

---

## 13. Error Boundaries & Error Handling

### **Q32: What are Error Boundaries? How do they work and what they don't catch?**  
 

- Error Boundaries are class components implementing `static getDerivedStateFromError` and `componentDidCatch` to catch render-time errors in the subtree and show fallback UI.
- They **do not** catch errors in event handlers, async callbacks, server-side code, or errors thrown in error boundary itself.
- Best practice: use boundaries at widget-level to avoid full-app crashes.

---

### **Q33: Error Boundary example**
```js
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  componentDidCatch(err, info) { /* log */ }
  render() { return this.state.hasError ? <div>Fallback</div> : this.props.children; }
}
```

<details>
<summary>Show Answer</summary>

Wrap risky components with `<ErrorBoundary><Risky/></ErrorBoundary>` to display fallback UI when children throw during render.

</details>

---

## 14. React 18 & Concurrent Features (Concept-heavy)

### **Q34: What is concurrent rendering (React 18)?**  
 

Concurrent rendering enables React to prepare multiple versions of UI and prioritize updates. It improves responsiveness by allowing React to interrupt rendering work and continue with higher-priority tasks (like user input). APIs: `startTransition`, `useTransition`, `useDeferredValue`. It's opt-in via features like `createRoot`/concurrent mode.

---

### **Q35: `startTransition` usage**
```js
const [query, setQuery] = React.useState('');
const [results, setResults] = React.useState([]);
const [isPending, startTransition] = React.useTransition();

function onChange(q) {
  setQuery(q); // urgent
  startTransition(() => {
    // low priority — update heavy state
    setResults(expensiveFilter(q));
  });
}
```

<details>
<summary>Show Answer</summary>

Marking the results update as non-urgent keeps input snappy while React schedules the expensive update at lower priority.

</details>

---

### **Q36: `useDeferredValue` and use case**  
 

`useDeferredValue(value)` returns a deferred version of `value` that lags behind; useful for heavy computations triggered by fast-changing input (e.g., live search). It helps avoid showing intermediate expensive renders.

---

## 15. Suspense for Data Fetching & Server Components (Concept)

### **Q37: Suspense for data fetching — concept and limitations**  
 

Suspense allows declarative loading states for components that read asynchronous data. On client, libraries like Relay or React Query integrate Suspense. On server, React Server Components and streaming SSR use Suspense boundaries. Limitations: older data-fetching patterns require adapter libraries; SSR needs framework support.

---

### **Q38: Example conceptual Suspense**
```js
// Pseudocode (requires suspending data fetch)
function Profile() {
  const user = useUser(userId); // may throw a Promise
  return <div>{user.name}</div>;
}
<Suspense fallback={<Loading/>}>
  <Profile />
</Suspense>
```

<details>
<summary>Show Answer</summary>

If `useUser` suspends (throws a Promise), Suspense shows the fallback until data resolves. Real usage needs libraries that integrate with Suspense.

</details>

---

## 16. Server-Side Rendering, Hydration & SEO (Concept)

### **Q39: SSR vs CSR vs SSG — when to choose which?**  
 

- **CSR**: client does all rendering; good for highly interactive apps where SEO is not primary.
- **SSR**: server renders HTML per request; useful for SEO, faster first paint for content pages.
- **SSG**: pre-render pages at build time (static); best for marketing sites and pages with infrequent data changes.
- **ISR** (Next.js): mix of SSG and incremental updates.

---

### **Q40: Hydration pitfalls & mismatch**
```js
// server-side HTML contains <div data-count="0">0</div> 
// client re-renders with different initial state
```

<details>
<summary>Show Answer</summary>

Hydration mismatch occurs when server HTML differs from client-rendered content (different initial data or non-deterministic rendering). Avoid by ensuring deterministic server render and synchronizing initial state via serialized props.

</details>

---

# React Interview Master – Part 2  
### Questions 41–80 (Context, State Management, Routing, Forms, Refs, Performance, Testing, SSR, Suspense)

---

## 17. Context & State Sharing

### **Q41: When to use Context vs when not to?**  
 

Use Context for cross-cutting concerns like theme, locale, authentication status — values that many components need to read. Avoid using Context for high-frequency changing state (e.g., fine-grained UI state) because provider value changes trigger re-renders for all consumers. For complex global state consider state managers (Redux, Zustand) or splitting contexts to minimize updates.

**Interview Tip:** Mention using derived selectors and memoized provider values to reduce re-renders.

---

### **Q42: Avoiding unnecessary re-renders with Context (Output / Pattern)**
```js
const AuthContext = React.createContext();
function App() {
  const [user, setUser] = React.useState(null);
  const value = { user, setUser }; // <-- problem: new object each render
  return <AuthContext.Provider value={value}><Routes/></AuthContext.Provider>;
}
```

<details>
<summary>Show Answer</summary>

Problem: `value` is a new object each render → consumers always re-render. Fix by memoizing: `const value = React.useMemo(() => ({user, setUser}), [user])`, or split into `UserContext` and `SetUserContext` to avoid re-renders when only setter changes.

</details>

---

### **Q43: Context selector pattern**  
 

A pattern (or library feature) that lets consumers subscribe to specific slices of context value to avoid re-rendering on unrelated changes. Implementations: custom selector hooks, or libraries like `use-context-selector`.

---

## 18. State Management Landscape

### **Q44: When to use Redux? What does Redux Toolkit add?**  
 

Use Redux for large apps with complex shared state, predictable state transitions, time-travel debugging, or lots of non-UI side effects. Redux Toolkit (RTK) reduces boilerplate: `configureStore`, `createSlice`, `createAsyncThunk`, and integrates Immer so reducers can use "mutative" syntax while producing immutable updates.

---

### **Q45: Local vs Global state decision checklist**  
 

Ask: Is the state needed by many components (global)? Does it outlive the component? Is it important for debugging or persistence? Prefer local state for UI components or ephemeral forms; use context or global stores for cross-cutting and app-wide state.

---

### **Q46: Example RTK slice (code)**
```js
// counterSlice.js
import { createSlice } from '@reduxjs/toolkit';
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment(state) { state.value += 1; }, // Immer handles immutability
    decrement(state) { state.value -= 1; }
  }
});
export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

<details>
<summary>Show Answer</summary>

RTK uses Immer so reducer code appears mutative but returns immutable updates. Use `configureStore` to combine reducers and enable devtools.

</details>

---

### **Q47: Alternatives — Zustand & Recoil (concepts)**  
 

- **Zustand**: tiny, hook-based store with minimal API, no boilerplate, direct usage via hooks.
- **Recoil**: atom-based state graph, derived selectors — nice for complex dependency graphs.
- Choose tools based on team familiarity and app needs.

---

## 19. Routing & Code Splitting

### **Q48: React Router basics and nested routes (code)**  
```js
// App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="users" element={<UserList />} />
          <Route path="users/:id" element={<UserDetail />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

<details>
<summary>Show Answer</summary>

`<Outlet />` is used inside `Layout` to render nested children. Use `useParams()` to read route params. Lazy-load route components to reduce initial bundle size.

</details>

---

### **Q49: Route-based code splitting & prefetching**  
 

Use `React.lazy` + `Suspense` or tools like `loadable-components` for SSR-friendly splitting. Prefetch on hover or intersection (link prefetch) to improve perceived performance.

---

## 20. Forms & Validation

### **Q50: Tradeoffs: controlled vs uncontrolled inputs (concept)**  
 

- **Controlled**: full control, easy validation, but more re-renders.
- **Uncontrolled**: read values via refs on submit, fewer re-renders; use `react-hook-form` for performant controlled/uncontrolled hybrid.

---

### **Q51: React Hook Form short example**
```js
import { useForm } from 'react-hook-form';
function MyForm() {
  const { register, handleSubmit } = useForm();
  const onSubmit = data => console.log(data);
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      <button>Submit</button>
    </form>
  );
}
```

<details>
<summary>Show Answer</summary>

`react-hook-form` minimizes re-renders by registering inputs and reading values on demand; excellent for large forms.

</details>

---

### **Q52: Debounced validation pattern (concept)**  
 

For expensive validation (e.g., remote username check), debounce calls to avoid firing on every keystroke. Use `useRef` for timers and `useEffect` for cleanup.

---

## 21. Refs, forwardRef & Imperative Handles

### **Q53: forwardRef rationale & example**
```js
const Input = React.forwardRef((props, ref) => <input ref={ref} {...props} />);
function Parent() {
  const ref = React.useRef();
  return <Input ref={ref} />;
}
```

<details>
<summary>Show Answer</summary>

`forwardRef` passes a parent ref to a child functional component. Use `useImperativeHandle` to expose a controlled API rather than raw DOM.

</details>

---

### **Q54: useImperativeHandle example**
```js
const Fancy = React.forwardRef((props, ref) => {
  const inputRef = React.useRef();
  React.useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus()
  }));
  return <input ref={inputRef} />;
});
```

<details>
<summary>Show Answer</summary>

Exposes `focus()` to parent while hiding internal implementation. Use sparingly—prefer declarative props.

</details>

---

### **Q55: When to use refs (list)**  
 

- DOM measurements & focus management  
- Integrating non-React libraries (e.g., D3)  
- Storing mutable values or timers without triggering re-renders  
- Avoid using refs for cross-component state — use state or context.

---

## 22. Rendering Optimization & Memoization

### **Q56: React.memo & shallow comparison caveats**  
 

`React.memo` shallowly compares props. Passing new object/array/function references causes re-renders despite memo. Use `useMemo` / `useCallback` to stabilize identities when necessary.

---

### **Q57: Example preventing re-render with useCallback & useMemo (code)**
```js
function Parent({items}) {
  const [count, setCount] = React.useState(0);
  const onClick = React.useCallback(() => setCount(c => c + 1), []);
  const memoItems = React.useMemo(() => items.map(i => i*2), [items]);
  return <Child onClick={onClick} items={memoItems} />;
}
const Child = React.memo(({onClick, items}) => {
  // renders only when onClick or items changes
});
```

<details>
<summary>Show Answer</summary>

This reduces unnecessary child re-renders by stabilizing prop references.

</details>

---

### **Q58: When not to optimize**  
 

Optimize only after profiling. Premature optimization (adding many memos) increases complexity and memory usage.

---

## 23. Large Lists & Virtualization

### **Q59: Why virtualization matters & pitfalls**  
 

Virtualization reduces DOM nodes by rendering only visible items. Pitfalls: handling dynamic heights, keyboard navigation, preserving scroll position, virtualization + window resizing. Test accessibility.

---

### **Q60: react-window basic usage**
```js
import { FixedSizeList as List } from 'react-window';
function Row({ index, style }) { return <div style={style}>Row {index}</div>; }
<List height={150} itemCount={1000} itemSize={35} width={300}>
  {Row}
</List>
```

<details>
<summary>Show Answer</summary>

`react-window` renders only visible rows; `style` must be forwarded to row container. For variable heights use `VariableSizeList`.

</details>

---

## 24. Profiling & Performance Tools

### **Q61: React Profiler — how to use & what to look for**  
 

Use React DevTools Profiler to record interactions, measure component render times, identify wasted renders. Look for frequently re-rendered components, long commit times, and components with large render trees.

---

### **Q62: Browser performance tools**  
 

Use Chrome DevTools Performance, Lighthouse, and flamegraphs. Look at paint/layout time, long tasks, and memory leaks. Correlate React renders with browser paint times.

---

## 25. Concurrency & React 18 Deep

### **Q63: useTransition vs startTransition vs useDeferredValue (concept)**  
 

- `startTransition(fn)`: marks updates in `fn` as non-urgent; React can interrupt low-priority work.
- `useTransition()` returns `[isPending, startTransition]` to indicate pending transitions.
- `useDeferredValue(value)` returns a deferred copy of `value` that lags to avoid expensive immediate renders.

Use for heavy list filtering or expensive renders to improve perceived responsiveness.

---

### **Q64: Example useTransition (code)**
```js
function Search() {
  const [q, setQ] = React.useState('');
  const [isPending, startTransition] = React.useTransition();
  const [results, setResults] = React.useState([]);
  function onChange(e) {
    const val = e.target.value;
    setQ(val);
    startTransition(() => {
      setResults(expensiveFilter(val));
    });
  }
  return <input value={q} onChange={onChange} />;
}
```

<details>
<summary>Show Answer</summary>

`setQ` is urgent (keeps input responsive); `setResults` is non-urgent and can be deferred.

</details>

---

### **Q65: StrictMode double render — why and implications**  
 

In development, `StrictMode` intentionally mounts, unmounts, and re-mounts components to surface side-effect bugs and prepare for concurrent features. Do not rely on side-effects during render — always use effects.

---

## 26. Suspense & Data Fetching (Advanced Conceptual)

### **Q66: Suspense for data fetching — how it works conceptually**  
 

When a component "suspends" (throws a Promise), React climbs to the nearest Suspense boundary and shows fallback UI until the Promise resolves. Libraries or frameworks provide fetcher hooks that integrate with Suspense.

---

### **Q67: Suspense pitfalls**  
 

- Not all data libraries support Suspense out-of-the-box.
- Avoid long blocking boundaries that freeze UI.
- For SSR, Suspense needs server support; client-only Suspense is for code splitting primarily.

---

## 27. Server Components & RSC (Conceptual)

### **Q68: What are React Server Components (RSC)?**  
 

RSC let parts of UI render on the server and send serialized component trees to the client, reducing client JS bundle. They can access server resources (DB) directly and return UI without shipping logic to the client.

---

### **Q69: When to use Server Components**  
 

Use for data-heavy, non-interactive parts of page (e.g., product lists), to minimize client JS and improve TTI (time to interactive).

---

## 28. Hydration, SSR & Hydration Mismatches

### **Q70: Common causes of hydration mismatch (list)**  
 

- Non-deterministic rendering (Date.now(), Math.random())  
- Browser-only APIs used during server render (window, localStorage)  
- Race conditions with async data  
- Different initial state on server vs client

---

### **Q71: Guarding against mismatch (pattern)**
```js
// example: render only on client
function ClientOnly({children}) {
  const [mounted, setMounted] = React.useState(false);
  React.useEffect(() => setMounted(true), []);
  return mounted ? children : null;
}
```

<details>
<summary>Show Answer</summary>

`ClientOnly` prevents server rendering of non-deterministic UI until client mounts. Use sparingly: impacts performance and SEO.

</details>

---

## 29. Testing React

### **Q72: Unit tests vs integration vs e2e (React context)**  
 

- **Unit tests**: isolated component logic (small, fast) — Jest + shallow render (rare).
- **Integration tests**: user interactions with components — React Testing Library (RTL) encourages testing behavior.
- **E2E**: full app flow — Cypress/Playwright.

---

### **Q73: RTL best practices**  
 

- Query by accessibility (role, labelText) not implementation details.  
- Simulate real user events (`userEvent`) instead of `fireEvent` where possible.  
- Avoid snapshot tests as sole assurance — prefer behavior checks.

---

### **Q74: Testing async effects (code)**
```js
import { render, screen, waitFor } from '@testing-library/react';
test('loads and displays data', async () => {
  render(<MyComponent />);
  await waitFor(() => expect(screen.getByText(/loaded/i)).toBeInTheDocument());
});
```

<details>
<summary>Show Answer</summary>

`waitFor` and `findBy*` queries help test async behaviors; mock network calls (msw) to control responses.

</details>

---

## 30. Accessibility (a11y)

### **Q75: Core a11y practices for React**  
 

- Use semantic elements (`button`, `nav`, `header`)  
- Ensure keyboard navigation (tab order, focus management)  
- Provide ARIA attributes only when necessary  
- Label inputs (`label` + `htmlFor`) and test with screen readers

---

### **Q76: Focus management example (code)**
```js
function Modal({open, onClose}) {
  const ref = React.useRef();
  React.useEffect(() => {
    if (open) ref.current?.focus();
  }, [open]);
  return open ? <div role="dialog" tabIndex={-1} ref={ref}>Modal</div> : null;
}
```

<details>
<summary>Show Answer</summary>

Setting `tabIndex={-1}` and focusing the dialog improves keyboard accessibility; manage focus return on close.

</details>

---

## 31. TypeScript with React (Concept)

### **Q77: Why use TypeScript with React?**  
 

TypeScript adds static typing, better editor tooling, fewer runtime bugs, and self-documenting APIs. Use `FC` with caution — prefer explicit prop types over `React.FC` if needing more precise typing.

---

### **Q78: Example typed props (code)**
```ts
type ButtonProps = { onClick: () => void; label: string };
function Button({onClick, label}: ButtonProps) {
  return <button onClick={onClick}>{label}</button>;
}
```

<details>
<summary>Show Answer</summary>

Explicit prop types improve maintainability. For complex hooks, type return values (tuples) and generics for reusability.

</details>

---

## 32. Debugging Strategies

### **Q79: Debugging render issues checklist**  
 

- Use React DevTools to inspect props/state and component tree.  
- Use Profiler to see render durations & frequent renders.  
- Log renders (`console.count`) or use `why-did-you-render` during development.  
- Pinpoint by commenting out subtrees or isolating components.

---

### **Q80: Example `why-did-you-render` usage**
 

Install `@welldone-software/why-did-you-render` in dev, configure to detect unnecessary re-renders by comparing props and component types. Useful to find identity issues.

---



# 33. Advanced Component Patterns

---

### **Q81: What is the “Render Props” pattern? Why was it used before hooks?**  
 

Render props were used to share logic between components before hooks. A component accepts a function as a prop and calls it to render UI. This allows logic to be reused (like data fetching, subscriptions) without inheritance or HOCs.

Hooks largely replaced render props, making logic reuse cleaner.

---

### **Q82: Render Props Example**
```js
function DataProvider({children}) {
  const [data, setData] = React.useState(null);
  React.useEffect(() => {
    fetch("/api").then(res => res.json()).then(setData);
  }, []);
  return children(data);
}

function App() {
  return (
    <DataProvider>
      {data => <div>{data ? data.msg : "Loading..."}</div>}
    </DataProvider>
  );
}
```

<details>
<summary>Show Answer</summary>

`DataProvider` provides data through a function prop. Useful pre-hooks; now replaced by custom hooks.

</details>

---

### **Q83: What are Higher-Order Components (HOCs)?**  
 

HOCs are functions that take a component and return a new component with enhanced behavior (e.g., logging, permissions, subscriptions). Example: `connect` from Redux.  
Hooks reduced the need for HOCs, but they still appear in older codebases and third-party libraries.

---

### **Q84: HOC Example**
```js
function withLogger(Component) {
  return function Wrapper(props) {
    console.log("rendering", Component.name);
    return <Component {...props} />;
  };
}
```

<details>
<summary>Show Answer</summary>

Enhances components by logging renders. Not ideal for modern apps unless necessary — hooks preferred.

</details>

---

### **Q85: Compound Component Pattern — why use it?**  
 

Allows parent and children components to share implicit state. Example: `<Dropdown><Dropdown.Button/><Dropdown.Menu/></Dropdown>`.

Good for UI libraries; consumers don’t need to manually pass props down. Implemented via Context or React.Children utilities.

---

### **Q86: Example Compound Component**
```js
const TabsContext = React.createContext();

function Tabs({children}) {
  const [active, setActive] = React.useState(0);
  return (
    <TabsContext.Provider value={{active, setActive}}>
      {children}
    </TabsContext.Provider>
  );
}

Tabs.Button = function({index, children}) {
  const {active, setActive} = React.useContext(TabsContext);
  return (
    <button
      style={{fontWeight: active===index?"bold":"normal"}}
      onClick={() => setActive(index)}
    >
      {children}
    </button>
  );
};
```

<details>
<summary>Show Answer</summary>

Compound components share state implicitly via Context — great for UI toolkits.

</details>

---

# 34. React Fiber & Internals

---

### **Q87: What is React Fiber and why was it introduced?**  
 

Fiber is React’s internal reimplementation of the reconciliation algorithm. Goals:

- Break rendering into **interruptible** units  
- Prioritize updates (urgent vs non-urgent)  
- Enable Concurrent Mode (React 18)  
- Scheduling, pausing, resuming rendering  
- Avoid blocking the main thread

Each fiber = a JS object representing a component instance & its work.

---

### **Q88: What are the two phases of the Fiber process?**  
 

1. **Render (Reconciliation) Phase**  
   - Build the work-in-progress fiber tree  
   - Calculate changes (diff)  
   - Can be paused/interrupted  
2. **Commit Phase**  
   - Apply DOM changes  
   - Run layout effects  
   - **Cannot** be interrupted

---

### **Q89: How does React decide what work is high or low priority?**  
 

React assigns lanes (priority levels) to updates:

- High priority (clicks, typing)
- Normal updates (state changes)
- Low priority (transition updates)
- Idle tasks (prefetching, pre-render)

React 18 allows `startTransition` to mark low priority work.

---

### **Q90: What is a Fiber Node (structure-level)?**  
 

A fiber node has fields like:

- `type` (component type)
- `pendingProps` / `memoizedProps`
- `child`, `sibling`, `return`  
- `flags` (effect tags)
- `stateNode` (DOM node or class instance)
- `updateQueue`

It tracks work progress, parent/child relationships, and effects.

---

### **Q91: Why is Fiber a linked list tree instead of plain JS objects?**  
 

Linked lists allow React to **pause**, **resume**, and **traverse** the tree efficiently during scheduling.  
Sibling pointers make traversal constant-time without recursion (helps avoid call stack overflow).

---

# 35. Diffing & Reconciliation Details

---

### **Q92: How does React’s diffing algorithm work for elements?**  
 

React uses heuristics:

- If element `type` changes → destroy previous subtree, build a new one.
- If `type` is same → reuse previous node, update props.
- For lists → keys determine element identity.

React avoids an O(n³) algorithm by assuming:

- Components of the same type with stable keys represent the same UI element.
- Insertions/deletions happen near the edges.

---

### **Q93: Why keys are crucial in lists?**  
 

Keys ensure child component identity across renders. Wrong keys cause:

- Wrong state association  
- Input focus loss  
- Incorrect animations  

Index as key breaks when list mutates.

---

### **Q94: Output — Key importance**
```js
const arr = ["A", "B", "C"];
// assume arr becomes ["B", "C", "A"]
```

<details>
<summary>Show Answer</summary>

With index keys, React reuses DOM incorrectly → moves state/history incorrectly. With stable IDs, React correctly reorders without remounting.

</details>

---

### **Q95: Why React avoids diffing children deeply?**  
 

Deep diffing is expensive (n³). React uses a O(n) heuristic based on type and keys. It prioritizes performance over perfect diff accuracy.

---

# 36. useEffect Internals & Timing

---

### **Q96: Why does useEffect run after paint?**  
 

React schedules effects after DOM mutations, allowing the browser to paint first. Effects should not block rendering. Use `useLayoutEffect` for synchronous layout reads before paint.

---

### **Q97: Output – useEffect ordering**
```js
function App() {
  React.useEffect(() => console.log("effect1"));
  React.useEffect(() => console.log("effect2"));
  return <div>Hi</div>;
}
```

<details>
<summary>Show Answer</summary>

`effect1`  
`effect2`  
Effects run in order they appear.

</details>

---

### **Q98: Cleanup timing**
```js
React.useEffect(() => {
  console.log("effect");
  return () => console.log("cleanup");
}, [val]);
```

<details>
<summary>Show Answer</summary>

When `val` changes:  
1. Run cleanup of old effect  
2. Run new effect  

Cleanup never runs on initial render.

</details>

---

### **Q99: Why closing over stale state happens?**  
 

Effects capture values from the render they belong to. If you reference state inside an effect without listing dependencies, you get stale values.

---

# 37. Scheduling & Performance Internals

---

### **Q100: What is React Scheduler?**  
 

The Scheduler (package inside React) controls rendering priority, yielding back to the browser, and managing deadlines. It powers Concurrent Mode by breaking work into chunks.

---

### **Q101: What is “time slicing”?**  
 

React splits rendering work into small units and yields to the browser to keep the UI responsive. Allows interruption and resuming work.

---

### **Q102: Why async rendering improves perceived performance?**  
 

Urgent tasks (typing) can interrupt heavy background tasks (list rendering). Old sync rendering blocked the main thread until the entire tree was reconciled.

---

### **Q103: Output — async rendering example (conceptual)**
```js
startTransition(() => {
  // render 10,000 items
});
```

<details>
<summary>Show Answer</summary>

React marks heavy work as non-urgent and renders it when idle, keeping UI responsive during transitions.

</details>

---

# 38. Suspense & Streaming SSR (Deep)

---

### **Q104: How Streaming SSR works in React 18?**  
 

- Server renders HTML in chunks  
- Suspense boundaries allow streaming fallback HTML  
- Once data resolves, server streams the real content  
- Client hydrates incrementally  
Allows faster TTFB and partial hydration.

---

### **Q105: Suspense boundary hydration example**
```jsx
<Suspense fallback={<Loading/>}>
  <Profile />
</Suspense>
```

<details>
<summary>Show Answer</summary>

On server: fallback is streamed immediately.  
When `Profile` resolves, server streams HTML to replace fallback.  
On client: hydration attaches event handlers lazily.

</details>

---

### **Q106: Why Suspense works only with frameworks for SSR (Next.js)?**  
 

Because the server must know how to catch suspended promises and stream boundaries. React alone cannot fetch data on the server; frameworks integrate data fetchers with Suspense.

---

# 39. Cache, Memoization & Derived State

---

### **Q107: Why avoid derived state?**  
 

Derived state duplicates data (e.g., `filteredList`). It risks inconsistencies. Prefer computing on-the-fly with `useMemo`.

---

### **Q108: Derived state bug**
```js
const [items, setItems] = useState([]);
const [filtered, setFiltered] = useState(items);
```

<details>
<summary>Show Answer</summary>

`filtered` does not update when `items` changes → inconsistent UI. Prefer `useMemo(() => items.filter(...), [items])`.

</details>

---

### **Q109: useMemo misuse**
```js
const doubled = useMemo(() => arr.map(x => x*2), []);
```

<details>
<summary>Show Answer</summary>

Bug: missing dependency `arr`. Leads to stale result.

</details>

---

### **Q110: Memo cost vs benefit?**  
 

`useMemo` caches values; recomputing a cheap value is often faster than memoizing it. Use memo for expensive computations or to maintain stable references.

---

# 40. Concurrent Updates & Race Conditions

---

### **Q111: Why race conditions occur in React?**  
 

React may start rendering version A of UI, but before commit, state updates to version B appear. Without proper cancellation, older async responses may overwrite newer render.

---

### **Q112: Example race condition**
```js
useEffect(() => {
  let active = true;
  fetch(`/search?q=${query}`).then(res => res.json()).then(data => {
    if (active) setResults(data);
  });
  return () => (active = false);
}, [query]);
```

<details>
<summary>Show Answer</summary>

The `active` flag ensures stale network responses don't update state after a new search query triggers a new request.

</details>

---

# 41. Ref Stability & Layout Measurements

---

### **Q113: useLayoutEffect vs useEffect**  
 

- `useLayoutEffect`: runs **synchronously after DOM mutations** but before browser paints; used for layout measurements.  
- `useEffect`: runs after paint; non-blocking.

---

### **Q114: Measuring element size**
```js
const ref = useRef();
useLayoutEffect(() => {
  const { width } = ref.current.getBoundingClientRect();
  console.log(width);
}, []);
```

<details>
<summary>Show Answer</summary>

Using `useLayoutEffect` ensures the measurement is accurate before paint (no flicker).

</details>

---

# 42. Memory Leaks & Cleanup

---

### **Q115: Common causes of memory leaks in React**  
 

- Forgetting to clear intervals or timeouts  
- Stale async operations updating unmounted components  
- Event listeners not removed  
- Retaining references in closures beyond lifecycle

---

### **Q116: Memory leak example**
```js
useEffect(() => {
  const id = setInterval(() => console.log("tick"), 1000);
  return () => clearInterval(id);
}, []);
```

<details>
<summary>Show Answer</summary>

Cleanup ensures interval stops on unmount — prevents leaks.

</details>

---

# 43. Architecture & Best Practices

---

### **Q117: Smart vs Dumb components (modern view)**  
 

Old pattern: smart = stateful, dumb = presentational.  
Modern view: any component can hold state; prefer colocating state and logic near where it's used. Use hooks to reuse logic rather than smart/dumb segregation.

---

### **Q118: Co-location principle**  
 

Move logic (state, handlers) closer to where they're actually used. Prevents global state explosion and improves maintainability.

---

### **Q119: Why avoid prop drilling?**  
 

Prop drilling makes components harder to maintain and refactor. Use context or local/global state when props pass through many unnecessary layers.

---

### **Q120: Example avoiding prop drilling**
```js
const ThemeContext = createContext();
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Layout />
    </ThemeContext.Provider>
  );
}
```

<details>
<summary>Show Answer</summary>

Children can read theme directly via `useContext`, avoiding redundant props.

</details>

---


# 44. React Priorities, Lanes & Scheduling

---

### **Q121: What are React lanes? Why were they introduced?**  
 

Lanes are React’s way of grouping updates by priority. Priorities determine:

- Which updates can interrupt others  
- Which updates get rendered first  
- How React schedules urgent vs non-urgent work  

Each update gets a lane; multiple lanes may be worked on concurrently. They enabled fine-grained control over scheduling in React 18.

---

### **Q122: Lane examples in React 18**
 

- Input events → **high priority**  
- Rendering expensive UI filters → **transition priority**  
- Prefetches → **idle lanes**  
- useDeferredValue updates → **deferred lanes**

This lets heavy work run without blocking urgent user interactions.

---

### **Q123: Why React may render the same component multiple times in development?**  
 

React StrictMode intentionally double-invokes:

- Component body  
- Effects cleanup + rerun  

This surfaces bugs in side effects and prepares components for concurrent rendering. It does NOT happen in production.

---

### **Q124: Output — Strange double rendering**
```js
function App() {
  console.log("render");
  return <div>Hi</div>;
}
```

<details>
<summary>Show Answer</summary>

In development, "render" prints twice due to StrictMode. In production, only once.

</details>

---

# 45. React Server Components (RSC) — Deep Dive

---

### **Q125: What problem do React Server Components solve?**  
 

- Reduce client JavaScript by executing logic on the server  
- Fetch data directly from server (DB calls inside components)  
- Improve performance (no client bundles for server-only components)  
- Allow streaming UI from server  
- Avoid hydration cost for parts of the UI  

RSC is **not SSR**; it renders *component trees* on server, not HTML.

---

### **Q126: What cannot be done in Server Components?**  
 

- Cannot use client-side hooks (useState, useEffect, etc.)  
- Cannot access browser APIs  
- Cannot attach event listeners  
- Must not rely on client-specific state  
- Must be pure and side-effect–free (except server interactions)

---

### **Q127: Example of a Server Component (Next.js 13+)**
```js
// app/user/page.jsx
export default async function UserPage() {
  const user = await fetchUser(); // server fetch
  return <div>{user.name}</div>;
}
```

<details>
<summary>Show Answer</summary>

This component runs entirely on the server and ships only serialized UI to the client — not its logic.

</details>

---

### **Q128: What are Client Components in the RSC model?**  
 

Client Components use `"use client"` directive and:

- Allow state, effects, refs  
- Run in browser  
- Are hydrated  
- Can wrap interactive UI sections  
- Can import Server Components, but not vice versa

---

# 46. React Hydration & SSR Edge Cases

---

### **Q129: What is hydration?**  
 

Hydration = attaching event listeners and React internals to server-rendered HTML. React compares server HTML with the first client render. If mismatched → hydration warning.

---

### **Q130: Output — Hydration mismatch**
```jsx
// server renders: <div>Count: 0</div>
// client initial state: 1
```

<details>
<summary>Show Answer</summary>

React warns:  
**“Text content does not match server-rendered HTML…”**  
Fix: ensure server and client initial states match (serialize props or fetch before render).

</details>

---

### **Q131: Why hydration errors are dangerous?**  
 

They cause:

- Broken event listeners  
- Detached UI  
- Unpredictable UI transitions  
- Client override of server HTML leading to flickers

---

# 47. Anti-Patterns in React

---

### **Q132: List the top React anti-patterns**  
 

- Storing derived state unnecessarily  
- Mutating state directly  
- Overusing context for frequently changing values  
- Excessive renders due to unstable object/function props  
- Running effects that should be computations  
- Using refs for state  
- Fetching data in render  
- Huge component files (poor separation of concerns)

---

### **Q133: Output — Mutating state accidentally**
```js
const [arr, setArr] = useState([1,2,3]);
arr.push(4);
setArr(arr);
console.log(arr);
```

<details>
<summary>Show Answer</summary>

Mutating directly causes React to think nothing changed (same reference), so it may skip re-render. Correct: `setArr([...arr, 4])`.

</details>

---

### **Q134: Why doing heavy computation inside render is bad?**  
 

Render should be pure and fast. Heavy computation will slow every render. Move such logic into:

- useMemo  
- useTransition  
- worker threads  
- server components

---

# 48. Advanced React Performance Engineering

---

### **Q135: What is render “bailout” in React?**  
 

When React detects no changes in props/state, it bails out and skips re-render for that fiber. Used heavily by React.memo, PureComponent, and useMemo/useCallback.

---

### **Q136: Output — Bailout example**
```js
const Child = React.memo(({value}) => {
  console.log("child render");
  return <div>{value}</div>;
});
function App() {
  const [n, setN] = useState(0);
  return <>
    <Child value="fixed" />
    <button onClick={() => setN(n+1)}>+</button>
  </>;
}
```

<details>
<summary>Show Answer</summary>

Child renders only once because props never change → bailout.

</details>

---

### **Q137: What is priority inversion in React?**  
 

When a low-priority update blocks a high-priority update — React avoids this by interrupting rendering to handle urgent events. Without concurrent rendering, long renders would freeze UI.

---

### **Q138: When does React recreate component identity?**  
 

- Changing the `key`  
- Changing component type  
- Removing and re-adding component in tree  
- Conditional rendering changes the order/structure

---

# 49. React & Browser Rendering Pipeline

---

### **Q139: How does React fit into the browser paint cycle?**  
 

Steps:

1. React schedules work  
2. Reconciliation (render phase)  
3. Commit → DOM mutations  
4. Browser layout  
5. Browser paint  
6. Browser idle time → more work scheduled  

React tries to avoid blocking layout/paint.

---

### **Q140: Why `useLayoutEffect` may cause jank?**  
 

It blocks painting until the effect runs. Use sparingly and switch to useEffect unless layout measurement requires sync execution.

---

# 50. React DOM & Synthetic Events (Deep)

---

### **Q141: How does React implement event delegation?**  
 

React attaches a single event listener at the root (document or root element).  
Events bubble to React’s listener where it runs handlers according to its internal synthetic event system.

Benefits:

- Performance  
- Cross-browser consistency  
- No need to attach listeners to every DOM node  

---

### **Q142: Output — Event propagation**
```js
function App() {
  return (
    <div onClick={() => console.log("parent")}>
      <button onClick={() => console.log("child")}>Click</button>
    </div>
  );
}
```

<details>
<summary>Show Answer</summary>

Clicking button outputs:  
child  
parent  
Because bubbling.

</details>

---

### **Q143: Why event.stopPropagation() must be used carefully?**  
 

It prevents parent handlers from running — sometimes needed but often breaks composability. Better alternatives: event delegation redesign, handling event origin inside handler.

---

# 51. Extreme Edge Cases

---

### **Q144: Why calling setState during render is bad?**
```js
function App() {
  const [n, setN] = useState(0);
  setN(5);
  return <div>{n}</div>;
}
```

<details>
<summary>Show Answer</summary>

Infinite re-render loop. setState inside render causes new renders repeatedly.

</details>

---

### **Q145: Output — Parent controlled vs child local state**
```js
function Child({value}) {
  const [local, setLocal] = useState(value);
  return <div>{local}</div>;
}
function App() {
  return <Child value={10} />;
}
```

<details>
<summary>Show Answer</summary>

Child initializes local state from prop *once*. It will **not update** when parent prop changes. Avoid copying props into state unless necessary.

</details>

---

### **Q146: Why not store functions inside state?**  
 

Causes unnecessary re-renders and breaks the expected identity semantics. Instead, define functions inside component or memoize if needed.

---

# 52. React Architecture & Large Apps

---

### **Q147: Recommended folder structure for large React apps**  
 

- group by feature/domain  
- colocate components, hooks, tests, styles  
- avoid giant `/components` folder  
- use `/shared` for utilities  
- use `/state` for global stores  
- route-based code-splitting  

---

### **Q148: Why feature-based structure is superior?**  
 

Improves cohesion, reduces mental overhead, increases maintainability, and scales better for teams.

---

### **Q149: Micro-frontend with React — high-level idea**  
 

Split app into smaller independent bundles that can be deployed separately. Tools:

- Module Federation  
- Single-SPA  
- Webpack federation plugins  

Trade-offs: performance overhead, shared dependencies, consistent UI patterns required.

---

### **Q150: Most important React interview concepts to master**  
 

- Hooks (all deep concepts)  
- Reconciliation & Fiber  
- Suspense & Concurrent features  
- Memoization & rendering optimization  
- State management (Context, Redux, Zustand)  
- SSR, hydration, RSC basics  
- Performance patterns & pitfalls  
- Large-scale architecture  

---
``

