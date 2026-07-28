## What is `React.memo`?

`React.memo` is a higher-order component (HOC) provided by React to optimize performance. It is used to wrap functional components to prevent them from re-rendering unless their props have changed.
In a standard React application, when a parent component re-renders, all of its child components will automatically re-render as well, regardless of whether the data passed to them (props) has actually changed. While React is fast, these unnecessary re-renders can slow down your application if the child component is complex or heavy. `React.memo` solves this problem.
## How It Works: Shallow Comparison
When you wrap a component in `React.memo`, React remembers (memoizes) the rendered output. On the next render, React compares the **new props** with the **old props**.
It does this using a **shallow comparison** (specifically, `Object.is`).
- If the props are exactly the same (primitives like strings, numbers, or booleans match perfectly), React skips the re-render and reuses the last rendered result.
- If the props have changed, React re-renders the component.
### Basic Usage Example
Here is how you wrap a functional component with `React.memo`:
```javascript
import React, { useState } from 'react';

// The child component is wrapped in React.memo
const ChildComponent = React.memo(({ name }) => {
  console.log("ChildComponent rendered!");
  return <div>Hello, {name}!</div>;
});

export default function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Alice");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count: {count}</button>
      {/* 
        Clicking the button updates 'count', causing ParentComponent to re-render.
        However, ChildComponent will NOT re-render because its 'name' prop hasn't changed.
      */}
      <ChildComponent name={name} />
    </div>
  );
}
```
## The Catch: Reference Equality (Objects, Arrays, and Functions)
Because `React.memo` only performs a _shallow_ comparison, it struggles with reference types (objects, arrays, and functions).
In JavaScript, two objects or arrays are only equal if they point to the exact same memory location. Every time a parent component re-renders, any objects, arrays, or functions defined inside it are recreated in memory. `React.memo` will see these as "new" props and force a re-render anyway.

**Problem Example:**
```JavaScript
// Even with React.memo, this will re-render if the parent re-renders!
<ChildComponent user={{ name: "Alice", age: 25 }} /> 
```
**The Solution:**
To fix this, you must pair `React.memo` with two other React hooks in the parent component:
1. **`useMemo`**: To keep the same reference for arrays and objects across renders.
2. **`useCallback`**: To keep the same reference for functions across renders.
## Custom Comparison Function
If a shallow comparison isn't enough (for example, if you are passing a deeply nested object and don't want to use `useMemo`), you can provide a custom comparison function as the second argument to `React.memo`.
This function receives the old props and the new props. It should return `true` if the props are equal (skip render) and `false` if they are different (trigger render).
```JavaScript
const MyComponent = ({ user }) => {
  return <div>{user.profile.name}</div>;
};

// Custom comparison function
const arePropsEqual = (prevProps, nextProps) => {
  // Only re-render if the deeply nested name changes
  return prevProps.user.profile.name === nextProps.user.profile.name;
};

export default React.memo(MyComponent, arePropsEqual);
```
> **Note:** Be careful with custom comparison functions. If your comparison logic is more expensive than the re-render itself, you will end up harming performance instead of improving it.
## When to Use `React.memo`
You should not wrap every component in `React.memo`. The memoization process itself costs memory and computing power. Use it strategically:
- **Pure Components:** When your component always renders the exact same output given the same props.
- **Heavy Components:** When the component contains complex UI logic, heavy math, or renders a massive list (e.g., a data grid or chart).
- **Frequent Parent Re-renders:** When the parent component re-renders constantly (like tracking a mouse position or a ticking clock), but the child component's props remain static.
## When NOT to Use `React.memo`
- **Props change frequently:** If the props passed to the component change on almost every single render, `React.memo` is just wasting time doing a useless comparison before re-rendering anyway.
- **Simple Components:** If a component is just a simple `div` or button, the cost of checking props might be higher than simply letting React re-render it.
- **Components using `children`:** If you pass standard children (`<Component><Child/></Component>`), the `children` prop creates a new reference every render, breaking the memoization.