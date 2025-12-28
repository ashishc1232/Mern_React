## useReducer – Easy & Clean Example

### Example: Light ON / OFF System


## 1. Initial State

```javascript
const initialState = {
  isOn: false
};
```

---

## 2. Reducer Function

Reducer decides **how state changes**.

```javascript
function lightReducer(state, action) {
  if (action.type === "TOGGLE") {
    return { isOn: !state.isOn };
  }
  return state;
}
```

---

## 3. Component using useReducer

```javascript
import { useReducer } from "react";

const initialState = { isOn: false };

function lightReducer(state, action) {
  if (action.type === "TOGGLE") {
    return { isOn: !state.isOn };
  }
  return state;
}

function LightSwitch() {
  const [state, dispatch] = useReducer(lightReducer, initialState);

  return (
    <div>
      <h2>Light Status: {state.isOn ? "ON" : "OFF"}</h2>

      <button onClick={() => dispatch({ type: "TOGGLE" })}>
        Toggle Light
      </button>
    </div>
  );
}

export default LightSwitch;
```

---

## 4. What is happening (simple words)

* `useReducer` replaces multiple `useState`
* `state` → current value
* `dispatch` → sends instruction
* `action.type` → what to do
* reducer returns **new state**

---

## 5. Mental Shortcut (remember this)

```
Button click → dispatch(action)
dispatch → reducer
reducer → new state
state → UI update
```

---

## When to use useReducer (easy rule)

* If logic feels confusing with `useState`
* If state changes depend on previous value
* If you want clean control


