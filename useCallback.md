### useCallback Hook (React)

`useCallback` is used to **remember a function** so that it is **not re-created on every render**.

---

## Problem (without useCallback)

Every time a parent re-renders, **functions are recreated**, and child components may re-render unnecessarily.

---

## Example: Button Counter with Memo Child

---

## 1. Child Component

```javascript
import React from "react";

const CountButton = React.memo(({ onClick, label }) => {
  console.log(label, "rendered");
  return <button onClick={onClick}>{label}</button>;
});

export default CountButton;
```

`React.memo` prevents re-render unless props change.

---

## 2. Parent Component using useCallback

```javascript
import { useState, useCallback } from "react";
import CountButton from "./CountButton";

function CounterApp() {
  const [count, setCount] = useState(0);
  const [theme, setTheme] = useState("light");

  const increment = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <h2>Count: {count}</h2>

      <CountButton label="Increase" onClick={increment} />

      <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Change Theme
      </button>
    </div>
  );
}

export default CounterApp;
```

---

## What is happening (easy)

* `useCallback` memorizes `increment` function
* Function is not recreated when `theme` changes
* `CountButton` does not re-render unnecessarily
* Performance improves

---

## Without useCallback (issue)

```javascript
const increment = () => {
  setCount(count + 1);
};
```

Function is recreated on every render, causing child re-render.

---

## Mental Rule (very important)

```
useCallback remembers function
useMemo remembers value
```

---

## When to use useCallback

* Passing function to memoized child
* Heavy child components
* Avoid unnecessary renders

---

## One-line 

`useCallback memoizes a function to prevent unnecessary re-creation and re-rendering of child components.`


