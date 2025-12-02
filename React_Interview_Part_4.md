sentinel div

import React, { useEffect, useRef, useState } from "react";
import Card from "./Card";
import useFetch from "./useFetch";
const URL = "https://jsonplaceholder.typicode.com/posts";
const PaginationHome = () => {
  const { value, loading, err } = useFetch(URL);
  const [visibleCount, setVisibleCount] = useState(5);
  const loaderRef = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver((entries) => {
      if (entries[0].isIntersecting) {
        setVisibleCount((prev) => prev + 5);
      }
    });

    if (loaderRef.current) {
      observer.observe(loaderRef.current);
    }
  }, []);

  return (
    <div
      style={{
        height: "600px",
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
      {/* using sentinel(placeholdera) div */}
      <div
        ref={loaderRef}
        style={{ height: "40px", backgroundColor: "grey" }}
      ></div>
    </div>
  );
};

export default PaginationHome;


Lazy loading:  Suspense + React.lazy
Lazy loading means load something only when its needed
React.lazy will take a callback function in which it will import the components only when needed and returns the component
Suspense will show a fallback code untill the component is loaded
import React, { Suspense, useState } from "react";
const BigComponent = React.lazy(() => import("./BigComponent"));
const LazyLoadingHome = () => {
  const [show, setShow] = useState(false);
  const handleShow = () => {
    setShow((prev) => !prev);
  };
  return (
    <div>
      <h1>Hello from lazy</h1>
      <button onClick={handleShow}>Show</button>
      {show && (
        <Suspense fallback={<h1>I am fallback</h1>}>
          <BigComponent />
        </Suspense>
      )}
    </div>
  );
};

export default LazyLoadingHome;





code splitting
breaking down big bundles of code into chunks for fast loading. Will do later in detail
Debouncing & throttling
When we need to limit the rate of events firing like api calls on every keypress, every scroll then we use debouncing and throttling

Debounce: 
wait for the user to stop
use in: search bar, filtering, auto-save, form validation, resize event
Throttle: 
limit how often, runs after a fixed time
use in: Scroll events, window resize, infinite scroll, button spam protection, drag events

Debounce example:
User types  wait until they stop  then call API once

Server Components: React 18+ feature
Server componets don’t run on client like DB operations done on server lever
No Js bundle cost
Fast rendering

React Events
React does not use browser event directly, it wraps the event into Something known as Synthetic events, to maintain cross browser compatibility and standarized behaviour
Then React calls that even via synthetic events.
React delegrates events: 
A technique where you attach ONE listener to a parent instead of adding listeners to every child.
React does not attach event listeners on DOM elements, instead react puts a single handler at the root level, and from there React handles events
Flow:
You click a button
React native DOM event bubbles to react root
React intercepts it
React finds the fiber node for that element
React calls onClick function
This allows react to :- re-render efficiently, control propagation and more
Event pooling: was done before react 17,now stopped
Event pooling means events were re-used for performance
After handler function finished, event obj becomes empty, accessing it in async callbacks leads to broken code and we had to use e.persist()
But from React 17, events are safe in async callback
Event propagation: the order in which an event travels through the DOM tree. Phases are :-
Capturing: Top  down
Document  html  body  parent  child
addEventListener("click", handler, true) :- here tue will enable event capturing
Target: event reached the actual element clicked
Bubling: Bottom  up
Child  parent  body  html  document
Bubbling is default behavious in js
In short
Event Propagation = the path an event takes through the DOM (capture → target → bubble).
Bubbling = event moves upward from target to ancestors.
Capturing = event moves downward from ancestors to target (not default).
Delegation = one handler on a parent handles events from all children.
Default = Bubbling.



React Component lifecycle



1. Mounting phase (Component appears on screen)
In this phase, component gets initialized and then component/node gets inserted into DOM tree
Constructor: to initialize the states, bind methods
getDerivedStateFromProps: sync state from props
render: to render the componenet before mounting, return jsx
componentDidMount: 
to tell that component successfully mounted in the DOM. 
It will be run  only once after first rendering. 
Its same as useEffect with empty dependency array
used to fetch api, async operations
2. Updating phase (state/props chages)
Here node is in the DOM tree. Any updation will be done from here
getDerivedStateFromProps: gets the updated data from props
shouldComponentUpdate: to check if the updation required or not. It is for optimization
render: to render the updation in DOM tree
getSnapShotBeforeUpdate: to capture the DOM tree before updation
componentDidUpdate: 
To tell that componet get updated. 
It is same as useEffect with dependency array
For API calls based on change
3. unmounting phase  (component removed)
componentWillUnmount: 
Used to tell now component will be removed from DOM tree
Used to cleanup api calls and async functions, remove event listeners, cleanup time intervals
Equivalent to cleanup return inside useEffect

Error Boundary
An error boundary is a react component that catches js error in its child components and prevents the entire app from crashing.
They catch errors inside
Render
Lifecycle method
Functional component rendering
Errors caused by child components
They cant catch error inside
Event handlers
Async erros like setTimeout, promises
Server size errors
Errors in other async code
Error boundaries exist because if one component crashes, react by default unmounts the entire component tree, causing a white screen.
Error boundaries lets us show
Fallback UI
Custom error message
Loggin info
Instead of crashing the whole UI

Note: error boundaries are part of react class component and not functional components as error boundaries can handle errors in lifecycle methods which does not exists in functional components
What happens internally (VERY IMPORTANT)
A child component throws an error
React searches upward in the tree
Finds the nearest Error Boundary
Calls:
getDerivedStateFromError() → sets fallback
componentDidCatch() → logs
Displays fallback UI instead of crashing the whole app

Error Boundary in Functional Component
We cannot create a real boundary using Hooks alone
But we CAN simulate error boundaries using:
Third-party library (react-error-boundary)
Try/catch inside event handlers
Custom fallback components
Suspense boundaries (for async loading)


React Query
React query is a liberary for managing server state – the data that comes from APIs
It handles fetching, catching, updating, re-fetching, stale data, retries, background updates and syncronization automatically.

React query exists bcz in manual data fetching we need to do everything by overselves like, updating data using useState, useEffect, loading state, error state, handle stale data, cahing logic and so on

React query is for server state and not for UI state like toggles, form inputs and all
CORS
CORS is a browser security mechanism that blocks requests to a different origin (domain/port/protocol) unless the server explicitly allows it using special headers.
To prevent malicious websites from secretly calling APIs of other websites.


React
│
├── 1. Fundamentals
│     ├── What is React & Why
│     ├── SPA vs MPA
│     ├── JSX (syntax, compilation)
│     ├── Components (Functional vs Class)
│     ├── Props vs State
│     ├── One-way data flow
│     ├── Virtual DOM (basics)
│     └── Keys (why needed, index key issue)
│
├── 2. Hooks (Core)
│     ├── useState
│     │     ├── Batch updates
│     │     └── Functional updates
│     ├── useEffect
│     │     ├── Dependency array rules
│     │     ├── Cleanup
│     │     └── Infinite loops
│     ├── useRef
│     │     ├── DOM access
│     │     └── Mutable values (no re-render)
│     ├── useContext
│     ├── useMemo
│     ├── useCallback
│     ├── useReducer
│     ├── forwardRef
│     └── useImperativeHandle (advanced)
│
├── 3. Rendering & Reconciliation
│     ├── Why components re-render
│     ├── React Fiber basics
│     ├── Reconciliation algorithm
│     ├── Diffing algorithm
│     ├── React.memo
│     ├── Shallow vs Deep comparison
│     └── Prevent unnecessary re-renders
│
├── 4. Events
│     ├── Synthetic events
│     ├── Event pooling
│     ├── Bubbling & capturing
│     ├── Why event errors skip error boundaries
│     └── Why handlers don’t auto-re-render
│
├── 5. Lists & Keys
│     ├── .map() rendering
│     ├── Unique keys
│     ├── Why index key is bad
│     └── Conditional rendering
│
├── 6. Forms
│     ├── Controlled components
│     ├── Uncontrolled components
│     ├── useRef for uncontrolled input
│     └── Basic validation
│
├── 7. State Management
│     ├── Prop drilling
│     ├── useContext (global-ish state)
│     ├── Redux
│     │     ├── Store, actions, reducers
│     │     └── Thunk / middleware
│     ├── Redux Toolkit
│     │     ├── createSlice
│     │     └── Immer under the hood
│     └── Zustand (interview basics)
│
├── 8. Routing (React Router v6+)
│     ├── Routes & Route
│     ├── useNavigate
│     ├── useParams
│     ├── useLocation
│     ├── Nested routes
│     ├── Outlet
│     ├── Protected routes
│     └── Lazy loading routes
│
├── 9. Performance Optimization
│     ├── Code splitting
│     ├── lazy() + Suspense
│     ├── useMemo / useCallback use-cases
│     ├── React.memo issues
│     ├── Avoid re-render waterfall
│     └── Virtualization / Windowing
│           ├── react-window
│           ├── react-virtualized
│           └── Why used for large lists
│
├── 10. Error Handling
│     ├── Error boundaries (class)
│     ├── componentDidCatch
│     ├── What they CAN catch
│     └── What they CANNOT catch
│           ├── event handler errors
│           ├── async errors
│           ├── promise errors
│           └── setTimeout errors
│
├── 11. React 18 Features
│     ├── Concurrent rendering
│     ├── Automatic batching
│     ├── startTransition
│     ├── useTransition
│     ├── useDeferredValue
│     └── StrictMode double-render
│
├── 12. API Calls & Side Effects
│     ├── Fetch inside useEffect
│     ├── AbortController
│     ├── Cleanup logic
│     └── Avoid infinite loops
│
├── 13. Lifecycle Mapping
│     ├── Mount → Update → Unmount
│     ├── Hooks equivalent of lifecycle
│     └── Class lifecycle knowledge
│
├── 14. Refs & Imperative Logic
│     ├── useRef vs createRef
│     ├── forwardRef
│     └── useImperativeHandle (custom DOM APIs)
│
├── 15. Custom Hooks
│     ├── Extract logic
│     ├── Return memoized values
│     └── Avoid re-renders
│
├── 16. Data Immutability
│     ├── Why mutation breaks React
│     ├── Immutable update patterns
│     └── Immer.js basics
│
├── 17. Build Tools (Basics)
│     ├── Vite
│     ├── Webpack (entry, output, loaders)
│     └── Babel (transpiling JSX)
│
├── 18. Testing (Optional)
│     ├── Jest
│     ├── React Testing Library
│     ├── Mock API calls
│     └── Snapshot testing
│
├── 19. Deployment
│     ├── Build process
│     ├── Environment variables
│     └── CRA vs Vite vs Next differences
│
└── 20. Next.js (Optional but beneficial)
      ├── File-based routing
      ├── SSR, SSG, CSR differences
      ├── API routes
      └── Data fetching APIs