### useMemo Hook (React)

`useMemo` is used to **remember a computed value** so React does **not recalculate it on every render**.

---

## Problem (without useMemo)

Heavy calculations run again even when they are not needed.

---

## Example: Filter Active Users

---

## Code

```javascript
import { useState, useMemo } from "react";

function UserList() {
  const [users] = useState([
    { id: 1, name: "Amit", active: true },
    { id: 2, name: "Ravi", active: false },
    { id: 3, name: "Neha", active: true }
  ]);

  const [showActive, setShowActive] = useState(true);

  const activeUsers = useMemo(() => {
    console.log("Filtering users...");
    return users.filter(user => user.active);
  }, [users]);

  return (
    <div>
      <h2>Users</h2>

      <button onClick={() => setShowActive(!showActive)}>
        Toggle Active
      </button>

      <ul>
        {(showActive ? activeUsers : users).map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default UserList;
```

## What is happening (easy)

* Filtering users is a calculation
* `useMemo` stores the result
* Re-calculation happens only when `users` change
* Toggling button does not re-run filter logic unnecessarily

---

## Without useMemo (problem)

```javascript
const activeUsers = users.filter(user => user.active);
```

Runs on every render.

---

## Mental Rule

```
useMemo → remembers value
useCallback → remembers function
```

---

## When to use useMemo

* Heavy calculations
* Derived data (filter, sort, map)
* Prevent performance issues

---

## One-line 

`useMemo memoizes a computed value and recalculates it only when its dependencies change.`


