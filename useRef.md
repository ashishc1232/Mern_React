### useRef Hook 

The `useRef` hook is used to **access DOM elements directly** or to **store values that do not cause re-render**.

---

## Example 1: Focus Input on Button Click (Most Common Use)

---

## Code

```javascript
import { useRef } from "react";

function FocusInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}

export default FocusInput;
```

---

## What is happening (easy)

* `useRef(null)` creates a reference
* `ref={inputRef}` connects input to ref
* `inputRef.current` points to the DOM element
* `.focus()` focuses input
* No re-render happens

---

## Example 2: Store Previous Value (No Re-render)

---

## Code

```javascript
import { useEffect, useRef, useState } from "react";

function PreviousValue() {
  const prevCountRef = useRef(null);
  const [count, setCount] = useState(0);

  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  return (
    <div>
      <p>Current Count: {count}</p>
      <p>Previous Count: {prevCountRef.current}</p>

      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
}

export default PreviousValue;
```

---

## Key Difference: useRef vs useState

| useRef               | useState          |
| -------------------- | ----------------- |
| Does not re-render   | Causes re-render  |
| Stores mutable value | Stores UI state   |
| Access DOM directly  | Cannot access DOM |

---

## When to use useRef

* Focus input
* Scroll to element
* Store previous value
* Store timer or interval ID

---

## One-line 

`useRef is used to persist values across renders without causing re-render and to directly access DOM elements.`


* useRef with form validation
