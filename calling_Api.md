## 1. Using `fetch` (Built-in JavaScript API)

###  Best for: Simple projects, no extra libraries

```jsx
import { useEffect, useState } from "react";

function App() {
  const [data, setData] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/posts")
      .then(res => res.json())
      .then(data => setData(data))
      .catch(err => console.error(err));
  }, []);

  return (
    <ul>
      {data.map(item => (
        <li key={item.id}>{item.title}</li>
      ))}
    </ul>
  );
}

export default App;
```

### Pros

* No external library
* Simple

### Cons

* Manual error handling
* No request cancellation

## 2. Using `fetch` with `async / await`

###  Best for: Cleaner, more readable code

```jsx
useEffect(() => {
  const getData = async () => {
    try {
      const response = await fetch("https://jsonplaceholder.typicode.com/posts");
      const data = await response.json();
      setData(data);
    } catch (error) {
      console.error(error);
    }
  };

  getData();
}, []);
```

---

## 3. Using **Axios** (Popular Library)

###  Best for: Real-world apps, better error handling

```bash
npm install axios
```

```jsx
import axios from "axios";
import { useEffect, useState } from "react";

function App() {
  const [data, setData] = useState([]);

  useEffect(() => {
    axios.get("https://jsonplaceholder.typicode.com/posts")
      .then(res => setData(res.data))
      .catch(err => console.error(err));
  }, []);

  return (
    <ul>
      {data.map(item => (
        <li key={item.id}>{item.title}</li>
      ))}
    </ul>
  );
}
```

### Pros

* Automatic JSON parsing
* Interceptors
* Better error handling

---

## 4. Axios with `async / await`

```jsx
useEffect(() => {
  const fetchData = async () => {
    try {
      const res = await axios.get("https://jsonplaceholder.typicode.com/posts");
      setData(res.data);
    } catch (err) {
      console.error(err);
    }
  };

  fetchData();
}, []);
```

---

## 5. Using **Custom API Service File** (Best Practice)

Samajhne ke liye **step-by-step** batata hoon ki **Custom API Service File (`api.js`) ko component me kaise call karte hain** 👇
(React beginner-friendly explanation, Hinglish)

---

##  `api.js` (API Service File)

```js
// src/api/api.js
import axios from "axios";

const API = axios.create({
  baseURL: "https://jsonplaceholder.typicode.com",
});

// GET request
export const getPosts = () => API.get("/posts");
```

✔️ Yahan humne axios ka instance banaya
✔️ `getPosts()` ek function hai jo API call karega

---

## Component me API call kaise karein

###  Step 1: Import karo

```js
import { useEffect, useState } from "react";
import { getPosts } from "./api/api"; // path apne project ke hisaab se
```

---

###  Step 2: State banao

```js
const [data, setData] = useState([]);
```

---

###  Step 3: `useEffect` me API call karo

```jsx
useEffect(() => {
  getPosts()
    .then((response) => {
      setData(response.data); // axios me data response.data me hota hai
    })
    .catch((error) => {
      console.error("API Error:", error);
    });
}, []);
```

---

##  Complete Component Example

```jsx
import { useEffect, useState } from "react";
import { getPosts } from "./api/api";

function Posts() {
  const [data, setData] = useState([]);

  useEffect(() => {
    getPosts()
      .then((res) => {
        setData(res.data);
      })
      .catch((err) => {
        console.error(err);
      });
  }, []);

  return (
    <div>
      <h2>Posts</h2>
      {data.map((post) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  );
}

export default Posts;
```

---

##  Async / Await Version (Recommended )

```jsx
useEffect(() => {
  const fetchPosts = async () => {
    try {
      const res = await getPosts();
      setData(res.data);
    } catch (error) {
      console.error(error);
    }
  };

  fetchPosts();
}, []);
```

---

##  Important Points (Yaad Rakhna)

 `getPosts()` **Promise return karta hai**
Axios me data hamesha `response.data` me hota hai
`useEffect` ka empty array `[]` → API sirf **once** call hoti hai
API logic alag file me → **clean & reusable code**

---


## 6. Using **React Query (TanStack Query)**

###  Best for: Large apps, caching, auto-refetching

```bash
npm install @tanstack/react-query
```

```jsx
import { useQuery } from "@tanstack/react-query";
import axios from "axios";

const fetchPosts = async () => {
  const res = await axios.get("https://jsonplaceholder.typicode.com/posts");
  return res.data;
};

function App() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error!</p>;

  return (
    <ul>
      {data.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### Pros

* Caching
* Background updates
* No `useEffect`

---

## 7. Using **Redux Toolkit (RTK Query)**

###  Best for: Apps already using Redux

```js
// apiSlice.js
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

export const api = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({ baseUrl: "https://jsonplaceholder.typicode.com" }),
  endpoints: builder => ({
    getPosts: builder.query({
      query: () => "/posts",
    }),
  }),
});

export const { useGetPostsQuery } = api;
```

```jsx
function App() {
  const { data, isLoading } = useGetPostsQuery();

  return isLoading ? <p>Loading...</p> : data.map(p => <p key={p.id}>{p.title}</p>);
}
```

---

## 8. Using **SWR (Stale-While-Revalidate)**

```bash
npm install swr
```

```jsx
import useSWR from "swr";
import axios from "axios";

const fetcher = url => axios.get(url).then(res => res.data);

function App() {
  const { data, error } = useSWR(
    "https://jsonplaceholder.typicode.com/posts",
    fetcher
  );

  if (error) return <p>Error</p>;
  if (!data) return <p>Loading...</p>;

  return data.map(post => <p key={post.id}>{post.title}</p>);
}
```

]

| Method             | Best For                    |
| ------------------ | --------------------------- |
| fetch              | Small apps                  |
| Axios              | Most projects               |
| Custom API Service | Clean architecture          |
| React Query        | Server state management     |
| RTK Query          | Redux apps                  |
| SWR                | Auto caching & revalidation |


Just tell me 👍
