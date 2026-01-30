React Interview Questions and asnwers topic wise

How react app created?
```
<body>
```
```
    <div id="root"></div>
```
```
    <script type="module" src="/src/main.jsx"></script>
```
```
  </body>
```
```
</html>
```



```
import { StrictMode } from "react";
```
```
import { createRoot } from "react-dom/client";
```
```
import App from "./App.jsx";
```

createRoot(document.getElementById("root")).render(
```
  <StrictMode>
```
```
    <App />
```
```
  </StrictMode>
```
);



Virtual DOM, fiber & reconcilliation
Create Root method creates a DOM like structure, virtual DOM
Problem with virtual DOM,
As I was updating one node, another came then I update them, then in between updation a new update came so I need to update the DOM every time.
Solution: a little pause between the updates so that some concurrent updated can be updated in one go: react fiber
 Don’t update UI immediately, and if you have already done some update calls then drop them and call those update along with new updates.

Virtual DOM: reconciliation: the process in which react compares DOM and VDOM and find the difference using diffing algorithm and update only those differences in the actual DOM
React Fiber: 
React fiber improves react animation, layouts (as continue ui updation by vdom makes page slow)
React fiber does incremental rendering: the ability to split rendering into chunks and spread it out over mulitple frames
Key features or react fiber is: the abilty to pause, abort or reuse work as new update comes in, it assign priority to different types of updates.
React fiber is closely related to hydration: in server side rendering (SSR) as first the html loads (HTML is created at server side) but the whole page is not interactive yet, adding interactivity through JS to plain HTML page web layout is known as hydration

React reconciliation algorithm: 
The algo react uses to diff one tree with another to dermine which part to be changed.
The feature of react that reconsider what to update and what not to update using the diffing algorithm(diffing algo: the process of comparing DOM and VDOM and find out what to update based on changes and update only those parts/node of DOM tree)
Reconciliation is the algorithm behind the VDOM
Note: in react update is done by useState
Different component types are assumed to generate different trees, react will not diff them, it will replace the old tree completely
Diffing of list is done using keys, keys should be stable, unique and predictable

Rules of react fiber
Different element type: replace entire subtree
Same type: update props, using diff
Keyed lists: match old/new items by key
Summary
In UI it is not necessary for every update to be applied immediately. It can cause frame drop and bad UI experience
UI updates can be prioritized
-----------------------------------------------------------------------------------------------
DOM: dom is a tree representation of HTML page 
Why vdom invented: 
when JS changes the DOM, the browser recalculated styles, layout and paints the page again which makes UI slow. 
And react want to avoid direct Dom manipulation
VDOM is a js object copy of real DOM
React updates the vdom first  compares it(diffing)  updates only the  changed nodes in the acutal DOM
Why need react fiber:
React still uses vdom but not the old way
Problem: render  diff  commit is synchronous (one by one), which cause frame drop, slowness in ui
React fiber breaks rendering work into small units(fiber) which can be paused, resumed, aborted or prioritized
Note: 
reconciliation engine = react fiber
rendering system = concurrent rendering



How react render works behind the scene
Render (async)  diff (async)  commit (sync)
Hydration 
Attaching JS to plain HTML (server side ready code) to make it interactive
Browser loads  empty HTML
React creates UI using JS
User finally sees the page
Server side rendering (Next js)
Server sends ready HTML
User sees page immediately
Then JS loads using hydration and makes page interacitve
Better SEO(search engine optimization)
Fast initial load but requires more resources
Perfect for blog, seo heavy websites
Client side rendering(React js)
Blank html page loads to browser
Whole HTML created at the client side
And js is also loaded at the client side only while creating html
SEO is weak bcz html is initially empty
First load is slower but after that navigation is fast
Requires more client resources
Perfect for dashboars, apps, authenticated UI, SPA (single page apps)
Hooks
Why we need Hooks?
When we update some variables inside jsx then its value will get updated (you can check that in console.log) but in UI its value won’t get updated & reflected.
So to reflect the updated value in UI, we need hooks
Here value is getting updated but not reflected in the UI, for that we need hooks
```
import React from "react";
```

```
const LifeCycle = () => {
```
```
  let counter = 0;
```

```
  const add = () => {
```
    counter = counter + 1;
    console.log("counter: ", counter); // value is getting updated

```
  };
```
```
  const subtract = () => {
```
    counter = counter - 1;
    console.log("counter: ", counter); // value is getting updated

```
  };
```
  return (
```
    <div>
```
```
      <h1>Hello there!</h1>
```
```
      <span>{counter}</span>
```
```
      <button onClick={() => add()}>Add</button> {/*same old value */}
```
```
      <button onClick={() => subtract()}>Subtract</button>
```
```
      {/*same old value */}
```
```
    </div>
```
  );
```
};
```

```
export default LifeCycle;
```
Hooks can only be called inside React function components.
Hooks can only be called at the top level of a component.
Hooks cannot be conditional
```
useState hook
```
```
useState hook allows us to track state in a functional component
```
format
```
import {useState} from ‘react’
```
```
const [currState, callback_function_that_updates_state] = useState(initialValue);
```
note: 
```
useState doesn’t updates state immediately.
```
React performs batched, asynchronous updates to imporve perfromance
Render phase: react calls components  returns UI  compares with prev UI
Commit phase: react updates DOM  runs effect
Re-render: triggered when state changes
In short: react useState hook stores reactive values that trigger re-render and live inside React fiber, not inside component


The counter problem
```
import React from "react";
```
```
import { useState } from "react";
```

```
const LifeCycle = () => {
```
```
  const [counter, setCounter] = useState(0);
```

```
  const add = () => {
```
    setCounter(counter + 1);
    setCounter(counter + 1);
    setCounter(counter + 1);
    console.log("counter: ", counter);
    // even after we have 3 counter , value will be updated by +1 only
```
  };
```

```
  const subtract = () => {
```
    setCounter(counter - 1);
```
  };
```
  return (
```
    <div>
```
```
      <h1>Hello there!</h1>
```
```
      <span>{counter}</span>
```
```
      <button onClick={() => add()}>Add</button>
```
```
      <button onClick={() => subtract()}>Subtract</button>
```
```
    </div>
```
  );
```
};
```

```
export default LifeCycle;
```


Reason:
Value is getting updated by +1 only because react updates value in batches, here all 3 counters are taking the old same value and hence all are updating it to +1 only not +3

Solution
Use prevCounter. setCounter is provided by a callback function which takes the latest state value, and since we are getting latest values, we are updating the right value
```
const add = () => {
```
    setCounter((prevCounter) => prevCounter + 1);
    setCounter((prevCounter) => prevCounter + 1);
    setCounter((prevCounter) => prevCounter + 1);

    console.log("counter: ", counter);
    // now counter will update by +3
```
  };
```
One Note – we don’t need hooks always
This is working fine but we don’t need 2 useState
```
import React, { useState } from "react";
```
```
const LifeCycle = () => {
```
```
  const [val, setVal] = useState(1);
```
  //const [addVal, setAddVal] = useState(0);
```
  let addVal = val + 1;
```
```
  const update = () => {
```
    setVal((prev) => prev * 5);
```
  };
```
  return (
```
    <div>
```
```
      <h1>Hello</h1> <button onClick={update}>Update</button>{" "}
```
```
      <p>value: {val}</p> <p>Add value: {addVal}</p>{" "}
```
```
    </div>
```
  );
```
};
```
```
export default LifeCycle;
```


Sometimes we don’t need useState for every variable.
Here val is getting updated two times using two useState updating functions. 
But we can do it by using only one useState.
See as the value of val is getting updated using useState, the react component re-renders and prints the latest value of val, and since we are already getting the latest value of val we have the updated addVal also.
```
useEffect
```
```
useEffect hook allows to perform side effects in componnets like fetching data, api calls, timers, updating DOM, etc after react render
```
note: side-effect – anything that react cannot handle itself in the pure render phase.
Side effects run inside useEffect because React renders components as pure functions.
If side effects ran during rendering, React would break its ability to re-render safely, batch updates, skip renders, memoize components, and run strict mode.
```

useEffect schedules side effects to run after the DOM is updated, ensuring predictable, stable, and non-blocking behavior.
```

🧩 Let’s visualize the timeline
🚫 Render Phase (Must be PURE)
Calculate JSX
Compare virtual DOM
Prepare updates
Do not run effects
✅ Commit Phase (DOM updated)
Now React runs:
✔ useLayoutEffect
✔ useEffect
✔ DOM operations
✔ Mutations outside render
This ensures:
UI is ready
DOM exists
Effects do not block rendering

Note: useEffect only runs AFTER the render, after commiting the DOM so that we can safely access DOM
Syntax:
```
useEffect(function, dependency)
```
Dependency
No dependency: runs on every render
[]: runs on the first render
[props, state]: runs when props or state changes
```
useEffect returns cleanup function. We can use them to clean the times, etc
```
note: why useEffect runs every time with empty dependency?
Because – empty array means : no values  nothing to compare  runs on every mount
Execution of useEffect
Render phase: pure calculation, no side effects allowed
Commit phase: DOM update happens
Effect phase: useEffect callback runs
Render  reconcilliation  DOM commit  useEffect callbaks  Event loop
Cleanup function: useEffect returns a cleanup function.
Cleanup function runs two times
Before component unmounts
Before re-running effect due to dependency change
“Why does useEffect cause an infinite loop?”
Example:
```
useEffect(() => {
```
  setCount(count + 1);
```
}, [count]);
```
Reason:
count changes → effect runs
effect changes count again
new render → count changed again
infinite loop 🔁
Fix: functional update
setCount(prev => prev + 1);
OR
Move logic outside effect.
Why do we need cleanup?
Prevent memory leaks
Remove old subscriptions
Remove old timers
Avoid duplicate event listeners
Make effect idempotent
React strict mode runs effects twice in dev mode
Summary: useEffect runs AFTER render, re-runs only when dependencies change, and cleans up before re-running or unmounting.
useContext
useContext hook allows us to share global or shared values between mulitple levels of components without passing props through each level
it saves us from prop drilling.
Syntax:
Create Context 
```
const UserContext = createContext();
```
Provide the Value 
```
<UserContext.Provider value={someValue}>
```
```
  <App/>
```
```
</UserContext.Provider>
```
Consume
```
const user = useContext(UserContext);
```

whenever the provider value changes,react automatically re-renders only the components that use the context (consumers)
It is not suitable for large complex apps, the problem is, useContext will re-render every component inside the UserContext.Provider when the value changes, even if another components are not using that value
Note: when to use what

//creating context
```
import React from "react";
```
```
import { useState, createContext } from "react";
```
```
import One from "./One";
```
```
export const UserContext = createContext();
```
```
const lifeCycle = () => {
```
```
  const [user, setUser] = useState("Guest");
```
```
  const [val, setVal] = useState(0);
```
  return (
```
    <div>
```
```
      <h1>Hello {user}</h1>
```
```
      {/* <UserContext.Provider value={user}> */}{" "}
```
```
      {/*passing only single value */}
```
```
      <UserContext.Provider value={{ user, setUser, val, setVal }}>
```
```
        {/* passing multiple values by wrapping them into {}, notice here we have {{}}*/}
```
```
        <One></One>
```
```
      </UserContext.Provider>
```
```
    </div>
```
  );
```
};
```

```
export default lifeCycle;
```


//utilizing context
One.jsx
```
import React from "react";
```
```
import Two from "./Two";
```

```
const One = () => {
```
  return (
```
    <div>
```
```
      <p>I am one</p>
```
```
      <Two />
```
```
    </div>
```
  );
```
};
```

```
export default One;
```

Two.jsx
```
import React, { useContext } from "react";
```
```
import { UserContext } from "./LifeCycle";
```
```
const Two = () => {
```
  // destructuring
```
  const { user, setUser, val, setVal } = useContext(UserContext);
```
  return <div>Hello {user}</div>;
```
};
```

```
export default Two;
```


It is not suitable for large complex apps, the problem is, useContext will re-render every component inside the UserContext.Provider when the value changes, even if another components are not using that value
Here One.jsx will also re-render if user changes even though user is used inside Two.jsx
useReducer
it is similar to useState hook. But useReducer is used to keep track of complex logic. It is similar to redux but build in direct react.
useReducer centralizes state logic in one pure reducer function
use useState when working with simple state/app
use useReducer when working with complex state/app
Syntax:
Const [state, dispatch] = useReducer(reducer, intialState, init)
reducer – it is a pure function that contains custom state logic
initialState – contains object containing initial values
initi – its otional, used to compute initial state lazily, when computation is heavy
useReducer hooks returns the current state and a dispatch method
“When do you choose useReducer over useState?”
when:
multiple state values depend on each other
state updates are complex
logic contains branching
I want predictable state transitions
I want central control (like Redux)
I want to avoid prop drilling by combining with useContext

```
import React, { useReducer } from "react";
```
```
const lifeCycle = () => {
```
  // initial state
```
  const initialState = { count: 0 };
```

  // pure reducer function
  // state: the current state
  // action: what to do on that state, passed as a argument by calling function
```
  function reducer(state, action) {
```
    switch (action.type) {
      case "add":
        return { count: state.count + 1 };
      case "subtract":
        return { count: state.count - 1 };
      default:
        return state;
```
    }
```
```
  }
```
  // const [state, setState] = useState(0);
  // reducer is the function that will do the updation like setState
  // dispatch is the function that will tell reducer what to update
```
  const [state, dispatch] = useReducer(reducer, initialState);
```

  return (
```
    <div>
```
```
      <p>{state.count}</p>
```
```
      <button onClick={() => dispatch({ type: "add" })}>+</button>
```
```
      <button onClick={() => dispatch({ type: "subtract" })}>-</button>
```
```
    </div>
```
  );
```
};
```

```
export default lifeCycle;
```

some explanation: 
initialState
This is the starting value for the reducer.
In your case: { count: 0 }
reducer function
It receives state (current state)
It receives action (an object you send using dispatch)
It returns the new state
It must be pure (no side effects, no async, no mutation)
Example:
When action.type is "add" → return { count: state.count + 1 }
dispatch
It is similar to calling setState, but more flexible
Instead of giving a value, you send an action (object)
React calls the reducer with this action
useReducer(reducer, initialState)
Connects React with your reducer
Returns:
state → the current state
dispatch → function to trigger updates
Why useReducer is used instead of useState?
When state logic becomes complex
When multiple updates depend on previous state
When you want predictable updates
When your state is an object or has multiple fields
dispatch is the function that SENDS an action to the reducer to decide how to update the state.

useCallback
useCallback is a react hook that lets  you cache a function definition between re-renders.
Syntax:
useCallback(function, dependecies_array);
here dependency array is for caching, whenever something will change from the dependecy array then re create the function
useMemo
useMemo hook returns a memoized value
useMemo caches the value so that it does not need to recalculated
useMemo only runs when one of its dependencies changes
useMemo & useCallback are similar. useMemo returns a memoized value and useCallback returns a memoized function
Syntax:
Const memoVal = useMemo(callback_function, dependency_array)
Note: don’t overuse useMemo as it is costly in terms of memory. use only when computation is expensive
Note: useMemo is not for avoiding re-reners of the component itself
useMemo is only for: 
to optimize value computation
avoid expensive re-calculation
note: React.Memo related to higher order component. It will re-render the component only when its props has changed.
Example: export default React.memo(MyComponent);

```
import React, { useMemo, useState } from "react";
```

```
const lifeCycle = () => {
```
```
  const [count, setCount] = useState(0);
```
```
  const [val, setVal] = useState(0);
```
```
  const slowDouble = (num) => {
```
    console.log("running slowly...");
    for (let i = 0; i < 300000000; i++) {
      // some code
```
    }
```
    return num * 2;
```
  };
```
  //const doubleVal = slowDouble(count);//wihtout use memo, it will run every time even when count value is not getting updates
```
  const doubleVal = useMemo(() => slowDouble(count), [count]); //now this function will run only when the value of count variable changes
```
  return (
```
    <div>
```
```
      <h1>Hello there</h1>
```
```
      <h1>{count}</h1>
```
```
      <button onClick={() => setCount((prev) => prev + 1)}>+</button>
```
```
      <h1>{val}</h1>
```
```
      <button onClick={() => setVal((prev) => prev + 1)}>+</button>
```
```
      <p>Double val: {doubleVal}</p>
```
```
    </div>
```
  );
```
};
```

```
export default lifeCycle;
```


some cases
  useEffect(fn, []) → runs once AFTER render
  useMemo(fn, []) → runs once DURING render
  useCallback(fn, []) → creates function once DURING render


useCallback
useCallback is used to memoized a callback function
memoizing means caching the reference of that function so that it does not need to be re-calculated. Function will be created anyways in every render, but it will be discarded  - preventing its effect to the component. As function creation is cheap in react but handling its effect is expensive like re-rendering bcz of that function

syntax:
```
const memoFun = useCallback (callback, dependencies)
```
when to use:
when passing a function as a prop to a memoized child
when dependency array rarely changes
expensive callback

don’t use:
when you aren’t passing function to child
for tiny function
note:
useMemo([]):
compute once
return same value forever
useCallback([]):
create function once
return same function forever

```
import React, { useCallback, useState } from "react";
```

```
const Child = React.memo(({ onClick }) => {
```
  console.log("child rendered");
  return <button onClick={onClick}>Child Button</button>;
```
});
```

```
const lifeCycle = () => {
```
```
  const [count, setCount] = useState(0);
```
```
  const [val, setVal] = useState(0);
```
  // here child will be rendered every time even if child button is not clicked
  // const handleClick = () => {
  //   console.log("child button clicked");
  //   setCount((prev) => prev + 1);
  // };

  //now child button will not get rendered every time
```
  const handleClick = useCallback(() => {
```
    console.log("child button clicked");
    setCount((prev) => prev + 1);
```
  }, []);
```

  return (
```
    <div>
```
```
      <h1>Parent: count {count}</h1>
```
```
      <h1>Parent: val: {val}</h1>
```
```
      <button onClick={() => setVal((prevVal) => prevVal + 1)}>+ val</button>
```

```
      <Child onClick={handleClick} />
```
```
    </div>
```
  );
```
};
```

```
export default lifeCycle;
```


useRef
useRef hook allows to persist values between renders
it can be used to store a mutable value that done not cause re-render when update
it can be used to update DOM elements direclty
inside ref, there is only one property: ref.current
```
const myRef = useRef(intialVal);
```
access the value: myRef.current;
use case: 
 it is perfect for timer
  previous state values
  storing previous props
  caching things
  storing state without re-rendering

```
import React, { useEffect, useState, useRef } from "react";
```

```
const lifeCycle = () => {
```
```
  const [count, setCount] = useState(0);
```
```
  const renderCount = useRef(1); // will not cause re-render
```
```
  const inputRef = useRef(null); //will be used to access DOM elements
```

```
  const focusInput = () => {
```
    inputRef.current.focus(); //direct DOM access
```
  };
```

```
  useEffect(() => {
```
    renderCount.current += 1;
```
  });
```
  return (
```
    <div>
```
```
      <h1>Count: {count}</h1>
```
```
      <h2>Component rendered: {renderCount.current}</h2>
```
```
      <button onClick={() => setCount((prev) => prev + 1)}>Click me</button>
```

```
      <br />
```

```
      <input 
```
      ref={inputRef} //tells the react to attach the actual DOM element into useRef variable
      type="text" 
      placeholder="Name" />
```
      <button onClick={focusInput}>Focus input</button>
```
```
    </div>
```
  );
```
};
```

```
export default lifeCycle;
```



passowrd generator
example ussing useCallback,useRef, useEffect

```
import React, { useState, useCallback, useEffect, useRef } from "react";
```
```
const LifeCycle = () => {
```
```
  const [length, setLength] = useState(8);
```
```
  const [numberAllowed, setNumberAllowed] = useState(false);
```
```
  const [charAllowed, setCharAllowed] = useState(false);
```
```
  const [password, setPassword] = useState("");
```
```
  const passwordRef = useRef(null);
```

```
  const fun = () => {
```
```
    let pass = "";
```
```
    let str = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
```
    if (numberAllowed) str += "0123456789";
    if (charAllowed) str += "!@#$%^&*~`";
    for (let i = 1; i <= length; i++) {
```
      let charInd = Math.floor(Math.random() * str.length + 1);
```
      pass = pass + str.charAt(charInd);
```
    }
```
    setPassword(pass);
```
  };
```

```
  const passwordGenerator = useCallback(fun, [
```
    length,
    numberAllowed,
    charAllowed,
    setPassword,
  ]);

```
  const copyToclipboard = useCallback(() => {
```
    passwordRef.current?.select();
    window.navigator.clipboard.writeText(password);
```
  }, [password]);
```

```
  useEffect(() => {
```
    passwordGenerator();
```
  }, [length, numberAllowed, charAllowed, passwordGenerator]);
```

  // note: passwordGenerator will not cause infinite re-render as it is optimized by useCallback function. if useCallback was not there then it can cause infinite rendering
  
  return (
```
    <div>
```
```
      <h1>working</h1>
```
```
      <input
```
        type="text"
        value={password}
        placeholder="Password"
        readOnly
        ref={passwordRef}
      />
```
      <button onClick={copyToclipboard}>Copy</button>
```
```
      <br />
```
```
      <input
```
        type="range"
        min={6}
        max={100}
        value={length}
        onChange={(e) => setLength(e.target.value)}
      />
```
      <label htmlFor="length">Length: {length}</label>
```
```
      <br />
```

```
      <input
```
        type="checkbox"
        defaultChecked={numberAllowed}
        id="numberInput"
        onChange={() => {
          setNumberAllowed((prev) => !prev);
```
        }}
```
      />
```
      <label htmlFor="numberInput">Number</label>
```

```
      <input
```
        type="checkbox"
        defaultChecked={charAllowed}
        id="charAllowed"
        onChange={() => {
          setCharAllowed((prev) => !prev);
```
        }}
```
      />
```
      <label htmlFor="charAllowed">Characters</label>
```
```
    </div>
```
  );
```
};
```
```
export default LifeCycle;
```

pure components: same input will give same output
React router
Used for client-side rendering. It does not refrest the page.
Note: everything is imported  from "react-router-dom"
Basic structure
Everything related to react router should be inside <BrowserRouter> </BrowserRouter>
Every route will be defined inside the <Routes>  </Routes>
Path inside the <Route/> is the url link that we sees 
Link will make a clickable text that will be redirected/re-routed to path that is mentioned in to. Link does SPA(single page application) navigation
NavLink is similary to Link but with extra fetures like isActive, pending and all, used for UI styling
Link and NavLink are click based navigation

```
import React from "react";
```
```
import { BrowserRouter, Routes, Route, Link, NavLink } from "react-router-dom";
```

```
import NotFound from "./NotFound";
```
```
import Home from "./Home";
```
```
import About from "./About";
```
```
import Contact from "./Contact";
```
```
import "./index.css";
```
```
const ReactRouter = () => {
```
  return (
```
    <>
```
```
      <BrowserRouter>
```
```
        <Routes>
```
```
          {/* path is the url link */}
```
```
          <Route path="/" element={<Home />} />
```
```
          <Route path="/about" element={<About />} />
```
```
          <Route path="/contact" element={<Contact />} />
```
```
        </Routes>
```

```
        {/* use link when using normal links */}
```
```
        <Link to="/">Home </Link>
```
```
        {/* use navLink when we want to find active and other states of link */}
```
```
        <NavLink
```
          to="/about"
          //   React router calls function(callback function ()=>{})
          // react router gives {isActive} value
          // className = {jsx}
          // className = {()=>{}} //callback function
          // className = {({object destructuring}) => setting className}
          className={({ isActive }) => (isActive ? "activeClass" : "")}
        >
          About
```
        </NavLink>
```
```
      </BrowserRouter>
```
```
    </>
```
  );
```
};
```

```
export default ReactRouter;
```


useNavigate
Link and NavLink are click based navigation. 
But when we need to navigate using JS or using some logic then use useNavigate hook
Like navigate when: user logs in, when logs out, when form is submitted, after API response and so on
Note: you cannot even declare useNavigate without <BrowserRouter> so wrap the whole component inside the BrowserRouter when calling it
```
import ReactRouter from "./interview/ReactRouter";
```
```
import { BrowserRouter } from "react-router-dom";
```
```
function App() {
```
  return (
```
    <>
```
```
      <BrowserRouter>
```
```
        <ReactRouter />
```
```
      </BrowserRouter>
```
```
    </>
```
  );
```
}
```

```
export default App;
```


else doing this will cause error
```
import ReactRouter from "./interview/ReactRouter";
```
```
function App() {
```
  return (
```
    <>
```
```
        <ReactRouter />
```
```
    </>
```
  );
```
}
```

```
export default App;
```

some imports … 
```
const ReactRouter = () => {
```
```
  const navigate = useNavigate(); //this is a function that can change route programitacally   you cannot do this, as even declaring useNavigate without BrowserRouter will give error
```

```
  const goToAbout = () => {
```
    navigate("/about");
```
  };
```
  return (
```
    <>
```
```
      <BrowserRouter>
```
```
        <Routes> some routes… </Routes>
```
		Some NavLinks
```
        <button onClick={goToAbout}>Go to about page</button> //error
```
```
      </BrowserRouter>
```
```
    </>
```
  );
```
};
```


Super important: go back and forth in history using navigate (-1), (1) or (-2)

```
import React from "react";
```
```
import {
```
  BrowserRouter,
  Routes,
  Route,
  NavLink,
  useNavigate,
```
} from "react-router-dom";
```

```
import NotFound from "./NotFound";
```
```
import Home from "./Home";
```
```
import About from "./About";
```
```
import Contact from "./Contact";
```
```
import "./index.css";
```
```
const ReactRouter = () => {
```
```
  const navigate = useNavigate(); //this is a function that can change route programitacally
```

```
  const goToAbout = () => {
```
    navigate("/about");
```
  };
```
  return (
```
    <>
```
```
      <Routes>
```
```
        {/* path is the url link */}
```
```
        <Route path="/" element={<Home />} />
```
```
        <Route path="/about" element={<About />} />
```
```
        <Route path="/contact" element={<Contact />} />
```
```
      </Routes>
```
```
      {/* use link when using normal links */}
```
```
      <NavLink
```
        to="/"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Home
```
      </NavLink>
```
```
      <NavLink
```
        to="/about"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        About
```
      </NavLink>
```
```
      <NavLink
```
        to="/contact"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Contact
```
      </NavLink>
```
```
      <button onClick={goToAbout}>Go to about page</button>
```
```
      <button onClick={() => navigate(-1)}>Go back</button>{" "}
```
```
      {/* go back in history*/}
```
```
      <button onClick={() => navigate(-2)}>Go back</button>{" "}
```
```
      {/* go back two steps in history*/}
```
```
      <button onClick={() => navigate(1)}>Go forward</button>
```
```
      {/* go forward in history*/}
```
```
    </>
```
  );
```
};
```

```
export default ReactRouter;
```





URL parameters & dynamic routes
Why params are always strings:  Because URL segments are text by nature.
```
import React from "react";
```
```
import {
```
  BrowserRouter,
  Routes,
  Route,
  NavLink,
```
} from "react-router-dom";
```

```
import NotFound from "./NotFound";
```
```
import Home from "./Home";
```
```
import About from "./About";
```
```
import Contact from "./Contact";
```
```
import "./index.css";
```
```
const ReactRouter = () => {
```
  return (
```
    <>
```
```
      <Routes>
```
```
        <Route path="/" element={<Home />} />
```
```
        <Route path="/about" element={<About />} />
```
```
        <Route path="/contact/:id" element={<Contact />} /> {/*using dynamic routing */}
```
```
      </Routes>      
```
```
      <NavLink
```
        to="/contact"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Contact
```
      </NavLink>
```
```
    </>
```
  );
```
};
```

```
export default ReactRouter;
```


Contact.jsx
```
import React from "react";
```
```
import { useParams } from "react-router-dom";
```
```
const Contact = () => {
```
```
  const { id } = useParams();//using dynamic routing, object destructuring
```
  return (
```
    <div>
```
```
      <p>Contact us at: {id}</p> {/*using dynamic routing */}
```
```
    </div>
```
  );
```
};
```

```
export default Contact;
```


note: /contact and /contact/123 are different. Now visiting /contact will not show anything as its route is nto there
also the useParams are always of string data type, if you want number then use this: Number(id)
how route ranking/scoring wroks
here we have 3 paths for contact, which one to render, so for that we have priority
```
 <Routes>
```
```
        {/* path is the url link */}
```
```
        <Route path="/contact" element={<OnlyContact />} />
```
```
        <Route path="/contact/:id" element={<IdContact />} />
```
```
        <Route path="/contact/new" element={<NewContact />} />
```
```
        <Route path="/contact/*" element={<NotFound />} />{" "}
```
```
        {/*using dynamic routing */}
```
```
      </Routes>
```






So for url:
/contact : OnlyContact component get renders as the exact match of url “contact” path is there and its has top priority
/contact/123: IdContact coponent gets redered as its exact match is not there by params is there
/contact/123/something: NotFound coponent gets redered as it was handled in * case
useLocation
using useLocation we can read – 
pathname: current route path
search: query string (?q=hello&page=2)
hash:  anchor (#section2)
state: data passed from navigate() or link()
key: unique key every time location changes
uses of useLocation
 A header that wants to highlight the current tab
 A popup that opens only on a certain route
 Reading query params like /products?search=Books
 Reading navigation sate sent from navigate() 
it is same as windows.location, but in a better way
```
import { Routes, Route } from "react-router-dom";
```
```
import Contact from "./Contact";
```
```
import "./index.css";
```
```
const ReactRouter = () => {
```
  return (
```
    <>
```
```
      <Routes>
```
```
        <Route path="/contact/:id" element={<Contact />} />
```
```
      </Routes>
```
```
    </>
```
  );
```
};
```

```
export default ReactRouter;
```

Contact.jsx
```
import React from "react";
```
```
import { useParams, useLocation } from "react-router-dom";
```
```
const Contact = () => {
```
```
  const location = useLocation();
```

  console.log("location: ", location);
```
  const { id } = useParams();
```
  return (
```
    <div>
```
```
      <p>Contact us at: {id}</p>
```
```
      <p>location:{location.pathname}</p>
```
```
    </div>
```
  );
```
};
```

```
export default Contact;
```



1️⃣2️⃣ useMatch
1️⃣3️⃣ Search Params (?q=hello)
1️⃣4️⃣ Redirects using <Navigate />
1️⃣5️⃣ 404 NotFound route

🟧 ADVANCED LEVEL
1️⃣6️⃣ Nested routes (most important)
1️⃣7️⃣ Layout routes (<Outlet />)
1️⃣8️⃣ Protected Routes (authentication guard)
1️⃣9️⃣ Relative Routing
2️⃣0️⃣ Index Routes
2️⃣1️⃣ Catch-All routes
2️⃣2️⃣ Active link advanced styling
2️⃣3️⃣ Loader & Action (React Router Data API)
2️⃣4️⃣ Error boundaries (v6.4+)
2️⃣5️⃣ Code Splitting with lazy + Suspense










Javascript
Event loop

After the global execution context (GEC) finished executing, call stack is empty and then if the timer of a callback funciton is finised then that callback will be inside the callback queue.
Event loop will keep checking for the function inside callback queue, if it is there then that callback function will be pushed to call stack by event loop for execution
Event loop job: to continuously monitor callback queue for function to execute
We need callback queue to better manage the callback functions, as there are lots of event listeners in our program


Microtask queue
Microtask queue has higher priority than callback queue.
Functions inside the microtask queue will be executed before function in callback queue.

In microtask queue: all the functions which comes from promises & mutation observer will goes inside microtask queue.
Mutaion observer keeps checking if there is some mutaion in the DOM tree, if yes then mutation observer will execute the callback function
Note: callback queue is also known as task queue.
Note: once all the task from microtask queue are finished executing then only callback queue will be given a chance for execution by event loop
Starvation of the task iunside the callback queue: when task inside the microtask queue generates more task then callback queue will never get change for execution
JS concurrency model: JavaScript is single-threaded (only one line of code runs at a time),
but
it achieves concurrency through the Event Loop, Task Queues, and Web APIs.

Important example:
async function test() {
  console.log("X");
  await null;
  console.log("Y");
```
}
```

console.log("Z");
test();
console.log("W");

// output
/*
prints Z: GEC code
test() : sees x, prints X
then await null is promise resolved, goes to microtask queue --> y is in microtask queue
prints W
prints Y as GEC finished
*/

1. Run entire synchronous GEC (global code)
Everything not async executes here.

2. Run all microtasks (Promise callbacks, async/await continuations, queueMicrotask, MutationObserver)
✔ Microtasks run before ANY macrotask
✔ Microtasks run until queue becomes empty

3. Run next macrotask (setTimeout, setInterval, DOM events, I/O)
✔ Only ONE macrotask runs per cycle
✔ After it finishes → go back to microtasks again

Render/update UI (optional step depending on browser)
Antoher important example
console.log("A");

setTimeout(() => console.log("B"), 0);

fetch("https://jsonplaceholder.typicode.com/todos/1").then(() =>
  console.log("C")
);

Promise.resolve().then(() => console.log("D")); //promise.resolve runs before fetch.then

console.log("E");

/*
A E D C B
*/



JS engine
Inside the JS runtime we have JS engine. Inside js runtime environment we have eveythiung that is required to run JS code
Chrome has V8, firefox has spidermonkey (first js engine, was evolved to this)
V8 was written in c++
Js engine takes high level code and convert it into machine level code for execution
Code  parsing   compilation  execution
Parsing : code is broken down into tokens. Syntax parser takes code and convert it into AST(abstract Syntax Tree) 
Compilation: JIT compilation (Just in time comiler). 
Interpreter: code is executed line by line. Code is fast executed.
Compiler: code is compiled first and then the optimized/compiled code runs. We have more efficiency
JS can behave both interpreted and compiled using JIT
AST goes to interpreter and the code is executed. While interpreter going to compiler for optimization. Here compiler will compiler the hot code (frequently used code) and store its compiler version byte code for faster execution next time
AOT: ahead of time compilation. It takes code that is going to be executed next time and optimize it as creates byte code
Execution: compilation and execution happens parallel. Memory heap and call stack plays an important role in execution.
We have automatic garbage collector inside the js which uses mark and sweep algorithm.


V8 has : ignition interpreter and turbo fan optimizing compiler





Set time out
Set time out callback will be executed only after the GEC has finished executing the entire code even though we have 0 sec timer. 


Higher Order Function (HOC)
A function that takes a function and returns another function is known as higher order function

```
const radius = [1, 2, 3, 4];
```

```
const caculateArea = function (radius) {
```
```
  let area = [];
```
  for (let i = 0; i < radius.length; i++) {
    area.push(Math.PI * radius[i] * radius[i]);
```
  }
```
  return area;
```
};
```
console.log(caculateArea(radius));

/*
show answer upto 2 decimals only
toFixed returns string, to convert it into number we used Number

area.push(Number(( Math.PI*radius[i]*radius[i]).toFixed(2)));
*/

// concept: extract the repeated logic and put it inside the single code like calculate

```
const area = function (radii) {
```
  return Math.PI * radii * radii;
```
};
```

```
const circumference = function (radii) {
```
  return 2 * Math.PI * radii;
```
};
```

```
const calculate = function (radius, logic) {
```
```
  const result = [];
```
  for (let i = 0; i < radius.length; i++) {
    result.push(logic(radius[i]));
```
  }
```
  return result;
```
};
```

console.log(calculate(radius, circumference));


this is kind of implementation of own map
console.log(radius.map((area)));

map filter reduce
These are HOF
Map
Map takes a callback function and calls that function for every item in array (in which map called on) and returns a new array
Note: don’t forget to return value from map else it will be undefined. No need to return if you are using arrow function and having only one line, just don’t use curly braces there

```
const arr = [1, 2, 3, 4, 5];
```

```
const double = function (item) {
```
  return item * 2;
```
};
```

// takes callback function and runs that callback function for every element in arr array
```
const output = arr.map(double);
```
console.log(output);

```
const tripple = arr.map((item)=> item*item*item);
```
console.log(tripple)

```
const twice = arr.map((item)=>{
```
    return 2*item;
```
})
```
console.log(twice)

Filter
Used to filter values in an array based on callback function /logic
```
function isOdd (item){
```
    return item%2!==0;
```
}
```

```
const odd = arr.filter(isOdd);
```
console.log(odd)

Reduce
Used to accumulate array values. 
Reduce takes all the array values and return single value based on some logic/callback function
The callback function in reduce takes two parameters – one is accumulator, and current array value
The reduce takes two parameters – callback function/logic and initail value of accumulator, defualt is 0


// without reduce
```
const sum = function (item) {
```
```
  let total = 0;
```
  for (let i = 0; i < item.length; i++) {
    total += item[i];
```
  }
```
  return total;
```
};
```
console.log(sum(arr));

// with reduce - reduce takes a callback function(which takes two parameters - accumulator(which holds total) and currect value of array ) and a intial value

```
let findSum = arr.reduce((accumulator, curr) => {
```
  return accumulator + curr;
```
}, 10);
```

// note: here 10 is the initial value of accumulator, default is 0. so now sum will start from 10
console.log(findSum);

 



Important example for revision: imp
```
const arr = [1, 2, 3, 4, 5];
```

// map
/*

```
const double = function (item) {
```
  return item * 2;
```
};
```

// takes callback function and runs that callback function for every element in arr array
```
const output = arr.map(double);
```
console.log(output);

```
const tripple = arr.map((item)=> item*item*item);
```
console.log(tripple)

```
const twice = arr.map((item)=>{
```
    return 2*item;
```
})
```
console.log(twice)

*/

// filter

/*
```
function isOdd (item){
```
    return item%2!==0;
```
}
```

```
const odd = arr.filter(isOdd);
```
console.log(odd)
*/

// reduce

/*
// without reduce
```
const sum = function (item) {
```
```
  let total = 0;
```
  for (let i = 0; i < item.length; i++) {
    total += item[i];
```
  }
```
  return total;
```
};
```
console.log(sum(arr));

// with reduce - reduce takes a callback function(which takes two parameters - accumulator(which holds total) and currect value of array ) and a intial value

```
let findSum = arr.reduce((accumulator, curr) => {
```
  return accumulator + curr;
```
}, 10);
```

// note: here 10 is the initial value of accumulator, default is 0. so now sum will start from 10
console.log(findSum);

// finding max using reduce
```
let max = arr.reduce((acc, curr) => {
```
  if (curr > acc) {
    acc = curr;
```
  }
```
  return acc;
```
}, 0);
```

console.log(max);
*/

// find list of full names
```
const users = [
```
```
  { firstName: "abc", lastName: "xyz", age: 26 },
```
```
  { firstName: "donald", lastName: "trump", age: 75 },
```
```
  { firstName: "elon", lastName: "musk", age: 50 },
```
```
  { firstName: "deepika", lastName: "padukone", age: 26 },
```
];

// list of full names
```
const full_name = users.map((item) => {
```
  return item.firstName + " " + item.lastName;
```
});
```

console.log(full_name);

// list of ages by group
```
const ages = users.reduce((acc, curr) => {
```

    // age already present
    if (acc[curr.age]) {
      acc[curr.age] = acc[curr.age] + 1;
```
    } else {
```
      // insert the age with count 1
      acc[curr.age] = 1;
```
    }
```

  return acc;
```
}, {});
```

console.log(ages);

// list of first names whose age is <30

```
const minor = users
```
  .filter((item) => item.age < 30)
  .map((item) => item.firstName);

console.log(minor);

// or use using reduce

// polyfills of map/filter/reduce - what is this, how this works



Lexical environmnet
It is the local memory along with refernce to its parents memory/variables & functions
Lexical means hierarcy/in a sequence
Scope chain: the way of finding variables in local environment and then in parent’s lexical environment.

Hoisting
Hoisting means JavaScript moves variable and function declarations to memory before the code starts executing.
JavaScript scans your code
Creates memory space for variables & functions
BEFORE the execution line starts running



Let/Const/Var
var
Hoisted
Memory allocated
Initialized with undefined
Accessible before declaration (but value is undefined)
Has global/functional scope
```
let & const
```
Hoisted
Memory allocated
NOT initialized
Stay in TDZ until the line where they are declared
Accessing before declaration → ReferenceError
Has block scope
TDZ: temporal dead zone: let & const stays in TDZ until they are initialized with some value.
The time between hoisting and actual initialization of let/const variables.
Note: in case of syntax error code will not get executed at all.
In case of let and const, you cannot reclare. It will give syntax error
Also for const you cannot reinitialze the variable
But for var you can redeclare and reinitialize both

Errors
Syntax error: Syntax Error occurs when the code breaks JavaScript grammar rules, so the JS engine cannot parse it.
It happens before execution starts.
Reference error: ReferenceError occurs when you try to access a variable that exists in the scope chain but is not accessible yet (e.g., TDZ), or it does not exist at all.
Case 1: Variable not declared
console.log(x); // ❌ ReferenceError: x is not defined
Case 2: let / const in TDZ
console.log(a); // ❌ ReferenceError (TDZ)
```
let a = 10;
```

Type error: TypeError occurs when the value is valid, but the operation you are performing on that value is not allowed
❌ Reassigning const
```
const a = 10;
```
a = 20; // ❌ TypeError: Assignment to constant variable
❌ Calling a non-function
```
let x = 5;
```
x(); // ❌ TypeError: x is not a function
❌ Accessing property of null/undefined
```
let z = null;
```
z.toString(); // ❌ TypeError: Cannot read property...

Block in js
```
{} this is known as block 
```
Blocks allow us to group multiple statements where only one statement is expected.
Example: if, for, while, function, etc. 
Just like in if(){} 
Block scope follows lexical scope.
Let & const have block scope.
Var – not block scoped, only function scoped
Shadowing
Shadowing occurs when an inner variable has the same name as an outer variable.
Inside the block:
the inner variable hides (shadows) the outer variable
outer variable is still present but not accessible inside the block


✅ Shadowing Behavior
✔ 1. var shadowing
```
var inside a block is NOT block-scoped →
It reassigns the outer variable (because both refer to the same memory).
```
```
var a = 10;
```

```
{
```
```
  var a = 20;  // same memory → overwrites outer
```
```
}
```

console.log(a); // 20
✔ This is allowed
✔ This is normal behavior
⚠ Not recommended in real code

✔ 2. let/const shadowing
Inner let or const creates a new block-scoped variable,
so they do NOT overwrite the outer variable.
```
let a = 10;
```

```
{
```
```
  let a = 20;  // new variable → block scope only
```
  console.log(a); // 20
```
}
```

console.log(a); // 10

❌ Illegal Shadowing
This happens when the inner variable breaks scoping rules.
Your example:
```
let a = 10;
```
```
{
```
```
  var a = 20; // ❌ illegal shadowing
```
```
}
```
❗ Why illegal?
```
let is block scoped (inner scope)
```
```
var is function/global scoped
```
So var a tries to redeclare the same name in the outer/global scope
→ JS throws SyntaxError

✅ Legal Shadowing
```
var a = 10;  
```
```
{
```
```
  let a = 20;  // ✔ legal shadowing
```
```
}
```
✔ Why legal?
```
var a exists in outer scope
```
```
let a exists in block scope
```
No overlap → so legal
Shadowing behaves same in function and block scope
There are some illegal shadowing also
You cant do 
Let a = 10;
```
{
```
Var  a =20 //illegal – bcz var has global scope and it is crossing its boundary, so it will interfere with it
```
}
```
But you can do this
```
var a = 10;
```
```
{
```
```
let  a =20 //illegal – bcz let is in its boundary, block scope so it wont interfere with var
```
```
}
```

Confustion between [], dot and “” – in object variable accessing
```
const arr = [1, 2, 3, 4, 5, 1, 2, 4, 2, 1, 2, 5, 8];
```
```
const ans = {};
```

for (let i = 0; i < arr.length; i++) {
```
  let curr = arr[i];
```
  if (ans[curr]) {
    ans[curr] = ans[curr] + 1;
```
  } else {
```
    ans[curr] = 1;
```
  }
```
```
}
```
/*
use [] in all cases. but specially when you dont know if the current key is there or not

bcz using obj.key will give error if key is not there in obj

also use obj[key] or obje["key"] are same

obj["key"]: if key has space or number
*/



Closures
A closure is formed when a function remembers its lexical scope even when the function is executed outside that scope.
Closure = function + its lexical environment (variables from parent function).
```
function x() {
```
```
  let a = 1;
```
```
  function y(){
```
    console.log(a);
```
  }
```
  return y;
```
}
```
```
let z = x(); //function along with its lexical scope is returned
```
console.log(z)

z();// 1


another example
```
function x() {
```
```
  let a = 1;
```
```
  function y(){
```
    console.log(a);
```
  }
```
  a =100; //updated in memory
  return y;
```
}
```
```
let z = x(); //function along with its lexical scope is returned
```
console.log(z)

z(); //100 - since reference of a was stored so it will get updated value, i.e.100

Because the inner function captures the reference to variables, not the value


Examples of closures and set time out

```
function x() {
```
```
  var i = 1;
```
  setTimeout(function () {
    console.log(i);
    console.log("one");
```
  }, 3000);
```
  console.log("two");
```
}
```
x();
// output: two  1  one


Important  //printing 1 to 5 after each seconds
```
function x() {
```
  console.log("one");
  for (var i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), i * 100);
```
  }
```
  console.log("two");
```
}
```
x();
/* output
one
two
5
5
5
5
5
*/


Note: you will get 5 s after 100, 200, 300, 400, 500 ms not after 500ms all the 5 will apprear at once bcz delay is decided at the moment setTimeout is registered, Not when the callback runs
But just using let instead of var, things changes.
Bcz in case of var, they are refering to the same memory space and we know set time out runs only aftet the GEC finished its code. 
So at the end for loop has ended runing and now the value of i is 5 at the end and at last set time out will print the same value of i that is 5

But in case of let, a new block scope of i is created with the updated value. So now each callback method has closure of updated i 
```
function x() {
```
  console.log("one");
  for (let i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), i * 100);
```
  }
```
  console.log("two");
```
}
```
x();
/* output
one
two
0
1
2
3
4
*/


Or we can use closures to achieve the same
We are passing a new copy of variable each time to the function.
In this way we are passing i to the function which is stored in x and has separate copy 
```
function x() {
```
  console.log("one");
  for (var i = 0; i < 5; i++) {
```
    function close(x) {
```
      setTimeout(() => console.log(x), x * 100);
```
    }
```
    close(i);
```
  }
```
  console.log("two");
```
}
```
x();
/* output
one
two
0
1
2
3
4
*/


Summary
✔ Why var prints 5 five times:
```
var is function scoped, so the same i is shared
```
loop completes before timeouts fire
final value of i = 5
all callbacks print the same shared value

✔ Why timings are different:
Each setTimeout is registered with a delay of i * 100
delay comes from old i value
callback prints new i value

✔ Why let works:
```
let is block scoped
```
Each iteration gets a new copy of i
Each callback closes over its own copy

✔ Closure fix (IIFE/extra function):
manually creates a new variable (x)
each callback closes over its own x
works even with var



Example same as setTimeout with normal functions
```
var arr = [];
```

for (var i = 0; i < 3; i++) {
  arr[i] = function () {
    console.log(i);
```
  };
```
```
}
```

arr[0](); //3 --> as var has function scope as all i refers to the same updated value that is 3
arr[1](); //3
arr[2](); //3


same can be fixed using let as it has block scope

Closure: a function along with reference of its outer environment. Function + lexical scope
Each function has access to its outer variables, so even when the function is executed in other scope then also it will have the access to its original lexical variable
Closure = Function + Its Lexical Environment (variables it can access).
A closure forms when:
A function is defined inside another function
It remembers the variables from its outer function
Even after the outer function has finished execution
And even if the inner function is executed in a different scope

Example:
```
function outer(){
```
```
    function inner(){
```
        console.log(x);
```
    }
```
```
    let x =10;  // even though let has block scope, it is accessible outside bcz of clousers. 
```
    // x can be declared anywhere, just it has to be above return statement
    return inner;
```
}
```

outer()(); //curying //10

closure with currying
```
function outest() {
```
```
  function outer(val) {
```
```
    function inner() {
```
      console.log(x, val);
```
    }
```
```
    let x = 10;
```
    return inner;
```
  }
```
  return outer;
```
}
```

// passing argument to second level function
outest()("value")(); //10 value


change in values of x – game of scopes
```
function outest() {
```
```
  function outer(val) {
```
```
    function inner() {
```
      console.log(x, val);
```
    }
```
```
    let x = 10;
```
    return inner;
```
  }
```
  return outer;
```
}
```

```
let x = 100; 
```
outest()("value")(); //10 value- even after x=100, as it is new variable to the function, outest function has copy of refernce of inner variable and it will refer to that only


but if x is not present in inner scope then:
```
function outest() {
```
```
  function outer(val) {
```
```
    function inner() {
```
      console.log(x, val);
```
    }
```
    // let x = 10;
    return inner;
```
  }
```
  return outer;
```
}
```

```
let x = 100; 
```
outest()("value")(); //100, as the x was not found in the inner scope, JS will look for x at outer scope

using closures to implement encapsulation
//just a mistake you might do
```
function counter() {
```
```
  let count = 0;
```
```
  function increment() {
```
    count++;
```
  }
```
  console.log(count); // value is getting initalized, as clg is after function, outisde
  return increment;
```
}
```

```
let counterfun = counter(); //0 as the value is getting initalized
```
counterfun(); 
counterfun();
counterfun();


//real example of encapsulation
```
function counter() {
```
```
  let count = 0;
```
```
  function increment() {
```
    count++;
    console.log(count); // value is incrementing inside
```
  }
```
  return increment;
```
}
```

```
let counterfun = counter(); 
```
counterfun();//1 -- as you are executing increment function repeatedly
counterfun();//2
counterfun();//3


we can create mulitple counters using the same code
```
function counter() {
```
```
  let count = 0;
```
```
  function increment() {
```
    count++;
    console.log(count); // value is incrementing inside
```
  }
```
  return increment;
```
}
```

```
let counterfun = counter();
```
counterfun(); //1 -- as you are executing increment function repeatedly
counterfun(); //2
counterfun(); //3

```
let counterfun2 = counter(); // a new counter is created, unaffected by above counter
```
counterfun2(); //1
counterfun2(); //2


using constructors we can make this scalable
```
function counter() {
```
```
  let count = 0;
```
  this.increment = function () {
    count++;
    console.log(count);
```
  };
```
  this.decrement = function () {
    count--;
    console.log(count);
```
  };
```
```
}
```

```
let counterfun = new counter();
```
counterfun.increment(); //1
counterfun.increment();//2
counterfun.decrement();//1

Important interview closure output question
```
function createIncrement() {
```
```
  let count = 0;
```

```
  function increment() {
```
    count++;
```
  }
```

```
  let message = `Count is ${count}`;
```

```
  function log() {
```
    console.log(message);
```
  }
```

  return [increment, log];
```
}
```

```
const [inc, log] = createIncrement();
```

inc();
inc();
inc();

log(); //0 --> as the message was created at the time of execution and later we update count but message is not recreated, even though count is 3


most important interview trap
```
function setup() {
```
```
  let data = { value: 1 };
```

  return {
    inc() {
      data.value++;
```
    },
```
    replace() {
      data = { value: 100 };
```
    },
```
    print() {
      console.log(data.value);
```
    },
```
```
  };
```
```
}
```

```
const obj = setup();
```

obj.inc(); // data.value = 2
obj.print(); // output 2

obj.replace(); // data replaced with new object --> value: 100

obj.inc(); // data.value = 100+1 ==101
obj.print(); // output 101



some important traps points to remember for closures
Advantages of Closures (Point Form, Interview-Ready)
1. Data Privacy
Enables creation of private variables
Protects data from being accessed or modified directly
Helps simulate “private” fields like in OOP
2. Persistent State
Function can remember values between calls
Maintains internal state even after outer function finishes
3. Encapsulation & Abstraction
Exposes only necessary functionality
Hides internal logic and implementation details
Supports clean modular design
4. Module Pattern
Helps structure large programs
Avoids polluting the global scope
Allows creation of public and private APIs
5. Function Factories
Allows creation of functions with preset data
Supports generating multiple customized functions
6. Useful in Asynchronous Programming
Callbacks, promises, and timers can remember outer variables
Necessary for maintaining state in async flows
7. Supports Currying
Closures allow splitting a function into smaller chained functions
Enables advanced functional programming patterns
8. Enables Memoization & Caching
Remembering previous inputs
Improves performance by storing computed results
9. Foundation of Higher-Order Functions
Used heavily in frameworks like React (hooks)
Disadvantages: overconsumption of memory, garbage collection issue as clousure variables are not garbage collected till program expires, can lead to memory leak, browser freez as the memory is accumulated
Garbage collector
Garbage Collection (GC) is a process in the JavaScript engine that automatically frees memory by removing variables and objects that are no longer reachable or used by the program.
JS uses mark and sweep algorithm for garbage collection
But since closures remembers the reference of its lexical environment variables, they cant be garbage collected
```
function a() {
```
```
  var a = 10,
```
    b = 20;
  return function () {
    console.log(a);
```
  };
```
```
}
```

```
var y = a();
```
y(); //10
console.log(a);//accesible: [Function: a]
console.log(b); //not accessible, garbage collected: ReferenceError: b is not defined

// b is garbage collected as b was not used anywhere 
// since a was used inside the function so is was not garbage collected


First class & anonymous functions
Function statement/declaration and function expression
a(); // function statement is hoisted and can be accessed before declaration

b(); //error -> hoisted based on data type, cant access here. b is variable here not a fuction

```
function a() {
```
  console.log("function statement/declaration");
```
}
```

```
let b = function () {
```
  console.log("function expression");
```
};
```


Anonymous functions
// anonymous function -- function without a name
// anonymous functions are used when function are used as a values

```
function (){
```
    console.log("anonymous function")
```
}
```

Named function expressiong
// named function expression

```
var a = function fun() {
```
  console.log("named fun expression", fun); //fun can be accessed inside
```
};
```

a(); // valid --> named fun expression [Function: fun]
fun(); // error, fun is not defined, cant access outside


parameter vs arguments
```
function fun(param){
```
    console.log(param);
```
}
```

fun("argument");

First class functions/citizens
The ability of functions to be used as values, to be passed from another function
we can pass a function to function , return a function 



callback function: 
when we pass a function to another function
they gives access to asynchronous world
we give responsibility to another program to call callback function
setTimeout(fun,1000); //calling fun using async programming

```
function fun(){
```
    console.log("callback function")
```
}
```

We should not block the main thread, i.e, use aync programming to execute task that takes time
Event listeners are heavy, they forms closures. Garbage collector cannot remove event listeners so we need to remove event listeners when we are not using them
Async programming
Callback hell/pyramid of doom: when there are nested callbacks inside each other.
Inversion of control: we as a developer lose control over program as we are using callbacks. We don’t know if our functions will be called or not, how many times they will be called, what if there are some errors while calling the callback functions. 
Promises
A promise is an object representing the eventual completion or failure of an asynchronous operation.
We attach the callback function with promises.
```
const cart = ["a", "b", "c"];
```

// passing the callback function
createOrder(cart, function (orderId) {
  proceedToPayment(orderId);
```
});
```

// attaching the callback function

```
const promise = createOrder(cart);
```

promise.then(function (orderId) {
  proceedToPayment(orderId);
```
});
```


Promises gives us the guarantee that our callback function will be called definetly and only once.
Promise guarantees that it will be resolved only once.
```
const GITHUB_API = "https://api.github.com/users/mojombo";
```

```
const user = fetch(GITHUB_API);
```

console.log(user);//fetch returns promise -- it is pending right now

promise has 3 states: pending, fulfilled(resolved), rejected
A JavaScript Promise has 3 States
1️ Pending
Initial state
Promise is still running
The async operation hasn’t finished yet
Neither fulfilled nor rejected

2️ Fulfilled (Resolved)
Operation completed successfully
Promise now has a value
.then() handlers will run

3️ Rejected
Operation failed
Promise now has a reason (error)
.catch() handlers will run
After a promise is settled (fulfilled or rejected), its state cannot change, it becomes immutable.
Creating the promise
We create promise using promise constructor.
Promise takes a callback function. Which has two parameters, resolve and reject.
We can resolve and reject promise only once.
we attach callback functions with promise.
// const cart = ["a","b","c"];
```
const cart = [];
```

// accepts cart and returns a promise
```
function createOrder(cart) {
```
  // creating the promise
```
  const pr = new Promise((resolve, reject) => {
```
    if (cart.length == 0) {
```
      const err = new Error("emtpy cart");
```
      reject(err);
```
    } else {
```
```
      const msg = "success";
```
      resolve(msg);
```
    }
```
```
  });
```

  return pr;
```
}
```

// consuming the promise
createOrder(cart)
  .then((data) => console.log(data))
  .catch((err) => console.log("promise fail: ", err));

Promise chain
Note: don’t forget to return from a promise chain to consume it to next level in hierarchy
```
const cart = ["a", "b", "c"];
```
// const cart = [];

// accepts cart and returns a promise
```
function createOrder(cart) {
```
  // creating the promise
```
  const pr = new Promise((resolve, reject) => {
```
    if (cart.length == 0) {
      reject(new Error("emtpy cart"));
```
    } else {
```
      resolve("success");
```
    }
```
```
  });
```

  return pr;
```
}
```

// consuming the promise -promise chaining
createOrder(cart)
  .then((data) => {
    console.log(data);
```
    const newPr = new Promise((resolve,reject)=>{
```
        if(true) resolve("level 1 resolved");
        else reject("level 1 reject")
```
    });
```
    return newPr
```
  })
```
  .then((data)=>console.log(data))
  .catch((err) => console.log("promise fail: ", err));


Catch will handle error at any level of chain, if the promise fails at the level 1 then also the .catch will be called and it will handle error.
We can have mulitple .catch methods. Each .catch will be responsible to handle errors above them. And .then after .catch will be executed normally
Promise API
Promise.all – fail fast
1️ Takes an array of promises
	Promise.all([p1, p2, p3])

2️ Runs all promises in parallel
p1 → finishes in 3s
p2 → finishes in 1s
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
```
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
```
```
const p2 = new Promise(res => setTimeout(() => res("B"), 1000));
```
```
const p3 = new Promise(res => setTimeout(() => res("C"), 2000));
```

Promise.all([p1, p2, p3]).then(result => {
  console.log(result); // ["A", "B", "C"]  → after 3s
```
});
```



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
```
  { status: "fulfilled", value: val1 },
```
```
  { status: "rejected",  reason: err2 },
```
```
  { status: "fulfilled", value: val3 }
```
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
```
  { status: "fulfilled", value: "A" },
```
```
  { status: "rejected", reason: "Error" },
```
```
  { status: "fulfilled", value: "C" }
```
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
```
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
```
```
const p2 = new Promise(res => setTimeout(() => res("B"), 5000));
```
```
const p3 = new Promise((res, rej) => setTimeout(() => rej("Error from p3"), 2000));
```

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
```
const p1 = new Promise(res => setTimeout(() => res("A"), 3000));
```
```
const p2 = new Promise((res, rej) => setTimeout(() => rej("Fail"), 1000));
```
```
const p3 = new Promise(res => setTimeout(() => res("C"), 2000));
```

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
```
}
```

// resolving the promise
```
const data = getData().then((val) => console.log(val));
```


difference between promise and async await
```
const p = new Promise((resolve, reject) => {
```
  if (true) {
    resolve("resolved");
```
  } else reject("rejected");
```
```
});
```

```
function getData() {
```
  p.then((data) => console.log(data));
```
}
```
getData();

async function handlePromise() {
  // instead of p.then ==> await p;, it will return the settled value
```
  const data = await p;
```
  console.log(data);
```
}
```
handlePromise();


await will hold the execution untill the promise is settled. While normal promise can’t do that.
```
const p = new Promise((resolve, reject) => {
```
  if (true) {
    resolve("resolved");
```
  } else reject("rejected");
```
```
});
```

```
function getData() {
```
  p.then((data) => console.log(data));
  console.log("after code"); //cant hold execution
```
}
```
// getData();
/*output
after code -- cant hold execution
resolved
*/

async function handlePromise() {
  // instead of p.then ==> await p;, it will return the settled value
```
  const data = await p;
```
  console.log(data);
  console.log("after async code"); //-- hold execution
```
}
```
handlePromise();
/*output
resolved  
after async code -- hold execution
*/



Set time out confusion - important
If we have multiple set time outs, then they will be registered at the time when the function is executing, then they both will wait for 1000ms, and as there timer is same they will get executed by event loop from callback/macrotask  queue to callstack.
```
const p = new Promise((resolve, reject) => {
```
  if (true) {
    setTimeout(() => {
      resolve("resolved");
```
    }, 1000);
```
```
  } else reject("rejected");
```
```
});
```

async function handlePromise() {
```
  const data = await p;
```
  console.log(data);
  console.log("one"); //-- hold execution

```
  const data2 = await p;
```
  console.log(data);
  console.log("two"); //-- hold execution
```
}
```

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
```
const GITHUB_API = "https://api.github.com/users/mojombo";
```

// fetch returns a promise
```
const data = await fetch(GITHUB_API); //returns readble stream
```
```
const response = await data.json(); // convert that readable stream using JSON, which also returns promise
```

console.log(response);



using fetch direclty using promise chain
// or you can do this also

fetch(GITHUB_API)
  .then((res) => res.json())
  .then((response) => console.log(response));


Error handling in async await
Using try, catch
```
const GITHUB_API = "https://api.github.com/users/mojombo";
```

async function getData() {
  try {
```
    const data = await fetch(GITHUB_API);
```
```
    const response = await data.json();
```
    console.log(response);
```
  } catch (err) {
```
    console.log("got some error: ", err);
```
  }
```
```
}
```

getData();


or we can use .catch as async function returns promise
```
const GITHUB_API = "https://api.github.com/users/mojombo";
```

async function getData() {
```
  const data = await fetch(GITHUB_API);
```
```
  const response = await data.json();
```
  console.log(response);
```
}
```

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
```
const student = {
```
  name: "abc",
  printName: function () {
    console.log(this.name);
```
  },
```
```
};
```

student.printName();

```
const person = {
```
  name: "xyz",
```
};
```

// the value of this becomes person inside student object
student.printName.call(person);


inside arrow function
arrow functions don’t have there own this binding. They take the value of this from there lexical environment/parent environment
this inside the HTML:
referene to HTML element
 will be the html button if it is inside the button tag





Call/Apply/Bind
Note: arrow function ignores call/apply/bind bcz they take this from lexical scope
This: the object that calls the function
```
const user = {
```
  name: "abc",
  sayHi() {
    console.log(this.name);
    console.log(this); //{ name: 'abc', sayHi: [Function: sayHi] }
```
  },
```
```
};
```

user.sayHi(); //abc


why we need call/apply/bind?
Sometimes we want manually decide what ‘this’ should be.
```
function show() {
```
  console.log(this);
```
}
```

show(); //global obj

```
const obj = {
```
  name: "abc",
```
};
```

show.call(obj);//{name:"abc"}


call
Calls function immediately
Sets this to given obj
Passes argument individually
```
function greet(a, b) {
```
  console.log(this.name, a, b);
```
}
```

```
const obj = { name: "abc" };
```
greet.call(obj, 5, 10); //abc 5 10


Apply
Calls function immediately
Sets this to given obj
Passes arguments as an array
```
function greet(a, b) {
```
  console.log(this.name, a, b);
```
}
```

```
const obj = { name: "abc" };
```
greet.apply(obj, [5, 10]); //abc 5 10


note: passing individual elements gives type error
Bind
Does not calls function immeditely
Returns a new function with ‘this’ permanent binding
Takes argument individually
Useful in callbacks and event handlers
```
function greet(a, b) {
```
  console.log(this.name, a, b);
```
}
```

```
const obj = { name: "abc" };
```
```
const fn = greet.bind(obj, 5, 10); //abc 5 10
```
fn(); //abc 5 10

note: passing arguments as array will not give error, it will be treated as single argument then
```
function greet(a, b) {
```
  console.log(this.name, a, b);
```
}
```

```
const obj = { name: "abc" };
```
```
const fn = greet.bind(obj, [5, 10]);  //[5,10] will be treated as single argument
```
fn(); //abc [ 5, 10 ] undefined

Note: arrow function ignores call/apply/bind bcz they take this from lexical scope
greet = (a, b) => {
  console.log(this.name, a, b); //undefined 5 10
  console.log(this); //{}
```
};
```

```
const obj = { name: "abc" };
```
```
const fn = greet.bind(obj, 5, 10);
```
fn(); //undefined 5 10 as this is module/global obj here

console.log("this: ", this); //{}


another example
```
const user = {
```
  name: "Leo",
  show: () => {
    console.log(this);
```
  },
```
```
};
```

user.show.call({ name: "Not Leo" }); //{}


very important interview traps
Loosing ‘this’ 
```
const user = {
```
  name: "Leo",
  show() {
    console.log(this.name);
```
  },
```
```
};
```

```
const ref = user.show;
```
console.log(ref); //[Function: show] – ref is just a function now, no obj calls it
ref(); // undefined or error


Ref is storing the whole function, but here we looses this binding
So when we call the function using ref(), we have no this binding and this is now global/module obj {} in case of node, so we get undefined.
Fix this: using call/apply/bind
```
const user = {
```
  name: "Leo",
  show() {
    console.log(this.name);
```
  },
```
```
};
```

```
const ref = user.show;
```
ref.call(user); // leo


Call/bind inside setTimeOut: loosing ‘this’ context
```
const obj = {
```
  name: "Leo",
  hi() {
    setTimeout(this.say, 0); //this.say is passed as callback function, loosing this context
```
  },
```
  say() {
    console.log(this.name);
```
  },
```
```
};
```

obj.hi(); //undefined


fix: using bind (as it binds this and returns a function)
```
const obj = {
```
  name: "Leo",
  hi() {
    setTimeout(this.say.bind(obj), 0); //this.say is passed as callback function, loosing this context, and bind returns a function
```
  },
```
  say() {
    console.log(this.name);
```
  },
```
```
};
```

obj.hi(); //Leo


note: this.say.bind(obj) and this.say.bind(this) both are same here
antoher example: 
```
var name = "Global";
```
```
const obj = {
```
  name: "Leo",
  say: function () {
    console.log(this.name);
```
  },
```
```
};
```

setTimeout(obj.say, 0); //undefined --> as obj.say is passed as callback function loosing its this context


fix: use bind to bind the this context with obj and return the function with preserve this context binding
setTimeout(obj.say.bind(obj), 0); //Leo

antother question
```
function f() {
```
  console.log(this);
```
}
```
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
```
const a = [{ x: 1 }];
```
```
const b = [...a]; //b = [{x:1}]
```

b[0].x = 99;
console.log(a); //[{ x: 99 }]; --> shallow coppy, nested level coppied by reference
console.log(b); //[{ x: 99 }]; --> shallow coppy, nested level coppied by reference


spread with non-premitive
```
const a =1;
```
```
const b = "hello";
```
console.log(...b); // h e l l 0
console.log(...a); //cant spread non-premitives except string

spread with function
```
function add(a, b, c) {
```
  return a + b + c;
```
}
```
```
const nums = [1, 2, 3];
```
console.log(add(...nums));//spread at argument level


rest with functions
// rest operator -- parameter level --> collecting all the values passed
```
function add(...rest) {
```
  return rest.reduce((item,acc)=>item +=acc);
```
}
```
```
const nums = [1, 2, 3];
```
console.log(add(1,2,3,4,5));


shallow copy : copied by value at first level, changing one cant change another
```
const a = {name:"user", age:22};
```
```
const b ={...a};
```

// shallow copy at first level
b.name = "newUser";
console.log(a);//{ name: 'user', age: 22 }
console.log(b);//{ name: 'newUser', age: 22 }

shallow copy: copied by reference at nested level, changing one leads to change another
```
const a = {name:"user", age:22, val:[1,2,3]};
```
```
const b ={...a};
```

// shallow copy at first level
b.name = "newUser";
b.val[0] = 100;
console.log(a);//{name:"user", age:22, val:[100,2,3]};
console.log(b);//{name:"newUser", age:22, val:[100,2,3]};

spread merges overwiting later keys, last value will be used
```
const a = { x: 1 };
```
```
const b = { x: 2 };
```

console.log({ ...a, ...b }); //{ x: 2 }


note: undefined and null cant be spread or rest
Rest operator
Note: rest has to be last parameter
spread with function
```
function add(a, b, c) {
```
  return a + b + c;
```
}
```
```
const nums = [1, 2, 3];
```
console.log(add(...nums));//spread at argument level


rest with functions
// rest operator -- parameter level --> collecting all the values passed
```
function add(...rest) {
```
  return rest.reduce((item,acc)=>item +=acc);
```
}
```
```
const nums = [1, 2, 3];
```
console.log(add(1,2,3,4,5));


rest with array destructuring
```
const [a, ...rest] = [1, 2, 4, 5];
```
console.log(a);//1
console.log(rest);//[2,4,5]


rest can be empty
```
const [a, ...rest] = [1];
```
console.log(a);//1
console.log(rest);//[]


note: rest also do shallow copy like spread, i.e., copies by value only top level and nested levels are copied by reference

you can re-arrange order of element to extract values
```
const obj = { a: 1, b: 2, c: 3 };
```
```
const { b, ...rest } = obj;
```

console.log(rest); // {a:1, c:3}


important react bug – interview 
```
const user = { name: "Leo" };
```
```
const list = [user, user]; //[{ name: "Leo" },{ name: "Leo" }]
```

```
const newList = [...list]; //[{ name: "Leo" },{ name: "Leo" }]
```

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
.Step 1: create slice
Note: slice creates reducers
Basic slice structure
```
import { createSlice } from "@reduxjs/toolkit";
```

```
const intialState = {
```
  value: 0,
```
};
```

```
const counterSlice = createSlice({
```
    name: "counter",
    initialState,
    reducers:{
        
```
    }
```
```
})
```

Complete code of slice
```
import { createSlice } from "@reduxjs/toolkit";
```

// initial state
```
const intitialState = {
```
  value: 0,
```
};
```

// contains name, intialState, reducers(logic)
// returns pure reducer function & actions
```
const counterSlice = createSlice({
```
  name: "counter", // used to prefix action types: "counter/increment"
  intitialState, //slice's intial state

  //synchrounous reducers - mutaing code bcz of immer
  reducers: {
    increment(state) {
      state.value += 1;
```
    },
```
    decrement(state) {
      state.value -= 1;
```
    },
```
    addBy(state, action) {
      // action.payload expected to be number
      state.value += action.payload;
```
    },
```
    reset(state) {
      state.value = 0;
```
    },
```
```
  },
```
```
});
```

// Named exports: action creators we can dispatch from components
```
export const { increment, decrement, addBy, reset } = counterSlice.actions;
```

// Default export: slice reducer to plug into the store
```
export default counterSlice.reducer;
```


Step 2: create store
   [Slices] ---> [STORE] ---> [React App]
Note: store combines reducers into one global state
configureStore creates store with: default values, built-in middleware like Thunk & immer

```
import { configureStore } from "@reduxjs/toolkit";
```
```
import counterReducer from "./mySlice/CounterSlice"; //reducer generated by RTK, we can give it any name
```

```
const store = configureStore({
```
  reducer: {
    // key: slice name, value: its reducer
    counter: counterReducer,
```
  },
```
```
});
```

```
export default store;
```


Step 3: connect store using Provider
Provider makes the store available to the entire app
Provider gives access to 
useSelector  it read state 
& useDispatch  it dispatches the action
```
import React from "react";
```
```
import Home from "./Home";
```
```
import { Provider } from "react-redux";
```
```
import store from "./Store";
```
```
const RTK = () => {
```
  return (
    // provider connects redux store to React
```
    <Provider store={store}>
```
```
      <Home />
```
```
    </Provider>
```
  );
```
};
```

```
export default RTK;
```



Step 4: implement RTK
useSelector: 
It access state.
It takes callback function which has access to slice: state.sliceName.variableName
useDispatch
It dispatches the action, it triggers the action that needs to be performed on state
Dispatch(action()). Note: dispatch is a variable that is accesing the useDispatch hook
```
import React, { useState } from "react";
```
```
import { useSelector, useDispatch } from "react-redux";
```
```
import { increment, decrement, addBy, reset } from "./mySlice/CounterSlice";
```

```
const Home = () => {
```
  //   select value from store
```
  const value = useSelector((state) => state.counter.value);
```
```
  const dispatch = useDispatch();
```

```
  const [amount, setAmount] = useState(0);
```

  return (
```
    <div>
```
```
      <h1>Count: {value}</h1>
```
```
      <div style={{ display: "flex", gap: 8, marginBottom: 12 }}>
```
```
        <button onClick={() => dispatch(increment())}>Increment</button>
```
```
        <button onClick={() => dispatch(decrement())}>Decrement</button>
```
```
        <button onClick={() => dispatch(reset())}>Reset</button>
```
```
      </div>
```

```
      <div>
```
```
        <input
```
          type="number"
          value={amount}
          onChange={(e) => setAmount(e.target.value)}
        />
```
        <button
```
          onClick={() => {
```
            const num = Number(amount) || 0;
```
            dispatch(addBy(num));
            setAmount(0);
```
          }}
```
        >
          Add By{" "}
```
        </button>
```
```
      </div>
```
```
    </div>
```
  );
```
};
```

```
export default Home;
```



React optimization
Shallow and deep comparison
In shallow comparison only reference is check for non premetive data types and for premetive data types value is checked
But in deep comparison each nested value is checked
In react everthing relly on shallow comparison as it is faster

```
const a = 10;
```
```
const b = 10;
```
```
const c = "10";
```

```
const o1 = { name: "a" };
```
```
const o2 = { name: "a" };
```

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
All rely on shallow comparison, that means even if the value is same and we are passing a new object each time then also react re-renders as it is fast
React.memo
useCallback
useMemo
```
useEffect
```
RTK
useMemo or memo
useMemo prevents re-rendering if the props don’t change.
Example of useMemo:
Counter component: it don’t have any props
here if we don’t wrap Counter componet in React.memo while exporting then it will re-render even though its props are not changing
```
import React, { useRef } from "react";
```

```
const Counter = () => {
```
```
  const count = useRef(0);
```
  return (
```
    <>
```
```
      <p>Render count: {count.current++}</p>
```
```
    </>
```
  );
```
};
```

```
export default React.memo(Counter);
```


Parent component: MemoHome
```
import React, { useState } from "react";
```
```
import Counter from "./Couter";
```

```
function MemoHome() {
```
```
  const [val, setVal] = useState(0);
```
  return (
```
    <>
```
```
      <p>Value from parent: {val}</p>
```
```
      <button onClick={() => setVal((prev) => prev + 1)}>Increase value</button>
```
```
      <Counter />
```
```
    </>
```
  );
```
}
```

```
export default UseMemoHome;
```

Note: use React.memo only when it is necessary as it has cost. React has to compare previous props with current props using shallow comparison which has cost
✔ useCallback — memoizes a function reference (stable function)
✔ useMemo — memoizes a computed value (stable value)
✔ React.memo — memoizes a component (skip re-render if props unchanged)
React virtualization/windowing
We use virtualization when we need to render a list or something that is really huge (like 1 million records in a list)
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
Fetch must be used inside useEffect. As fetch is async and react needs to know when to run it
Note: we cant use aync code inside custome hook, if we are using then use inside useEffect, else the async code will start working in the background that react don’t allow. React works syncronously

Render (fast, synchronous)
↓
```
useEffect triggers (async allowed)
```
↓
Fetch happens here
↓
State updates
↓
React re-renders with actual data
```
import React, { useState } from "react";
```
```
import Card from "./Card";
```
```
import useFetch from "./useFetch";
```
```
const URL = "https://jsonplaceholder.typicode.com/posts";
```
```
const PaginationHome = () => {
```
```
  const { value, loading, err } = useFetch(URL);
```
```
  const [startInd, setStartInd] = useState(0);
```
```
  const [endInd, setEndInd] = useState(5);
```

```
  const handlePrev = () => {
```
    setEndInd((prev) => prev - 5);
    setStartInd((prev) => prev - 5);
```
  };
```

```
  const handleNext = () => {
```
    setStartInd((prev) => prev + 5);
    setEndInd((prev) => prev + 5);
```
  };
```

  return (
```
    <div>
```
```
      <h1> I am paginatyion again</h1>
```
```
      {loading ? (
```
```
        <h1>I am still loading...</h1>
```
      ) : err ? (
```
        <h1>Error in fetching data</h1>
```
      ) : (
        value.slice(startInd, endInd).map((item) => {
          return <Card key={item.id} val={item} />;
```
        })
```
      )}

```
      <button onClick={handlePrev} disabled={startInd == 0 ? true : false}>
```
        Prev
```
      </button>
```
```
      <button onClick={handleNext} disabled={endInd == 30 ? true : false}>
```
        Next
```
      </button>
```
```
    </div>
```
  );
```
};
```

```
export default PaginationHome;
```



```
import React from "react";
```

```
function Card({ val }) {
```
  //   console.log(item);
  return (
```
    <div style={{ border: "2px solid black", padding: "5px", margin: " 5px" }}>
```
```
      <span>{val.id}</span>
```
```
      <p>{val.title}</p>
```
```
    </div>
```
  );
```
}
```

```
export default Card;
```


//fetching data using custom hook
```
import { useEffect, useState } from "react";
```

```
const useFetch = (URL) => {
```
```
  const [value, setValue] = useState([]);
```
```
  const [loading, setLoading] = useState(true);
```
```
  const [err, setErr] = useState(null);
```

  async function getData() {
    try {
```
      const data = await fetch(URL);
```
```
      const response = await data.json();
```
      setValue(response);
```
    } catch (err) {
```
      setErr(err);
```
    } finally {
```
      setLoading(false);
```
    }
```
```
  }
```

  //   getData must call inside useEffect as side effects(fetch) always comes inside useEffect or useLayoutEffect
```
  useEffect(() => {
```
    getData();
```
  }, [URL]);
```

  return { value, loading, err };
```
};
```

```
export default useFetch;
```



infinite scrolling using client side
scrollTop  how far you have scrolled from top
clientHeight → Visible height of the container, here 400px
scrollHeight → Total height of the scrollable content
```
import React, { useState } from "react";
```
```
import Card from "./Card";
```
```
import useFetch from "./useFetch";
```
```
const URL = "https://jsonplaceholder.typicode.com/posts";
```
```
const PaginationHome = () => {
```
```
  const { value, loading, err } = useFetch(URL);
```
```
  const [visibleCount, setVisibleCount] = useState(5);
```

```
  const handleScroll = (e) => {
```
```
    const { scrollTop, clientHeight, scrollHeight } = e.target;
```
    if (scrollTop + clientHeight >= scrollHeight - 5) {
      setVisibleCount((prev) => prev + 5);
```
    }
```
    // console.log(e.target);
```
  };
```

  return (
```
    <div
```
      onScroll={handleScroll}
      style={{
        height: "400px",
        overflowY: "auto",
        border: "2px solid red",
```
      }}
```
    >
```
      <h1> I am paginatyion again</h1>
```
```
      {loading ? (
```
```
        <h1>I am still loading...</h1>
```
      ) : err ? (
```
        <h1>Error in fetching data</h1>
```
      ) : (
        value.slice(0, visibleCount).map((item) => {
          return <Card key={item.id} val={item} />;
```
        })
```
      )}
```
    </div>
```
  );
```
};
```

```
export default PaginationHome;
```

rest of the code is same as pagination

//some advance part that I don’t know  using the advance way
// using sentinel div

```
import React, { useEffect, useRef, useState } from "react";
```
```
import Card from "./Card";
```
```
import useFetch from "./useFetch";
```
```
const URL = "https://jsonplaceholder.typicode.com/posts";
```
```
const PaginationHome = () => {
```
```
  const { value, loading, err } = useFetch(URL);
```
```
  const [visibleCount, setVisibleCount] = useState(5);
```
```
  const loaderRef = useRef(null);
```

```
  useEffect(() => {
```
```
    const observer = new IntersectionObserver((entries) => {
```
      if (entries[0].isIntersecting) {
        setVisibleCount((prev) => prev + 5);
```
      }
```
```
    });
```

    if (loaderRef.current) {
      observer.observe(loaderRef.current);
```
    }
```
```
  }, []);
```

  return (
```
    <div
```
      style={{
        height: "600px",
        overflowY: "auto",
        border: "2px solid red",
```
      }}
```
    >
```
      <h1> I am paginatyion again</h1>
```
```
      {loading ? (
```
```
        <h1>I am still loading...</h1>
```
      ) : err ? (
```
        <h1>Error in fetching data</h1>
```
      ) : (
        value.slice(0, visibleCount).map((item) => {
          return <Card key={item.id} val={item} />;
```
        })
```
      )}
```
      {/* using sentinel(placeholdera) div */}
```
```
      <div
```
        ref={loaderRef}
        style={{ height: "40px", backgroundColor: "grey" }}
      ></div>
```
    </div>
```
  );
```
};
```

```
export default PaginationHome;
```



Lazy loading:  Suspense + React.lazy
Lazy loading means load something only when its needed
React.lazy will take a callback function in which it will import the components only when needed and returns the component
Suspense will show a fallback code untill the component is loaded
```
import React, { Suspense, useState } from "react";
```
```
const BigComponent = React.lazy(() => import("./BigComponent"));
```
```
const LazyLoadingHome = () => {
```
```
  const [show, setShow] = useState(false);
```
```
  const handleShow = () => {
```
    setShow((prev) => !prev);
```
  };
```
  return (
```
    <div>
```
```
      <h1>Hello from lazy</h1>
```
```
      <button onClick={handleShow}>Show</button>
```
```
      {show && (
```
```
        <Suspense fallback={<h1>I am fallback</h1>}>
```
```
          <BigComponent />
```
```
        </Suspense>
```
      )}
```
    </div>
```
  );
```
};
```

```
export default LazyLoadingHome;
```






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
addEventListener("click", handler, true) :- here true will enable event capturing
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
How React renders the code
⭐ How React Starts Rendering — The Root Connection
Every React app has two important files:

1️ index.html (in public folder)
```
<!DOCTYPE html>
```
```
<html>
```
```
  <head>
```
```
    <title>React App</title>
```
```
  </head>
```
```
  <body>
```
```
    <div id="root"></div> <!-- React mounts here -->
```
```
  </body>
```
```
</html>
```
React does not recreate the entire HTML page.
It only takes control of the element with id="root".
This is the mounting point.

2️ index.js / main.jsx (React entry file)
In React 18+:
```
import React from "react";
```
```
import ReactDOM from "react-dom/client";
```
```
import App from "./App";
```

```
const root = ReactDOM.createRoot(document.getElementById("root"));
```

root.render(
```
  <React.StrictMode>
```
```
    <App />
```
```
  </React.StrictMode>
```
);
This code does 3 things:
✔ 1. Finds the root div in index.html
document.getElementById("root")
✔ 2. Creates a React root
ReactDOM.createRoot(...)
This is part of React Fiber (concurrent rendering).
Before React 18, the old SDK used:
ReactDOM.render(<App />, document.getElementById("root"));
✔ 3. Renders your <App /> component into the root div
Everything inside your React app (components, state, UI) gets rendered inside this root div.
React will control this one div and update only what changes, thanks to:
Virtual DOM
Reconciliation
Fiber

🔥 Final Summary
React attaches itself to a root DOM node (usually <div id="root">) in index.html.
In React 18, ReactDOM.createRoot() creates a concurrent rendering root, and root.render(<App />) tells React to take over that DOM node and render the component tree inside it.
REST



API
API (Application Programming Interface)
→ A set of rules that allows two software systems to communicate.

⭐ REST = Representational State Transfer
Key characteristics:
Uses HTTP methods:
GET → Read
POST → Create
PUT/PATCH → Update
DELETE → Remove
Data formats:
JSON (most common)
XML (older)
Stateless → Server does not store client state
Cacheable → Responses can be cached
SOAP = Simple Object Access Protocol
Mostly used in enterprise systems like banking, government, finance.
Characteristics:
Uses XML only
Very strict structure → requires a “SOAP Envelope”
Built-in error handling
Has official security standards (WS-Security)
Heavier → more complex than REST

CSS
🎯 3. Responsive Units (MUST know)



React practical

React batch update
✅ Correct Understanding (Polished Version)
✔ React batches state updates
True.
When you call setState, React schedules the update, it does not update state immediately during that same render.

✔ Stale value happens ONLY when:
You read the old state value immediately after calling setState in the same synchronous event.
Example:
setChars(e.target.value);
countCharacters(chars); // chars is stale here!
Here, chars is still the old value.

❌ But this part you said is slightly off:
"so either update that state inside useEffect or use functional state update"
Let me correct it:

❌ Misconception:
“Use useEffect to fix stale values”
❌ Not true.
```
useEffect is not meant for fixing stale state; it is for handling side effects.
```
Using useEffect to fix stale values is usually a wrong pattern.

✔ Correct rule:
Use functional updates only when your next state depends on previous state.
Example:
setCount((prev) => prev + 1);
This avoids stale values because React gives you the latest state automatically.

⭐ So here is the REAL correct rule set:

✅ Rule 1:
If the next state depends on the previous state,
👉 use functional state updates.
Example:
setTurn(prev => prev === "X" ? "O" : "X");

✅ Rule 2:
If you need the updated state immediately after typing/input,
👉 use the event value, not the stale state.
Example:
onChange={(e) => {
```
  const value = e.target.value;
```
  setChars(value);
  setCount(value.length); // correct
```
}}
```

❌ Rule 3 (VERY IMPORTANT):
Do NOT use useEffect to “fix” stale state inside event handlers.
```
useEffect(() => {
```
  setCount(chars.length);
```
}, [chars]);
```
This works, but it’s:
extra re-render
not needed
not idiomatic
misuse of useEffect
```
useEffect is for side effects, not syncing logic.
```

⭐ SUPER IMPORTANT DISTINCTION
❌ useEffect does NOT fix stale state
It just runs after render, which makes it look like it's giving you the new value.
But the correct pattern is NOT:
“Use useEffect when state becomes stale.”
The correct pattern is:
✔ Avoid stale state by:
using event values
using functional setState
computing values inside render
not storing derived values in state

🎯 Your final corrected statement (the one you should memorize):
React state updates are asynchronous, so you cannot rely on state inside the same event where you just updated it.
To avoid stale state:
use functional updates when next state depends on previous state, OR
use the event value directly, OR
compute the derived value inside render
But not useEffect unless it's a real side effect.

If you want, I can explain:
When stale state happens
When it doesn’t
How React’s batching works
Why Tic Tac Toe broke because of this
Just say:
👉 “Explain stale state with React batching properly.”

Session and local storage
Session storage: persists till session life only, used in coockies. Cookies are send to server while making network calls.
Coockie storage: less
Session storage: 
Local storage: can persisit even after the session ends, even if we shut down the system. Has highest memory capacity , 5MB at least
Uses of local storage: caching, optimization – its less expensive that server fetching data, they follow the same origin policy
Same origin: protcol (hhtp/https) + host(abc.com) + port(8080)





Iterable vs enumerable
Iterable → I iterate VALUES (for...of), note we can use for … in for arrays also, but to be on safe side use for…of only
Enumerable → I enumerate KEYS (for...in)

Keys and entries

Status codes
✅ 1xx — Informational
100 Continue → Server received headers, client should continue sending body.

⭐ 2xx — Success
200 OK → Everything successful.
201 Created → New resource created (POST success).
204 No Content → Success but empty body (DELETE success, PUT success).

⭐ 3xx — Redirects
301 Moved Permanently → Resource moved; clients should update URL.
302 Found → Temporary redirect.
304 Not Modified → Client can use cached version.

⭐ 4xx — Client Errors
400 Bad Request → Client sent invalid data.
401 Unauthorized → Authentication required or failed (JWT expired).
403 Forbidden → Authenticated but NOT allowed.
404 Not Found → Resource doesn’t exist.
409 Conflict → Duplicate data or version conflict (unique email exists).
422 Unprocessable Entity → Validation errors.

⭐ 5xx — Server Errors
500 Internal Server Error → Server crashed.
502 Bad Gateway → Upstream server returned bad response.
503 Service Unavailable → Server down or overloaded.
504 Gateway Timeout → Server took too long to respond.

🎯 Memory Trick for Interviews
401 = You’re not logged in
403 = You’re logged in but not allowed
404 = Wrong address
409 = Duplicate
500 = Developer messed up

Array.from
```
const a = 5;
```
```
const b = 3;
```

```
const arr = Array.from({ length: a }, () =>
```
  Array.from({ length: b }, () => ({}))
);
console.log(arr);

Difference betwee encoding, hsahing & encryption

Encoding: it is done to store data. Example: in URL space is strored as %20 as space is not allowed
Hashing: data is stored in secure way using some algortihms, it is irrevesible. Used to store data
Encryption: data can be unencrypted, used for secure communication.

CSS
Position
Static: normal flow, no access to top,bottom,let,rightz index
Absolute: positioned to its parent who has position property apart rom static. Item will be taken out of its normal flo
Relative: item will be positioned to its normal flow, and will not be taken out of its normal flow
