
## 🔹 useEffect kya karta hai?

`useEffect` tab use hota hai jab hume:

* API call karni ho
* Page load par kaam karna ho
* State change hone par kuch karna ho

---

## 🔹 Syntax

```jsx
useEffect(() => {
  // code yahan likhte hain
}, []);
```

---

## 1️⃣ Page Load par run hona (Most Common)

```jsx
import { useEffect } from "react";

function App() {
  useEffect(() => {
    console.log("Page loaded");
  }, []);

  return <h1>Hello</h1>;
}
```

✅ `[]` → sirf **1 baar** chalega (component mount)

---

## 2️⃣ State change hone par run hona

```jsx
import { useState, useEffect } from "react";

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Count changed:", count);
  }, [count]);

  return (
    <>
      <h2>{count}</h2>
      <button onClick={() => setCount(count + 1)}>+</button>
    </>
  );
}
```

✅ Jab bhi `count` change hoga → useEffect chalega

---

## 3️⃣ API Call using useEffect (Simple)

```jsx
import { useEffect, useState } from "react";

function App() {
  const [data, setData] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(result => setData(result));
  }, []);

  return (
    <ul>
      {data.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## 4️⃣ useEffect without dependency array ❌

```jsx
useEffect(() => {
  console.log("Har render par chalega");
});
```

❌ Har render pe chalega (performance issue)

---

## 5️⃣ Cleanup Function (Timer Example)

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running...");
  }, 1000);

  return () => clearInterval(timer); // cleanup
}, []);
```


