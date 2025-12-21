

## First: Core Difference

| Props                              | State                           |
| ---------------------------------- | ------------------------------- |
| Passed from parent to child        | Managed inside a component      |
| Read-only (child cannot change it) | Can be changed using `setState` |
| Used to send data                  | Used to manage data             |
| Immutable inside child             | Mutable using updater function  |

Short rule to remember:

* Props = outside data
* State = inside data

---

## Example 1: Props Example (Parent → Child)

Parent component sends data to child using props.

### Parent Component

```jsx
function Parent() {
  return (
    <div>
      <User name="Ashish" age={24} />
    </div>
  );
}

export default Parent;
```

### Child Component

```jsx
function User(props) {
  return (
    <div>
      <p>Name: {props.name}</p>
      <p>Age: {props.age}</p>
    </div>
  );
}

export default User;
```

Key points:

* `name` and `age` come from parent
* Child cannot modify `props.name`
* Props flow only one way (top to bottom)

---

## Example 2: State Example (Inside Component)

State is created and controlled by the component itself.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

Key points:

* `count` is local data
* Can be updated using `setCount`
* UI updates automatically

---

## Example 3: Props Cannot Be Changed by Child

This is wrong and will not work:

```jsx
function Child(props) {
  props.name = "Rahul"; // ❌ not allowed
  return <p>{props.name}</p>;
}
```

Why:

* Props are read-only
* Child should never change parent data directly

---

## Example 4: State Passed as Props

Very common real-world pattern.

### Parent

```jsx
import { useState } from "react";
import Child from "./Child";

function Parent() {
  const [username, setUsername] = useState("Ashish");

  return (
    <Child name={username} />
  );
}

export default Parent;
```

### Child

```jsx
function Child({ name }) {
  return <h2>{name}</h2>;
}

export default Child;
```

Key idea:

* State lives in parent
* Passed as props to child

---

## Example 5: Child Updating Parent State (Correct Way)

Child does not change state directly.
It calls a function passed as props.

### Parent

```jsx
import { useState } from "react";
import Child from "./Child";

function Parent() {
  const [count, setCount] = useState(0);

  return (
    <Child count={count} increment={setCount} />
  );
}

export default Parent;
```

### Child

```jsx
function Child({ count, increment }) {
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => increment(count + 1)}>
        Increase
      </button>
    </div>
  );
}

export default Child;
```

Key learning:

* Parent owns the state
* Child triggers update via function

---

## Example 6: Props vs State in One Component

```jsx
import { useState } from "react";

function Profile({ name }) {
  const [age, setAge] = useState(20);

  return (
    <div>
      <p>Name (prop): {name}</p>
      <p>Age (state): {age}</p>

      <button onClick={() => setAge(age + 1)}>
        Increase Age
      </button>
    </div>
  );
}

export default Profile;
```

Explanation:

* `name` comes from outside
* `age` is controlled internally

---

## When to Use Props vs State

Use **props** when:

* Data comes from parent
* Component is reusable
* You only need to display data

Use **state** when:

* Data changes over time
* User interaction is involved
* Component needs to manage its own data

---


