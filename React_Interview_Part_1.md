React Interview Questions and asnwers topic wise


Virtual DOM, fiber & reconcilliation
Create Root method creates a DOM like structure, virtual DOM
Problem with virtual DOM,
As I was updating one node, another came then I update them, then in between updation a new update came so I need to update the DOM every time.
Solution: a little pause between the updates so that some concurrent updated can be updated in one go: react fiber
 Don’t update UI immediately, and if you have already done some update calls then drop them and call those update along with new updates.

Virtual DOM: reconsiliation alogrithm: the process in which react compares DOM and VDOM and find the difference and update only those differences in the actual DOM
React Fiber: 
React fiber improves react animation, layouts (as continue ui updation by vdom makes page slow)
React fiber does incremental rendering: the ability to split rendering into chunks and spread it out over mulitple frames
Key features or react fiber is: the abilty to pause, abort or reuse work as new update comes in, it assign priority to different types of updates.
React fiber is closely related to hydration: in server side rendering (SSR) as first the html loads (HTML is created at server side) but the whole page is not interactive yet, adding interactivity through JS to plain HTML page web layout is known as hydration

React reconciliation algorithm: 
The algo react uses to diff one tree with another to dermine which part to be changed.
The feature of react that reconside what to update and what not to update using the diffing algorithm(diffing algo: the process of comparing DOM and VDOM and find out what to update based on changes and update only those parts/node of DOM tree)
Reconciliation is the algorithm behind the VDOM
Note: in react update is done by useState
Different component types are assumed to generate different trees, react will not diff them, it will replace the old tree completely
Diffing of list is done using keys, keys should be stable, unique and predictable
Rules of react fiber
Different element type: replace entire subtree
Same type: update props, using diff
Keyed lists: math old/new items by key
Summary
In UI it is not necessary for every update to be applied immediately. It can casae frame drop and bad UI experience
UI updates can be prioritized
-----------------------------------------------------------------------------------------------
DOM: dom is a tree representation of HTML page 
Why vdom invented: 
when JS changes the DOM, the brwoser recaldulated styles, layout and paints the page again which maked UI slow. 
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
When we update some variables inside jsx then its value will get updated (you can check that in console.log) but in UI its value wont get updated & reflected.
So to reflect the updated value in UI, we need hooks
Here value is getting updated but not reflected in the UI, for that we need hooks
import React from "react";

const LifeCycle = () => {
  let counter = 0;

  const add = () => {
    counter = counter + 1;
    console.log("counter: ", counter); // value is getting updated

  };
  const subtract = () => {
    counter = counter - 1;
    console.log("counter: ", counter); // value is getting updated

  };
  return (
    <div>
      <h1>Hello there!</h1>
      <span>{counter}</span>
      <button onClick={() => add()}>Add</button> {/*same old value */}
      <button onClick={() => subtract()}>Subtract</button>
      {/*same old value */}
    </div>
  );
};

export default LifeCycle;

Hooks can only be called inside React function components.
Hooks can only be called at the top level of a component.
Hooks cannot be conditional

useState hook
useState hook allows us to track state in a functional component
format
import {useState} from ‘react’
const [currState, callback_function_that_updates_state] = useState(initialValue);
note: 
useState doesn’t updates state immediately.
React performs batched, asynchronous updates to imporve perfromance
Render phase: react calls components  returns UI  compares with prev UI
Commit phase: react updates DOM  runs effect
Re-render: triggered when state changes
In short: react useState hook stores reactive values that trigger re-render and live inside React fiber, not inside component

The counter problem
import React from "react";
import { useState } from "react";

const LifeCycle = () => {
  const [counter, setCounter] = useState(0);

  const add = () => {
    setCounter(counter + 1);
    setCounter(counter + 1);
    setCounter(counter + 1);
    console.log("counter: ", counter);
    // even after we have 3 counter , value will be updated by +1 only
  };

  const subtract = () => {
    setCounter(counter - 1);
  };
  return (
    <div>
      <h1>Hello there!</h1>
      <span>{counter}</span>
      <button onClick={() => add()}>Add</button>
      <button onClick={() => subtract()}>Subtract</button>
    </div>
  );
};

export default LifeCycle;


Reason:
Value is getting updated by +1 only because react updates value in batches, here all 3 counters are taking the old same value and hence all are updating it to +1 only not +3
Solution
Use prevCounter. setCounter is provided by a callback function which takes the latest state value, and since we are getting latest values, we are updating the right value
const add = () => {
    setCounter((prevCounter) => prevCounter + 1);
    setCounter((prevCounter) => prevCounter + 1);
    setCounter((prevCounter) => prevCounter + 1);

    console.log("counter: ", counter);
    // now counter will update by +3
  };
One Note – we don’t need hooks always
This is working fine but we don’t need 2 useState
import React, { useState } from "react";
const LifeCycle = () => {
  const [val, setVal] = useState(1);
  //const [addVal, setAddVal] = useState(0);
  let addVal = val + 1;
  const update = () => {
    setVal((prev) => prev * 5);
  };
  return (
    <div>
      <h1>Hello</h1> <button onClick={update}>Update</button>{" "}
      <p>value: {val}</p> <p>Add value: {addVal}</p>{" "}
    </div>
  );
};
export default LifeCycle;


Sometimes we don’t need useState for every variable.
Here val is getting updated two times using two useState updating functions. 
But we can do it by using only one useState.
See as the value of val is getting updated using useState, the react component re-renders and prints the latest value of val, and since we are already getting the latest value of val we have the updated addVal also.


useEffect
useEffect hook allows to perform side effects in componnets like fetching data, api calls, timers, updating DOM, etc after react render
note: side-effect – anything that react cannot handle itself in the pure render phase.
Note: useEffect only runs AFTER the render, after commiting the DOM so that we can safely access DOM
Syntax:
useEffect(function, dependency)
Dependency
No dependency: runs on every render
[]: runs on the first render
[props, state]: runs when props or state changes
useEffect returns cleanup function. We can use them to clean the times, etc
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
useEffect(() => {
  setCount(count + 1);
}, [count]);
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
const UserContext = createContext();
Provide the Value 
<UserContext.Provider value={someValue}>
  <App/>
</UserContext.Provider>
Consume
const user = useContext(UserContext);

whenever the provider value changes,react automatically re-renders only the components that use the context (consumers)
It is not suitable for large complex apps, the problem is, useContext will re-render every component inside the UserContext.Provider when the value changes, even if another components are not using that value
Note: when to use what

//creating context
import React from "react";
import { useState, createContext } from "react";
import One from "./One";
export const UserContext = createContext();
const lifeCycle = () => {
  const [user, setUser] = useState("Guest");
  const [val, setVal] = useState(0);
  return (
    <div>
      <h1>Hello {user}</h1>
      {/* <UserContext.Provider value={user}> */}{" "}
      {/*passing only single value */}
      <UserContext.Provider value={{ user, setUser, val, setVal }}>
        {/* passing multiple values by wrapping them into {}, notice here we have {{}}*/}
        <One></One>
      </UserContext.Provider>
    </div>
  );
};

export default lifeCycle;


//utilizing context
One.jsx
import React from "react";
import Two from "./Two";

const One = () => {
  return (
    <div>
      <p>I am one</p>
      <Two />
    </div>
  );
};

export default One;

Two.jsx
import React, { useContext } from "react";
import { UserContext } from "./LifeCycle";
const Two = () => {
  // destructuring
  const { user, setUser, val, setVal } = useContext(UserContext);
  return <div>Hello {user}</div>;
};

export default Two;


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


import React, { useReducer } from "react";
const lifeCycle = () => {
  // initial state
  const initialState = { count: 0 };

  // pure reducer function
  // state: the current state
  // action: what to do on that state, passed as a argument by calling function
  function reducer(state, action) {
    switch (action.type) {
      case "add":
        return { count: state.count + 1 };
      case "subtract":
        return { count: state.count - 1 };
      default:
        return state;
    }
  }
  // const [state, setState] = useState(0);
  // reducer is the function that will do the updation like setState
  // dispatch is the function that will tell reducer what to update
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "add" })}>+</button>
      <button onClick={() => dispatch({ type: "subtract" })}>-</button>
    </div>
  );
};

export default lifeCycle;



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

useEffect
used to run side effects like api calls, fetch etc.
dependency array: [] render only once after first mounting, [some dependency]: render only when dependency changes, no dependcy: render on every re-render
syntax:
useEffect(function()=>{
return cleanup function},
dependency array
)
Here on changing of dependency array, the whole component will re-render
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

import React, { useMemo, useState } from "react";

const lifeCycle = () => {
  const [count, setCount] = useState(0);
  const [val, setVal] = useState(0);
  const slowDouble = (num) => {
    console.log("running slowly...");
    for (let i = 0; i < 300000000; i++) {
      // some code
    }
    return num * 2;
  };
  //const doubleVal = slowDouble(count);//wihtout use memo, it will run every time even when count value is not getting updates
  const doubleVal = useMemo(() => slowDouble(count), [count]); //now this function will run only when the value of count variable changes
  return (
    <div>
      <h1>Hello there</h1>
      <h1>{count}</h1>
      <button onClick={() => setCount((prev) => prev + 1)}>+</button>
      <h1>{val}</h1>
      <button onClick={() => setVal((prev) => prev + 1)}>+</button>
      <p>Double val: {doubleVal}</p>
    </div>
  );
};

export default lifeCycle;


some cases
  useEffect(fn, []) → runs once AFTER render
  useMemo(fn, []) → runs once DURING render
  useCallback(fn, []) → creates function once DURING render


  useEffect(fn, []) → runs once AFTER render
  useMemo(fn, []) → runs once DURING render
  useCallback(fn, []) → creates function once DURING render

useCallback
useCallback is used to memoized a callback function
memoizing means caching the result of that function so that it does not need to be re-calculated/ re-calculated

syntax:
const memoFun = useCallback (callback, dependencies)
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

import React, { useCallback, useState } from "react";

const Child = React.memo(({ onClick }) => {
  console.log("child rendered");
  return <button onClick={onClick}>Child Button</button>;
});

const lifeCycle = () => {
  const [count, setCount] = useState(0);
  const [val, setVal] = useState(0);
  // here child will be rendered every time even if child button is not clicked
  // const handleClick = () => {
  //   console.log("child button clicked");
  //   setCount((prev) => prev + 1);
  // };

  //now child button will not get rendered every time
  const handleClick = useCallback(() => {
    console.log("child button clicked");
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <h1>Parent: count {count}</h1>
      <h1>Parent: val: {val}</h1>
      <button onClick={() => setVal((prevVal) => prevVal + 1)}>+ val</button>

      <Child onClick={handleClick} />
    </div>
  );
};

export default lifeCycle;


useRef
useRef hook allows to persist values between renders
it can be used to store a mutable value that done not cause re-render when update
it can be used to update DOM elements direclty
inside ref, there is only one property: ref.current
const myRef = useRef(intialVal);
access the value: myRef.current;
use case: 
 it is perfect for timer
  previous state values
  storing previous props
  caching things
  storing state without re-rendering

import React, { useEffect, useState, useRef } from "react";

const lifeCycle = () => {
  const [count, setCount] = useState(0);
  const renderCount = useRef(1); // will not cause re-render
  const inputRef = useRef(null); //will be used to access DOM elements

  const focusInput = () => {
    inputRef.current.focus(); //direct DOM access
  };

  useEffect(() => {
    renderCount.current += 1;
  });
  return (
    <div>
      <h1>Count: {count}</h1>
      <h2>Component rendered: {renderCount.current}</h2>
      <button onClick={() => setCount((prev) => prev + 1)}>Click me</button>

      <br />

      <input 
      ref={inputRef} //tells the react to attach the actual DOM element into useRef variable
      type="text" 
      placeholder="Name" />
      <button onClick={focusInput}>Focus input</button>
    </div>
  );
};

export default lifeCycle;



passowrd generator
example ussing useCallback,useRef, useEffect

import React, { useState, useCallback, useEffect, useRef } from "react";
const LifeCycle = () => {
  const [length, setLength] = useState(8);
  const [numberAllowed, setNumberAllowed] = useState(false);
  const [charAllowed, setCharAllowed] = useState(false);
  const [password, setPassword] = useState("");
  const passwordRef = useRef(null);

  const fun = () => {
    let pass = "";
    let str = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
    if (numberAllowed) str += "0123456789";
    if (charAllowed) str += "!@#$%^&*~`";
    for (let i = 1; i <= length; i++) {
      let charInd = Math.floor(Math.random() * str.length + 1);
      pass = pass + str.charAt(charInd);
    }
    setPassword(pass);
  };

  const passwordGenerator = useCallback(fun, [
    length,
    numberAllowed,
    charAllowed,
    setPassword,
  ]);

  const copyToclipboard = useCallback(() => {
    passwordRef.current?.select();
    window.navigator.clipboard.writeText(password);
  }, [password]);

  useEffect(() => {
    passwordGenerator();
  }, [length, numberAllowed, charAllowed, passwordGenerator]);

  // note: passwordGenerator will not cause infinite re-render as it is optimized by useCallback function. if useCallback was not there then it can cause infinite rendering
  
  return (
    <div>
      <h1>working</h1>
      <input
        type="text"
        value={password}
        placeholder="Password"
        readOnly
        ref={passwordRef}
      />
      <button onClick={copyToclipboard}>Copy</button>
      <br />
      <input
        type="range"
        min={6}
        max={100}
        value={length}
        onChange={(e) => setLength(e.target.value)}
      />
      <label htmlFor="length">Length: {length}</label>
      <br />

      <input
        type="checkbox"
        defaultChecked={numberAllowed}
        id="numberInput"
        onChange={() => {
          setNumberAllowed((prev) => !prev);
        }}
      />
      <label htmlFor="numberInput">Number</label>

      <input
        type="checkbox"
        defaultChecked={charAllowed}
        id="charAllowed"
        onChange={() => {
          setCharAllowed((prev) => !prev);
        }}
      />
      <label htmlFor="charAllowed">Characters</label>
    </div>
  );
};
export default LifeCycle;

pure components: same input will give same output
Custom hooks - left


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

import React from "react";
import { BrowserRouter, Routes, Route, Link, NavLink } from "react-router-dom";

import NotFound from "./NotFound";
import Home from "./Home";
import About from "./About";
import Contact from "./Contact";
import "./index.css";
const ReactRouter = () => {
  return (
    <>
      <BrowserRouter>
        <Routes>
          {/* path is the url link */}
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>

        {/* use link when using normal links */}
        <Link to="/">Home </Link>
        {/* use navLink when we want to find active and othter states of link */}
        <NavLink
          to="/about"
          //   React router calls function(callback function ()=>{})
          // react router gives {isActive} value
          // className = {jsx}
          // className = {()=>{}} //callback function
          // className = {({object destructuring}) => setting className}
          className={({ isActive }) => (isActive ? "activeClass" : "")}
        >
          About
        </NavLink>
      </BrowserRouter>
    </>
  );
};

export default ReactRouter;


useNavigate
Link and NavLink are click based navigation. 
But when we need to navigate using JS or using some logic then use useNavigate hook
Like navigate when: user logs in, when logs out, when form is submitted, after API response and so on

Note: you cannot even declare useNavigate without <BrowserRouter> so wrap the whole component inside the BrowserRouter when calling it
import ReactRouter from "./interview/ReactRouter";
import { BrowserRouter } from "react-router-dom";
function App() {
  return (
    <>
      <BrowserRouter>
        <ReactRouter />
      </BrowserRouter>
    </>
  );
}

export default App;



else doing this will cause error
import ReactRouter from "./interview/ReactRouter";
function App() {
  return (
    <>
        <ReactRouter />
    </>
  );
}

export default App;

some imports … 
const ReactRouter = () => {
  const navigate = useNavigate(); //this is a function that can change route programitacally   you cannot do this, as even declaring useNavigate without BrowserRouter will give error

  const goToAbout = () => {
    navigate("/about");
  };
  return (
    <>
      <BrowserRouter>
        <Routes> some routes… </Routes>
		Some NavLinks
        <button onClick={goToAbout}>Go to about page</button> //error
      </BrowserRouter>
    </>
  );
};


Super important: go back and forth in history using navigate (-1), (1) or (-2)

import React from "react";
import {
  BrowserRouter,
  Routes,
  Route,
  NavLink,
  useNavigate,
} from "react-router-dom";

import NotFound from "./NotFound";
import Home from "./Home";
import About from "./About";
import Contact from "./Contact";
import "./index.css";
const ReactRouter = () => {
  const navigate = useNavigate(); //this is a function that can change route programitacally

  const goToAbout = () => {
    navigate("/about");
  };
  return (
    <>
      <Routes>
        {/* path is the url link */}
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
      {/* use link when using normal links */}
      <NavLink
        to="/"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Home
      </NavLink>
      <NavLink
        to="/about"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        About
      </NavLink>
      <NavLink
        to="/contact"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Contact
      </NavLink>
      <button onClick={goToAbout}>Go to about page</button>
      <button onClick={() => navigate(-1)}>Go back</button>{" "}
      {/* go back in history*/}
      <button onClick={() => navigate(-2)}>Go back</button>{" "}
      {/* go back two steps in history*/}
      <button onClick={() => navigate(1)}>Go forward</button>
      {/* go forward in history*/}
    </>
  );
};

export default ReactRouter;



URL parameters & dynamic routes
Why params are always strings:  Because URL segments are text by nature.
import React from "react";
import {
  BrowserRouter,
  Routes,
  Route,
  NavLink,
} from "react-router-dom";

import NotFound from "./NotFound";
import Home from "./Home";
import About from "./About";
import Contact from "./Contact";
import "./index.css";
const ReactRouter = () => {
  return (
    <>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact/:id" element={<Contact />} /> {/*using dynamic routing */}
      </Routes>      
      <NavLink
        to="/contact"
        className={({ isActive }) => (isActive ? "activeClass" : "")}
      >
        Contact
      </NavLink>
    </>
  );
};

export default ReactRouter;


Contact.jsx
import React from "react";
import { useParams } from "react-router-dom";
const Contact = () => {
  const { id } = useParams();//using dynamic routing, object destructuring
  return (
    <div>
      <p>Contact us at: {id}</p> {/*using dynamic routing */}
    </div>
  );
};

export default Contact;


note: /contact and /contact/123 are different. Now visiting /contact will not show anything as its route is nto there
also the useParams are always of string data type, if you want number then use this: Number(id)
how route ranking/scoring wroks
here we have 3 paths for contact, which one to render, so for that we have priority
 <Routes>
        {/* path is the url link */}
        <Route path="/contact" element={<OnlyContact />} />
        <Route path="/contact/:id" element={<IdContact />} />
        <Route path="/contact/new" element={<NewContact />} />
        <Route path="/contact/*" element={<NotFound />} />{" "}
        {/*using dynamic routing */}
      </Routes>






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
import { Route