

## 🎯 Session Overview

| Topic | Time (approx) |
|-------|--------------|
| Kya hai Axios & kyun use karna hai | 5 min |
| Async/Await vs Callbacks | 5 min |
| Setup & JSONPlaceholder API | 5 min |
| GET — Data fetch karo | 10 min |
| POST — Naya post banao | 10 min |
| PATCH — Update karo | 10 min |
| DELETE — Hatao | 10 min |
| Error Handling (try-catch) | 5 min |
| 🆕 Phase 2: json-server pe migrate karo | 10 min |

> **Session mein 2 APIs use hongi:**
> - **Phase 1** → `https://jsonplaceholder.typicode.com/posts` (fake, online)
> - **Phase 2** → `http://localhost:3001/posts` (real save, offline — json-server)

---

## 📦 PART 0 — Project Setup

**Bolna hai:**
> "Bhai, pehle ek seedha React app banate hain. No fancy stuff, no TypeScript drama, plain React. Aur Axios install karte hain."

```bash
npx create-react-app axios-crud-demo
cd axios-crud-demo
npm install axios
npm start
```

**Ek line concept:**
> "Axios install ho gaya. Ab `fetch` ko bhool jao — Axios tumhara naya best friend hai."

---

## 🤔 PART 1 — Kya Hai Axios & Kyun Use Karna Hai?

**Bolna hai:**
> "Bhai, pehle ye bata — ye Axios hai kya cheez? Kya `fetch` kafi nahi tha?"

### Fetch vs Axios — Side by Side

```js
// FETCH wala tarika (purana dard)
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(res => res.json())          // pehle manually parse karo
  .then(data => console.log(data))
  .catch(err => console.log(err));
```

```js
// AXIOS wala tarika (naya sukoon)
axios.get('https://jsonplaceholder.typicode.com/posts/1')
  .then(res => console.log(res.data)); // automatically parse!
```

### Axios ke Benefits (bolna hai students ko):

| Feature | Fetch | Axios |
|---------|-------|-------|
| Auto JSON parse | ❌ Manual | ✅ Automatic |
| Error handling | ❌ 404 bhi success manta hai | ✅ HTTP errors pakad leta hai |
| Request cancel | ❌ Complex | ✅ Built-in |
| Base URL config | ❌ Nahi | ✅ Haan |
| Interceptors | ❌ Nahi | ✅ Haan |
| Timeout | ❌ Manual | ✅ Easy |

**Funny line bolna:**
> "Fetch aur Axios ka farak wahi hai jo cycle aur bike mein hota hai. Cycle se bhi pohonch jaoge, lekin bike pe style hai, speed hai, aur garmi bhi kam lagti hai. 😄"

---

## ⚡ PART 2 — Async/Await Kyun?

**Bolna hai:**
> "Ab baat karte hain async/await ki. Ye `.then()` chain se better kyun hai?"

### `.then()` wala tarika:
```js
axios.get('/posts')
  .then(res => {
    return axios.get(`/users/${res.data[0].userId}`)
  })
  .then(userRes => {
    return axios.get(`/todos?userId=${userRes.data.id}`)
  })
  .then(todos => console.log(todos))
  .catch(err => console.log(err));
  // 😵 Ye hai "Callback Hell ka cousin — Promise Chain Hell"
```

### Async/Await wala tarika:
```js
const fetchData = async () => {
  try {
    const postRes  = await axios.get('/posts');
    const userRes  = await axios.get(`/users/${postRes.data[0].userId}`);
    const todos    = await axios.get(`/todos?userId=${userRes.data.id}`);
    console.log(todos.data);
  } catch (err) {
    console.log(err);
  }
  // ✅ Top se bottom padho, seedha samajh aata hai!
};
```

**Bolna hai:**
> "Async/Await basically tumhari code ko aise likhne deta hai jaise normal story ho — upar se neeche. `.then().then().then()` wala horror movie mat likho."

### Try-Catch Kyun?
```js
// Bina try-catch ke — zindagi ka sabse bada regret
const fetchPosts = async () => {
  const res = await axios.get('WRONG_URL'); // 💥 App crash!
  console.log(res.data);
};

// Try-catch ke saath — professional banda
const fetchPosts = async () => {
  try {
    const res = await axios.get('WRONG_URL');
    console.log(res.data);
  } catch (error) {
    console.log('Kuch toh gadbad hai daya:', error.message); // 😄
  }
};
```

---

## 💻 PART 3 — Full Working App (Phase 1: JSONPlaceholder)

> "Ab asli kaam karte hain. Ek posts manager banate hain jahan GET, POST, PATCH, DELETE — sab karenge."

**Bolna hai:**
> "Pehle JSONPlaceholder se karte hain — ye ek free fake API hai. Samjho ye ek dummy waiter hai jo hamesha haan karta hai, order note karta hai, lekin kitchen mein jaata hi nahi. Response milta hai, data save nahi hota. Perfect for concepts!"

**`src/App.js` — Phase 1 Code:**

```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';

// ⚠️  Phase 1: JSONPlaceholder — fake API, data save nahi hota
// 🔁  Phase 2 mein sirf YEH EK LINE badlegi — baaki sab same rahega
const BASE_URL = 'https://jsonplaceholder.typicode.com/posts';

export default function App() {
  const [posts, setPosts]       = useState([]);
  const [loading, setLoading]   = useState(false);
  const [error, setError]       = useState('');
  const [newTitle, setNewTitle] = useState('');
  const [editPost, setEditPost] = useState(null); // { id, title }

  // ─────────────────────────────────────────
  // GET — Sare posts fetch karo
  // ─────────────────────────────────────────
  const fetchPosts = async () => {
    setLoading(true);
    setError('');
    try {
      const res = await axios.get(`${BASE_URL}?_limit=5`);
      setPosts(res.data);
    } catch (err) {
      setError('Posts fetch nahi hue 😢 ' + err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchPosts();
  }, []);

  // ─────────────────────────────────────────
  // POST — Naya post banao
  // ─────────────────────────────────────────
  const createPost = async () => {
    if (!newTitle.trim()) return alert('Title likhna toh padega bhai!');
    try {
      const res = await axios.post(BASE_URL, {
        title:  newTitle,
        body:   'Yeh naya post hai, fresh out of the oven 🍞',
        userId: 1,
      });
      // JSONPlaceholder naya id deta hai (101), hum usse list mein add karte hain
      setPosts([res.data, ...posts]);
      setNewTitle('');
      alert(`Post created! ID: ${res.data.id} 🎉`);
    } catch (err) {
      setError('Post create nahi hua 😢 ' + err.message);
    }
  };

  // ─────────────────────────────────────────
  // PATCH — Sirf title update karo
  // ─────────────────────────────────────────
  const updatePost = async () => {
    if (!editPost?.title.trim()) return alert('Updated title toh likho!');
    try {
      const res = await axios.patch(`${BASE_URL}/${editPost.id}`, {
        title: editPost.title,
      });
      setPosts(posts.map(p =>
        p.id === editPost.id ? { ...p, title: res.data.title } : p
      ));
      setEditPost(null);
      alert('Post update ho gaya! ✅');
    } catch (err) {
      setError('Update nahi hua 😢 ' + err.message);
    }
  };

  // ─────────────────────────────────────────
  // DELETE — Post hatao
  // ─────────────────────────────────────────
  const deletePost = async (id) => {
    if (!window.confirm('Pakka delete karna hai? Wapas nahi aayega!')) return;
    try {
      await axios.delete(`${BASE_URL}/${id}`);
      setPosts(posts.filter(p => p.id !== id));
      alert('Post delete ho gaya! 🗑️');
    } catch (err) {
      setError('Delete nahi hua 😢 ' + err.message);
    }
  };

  // ─────────────────────────────────────────
  // UI
  // ─────────────────────────────────────────
  return (
    <div style={{ maxWidth: '700px', margin: '20px auto', fontFamily: 'sans-serif', padding: '0 16px' }}>
      <h1>📝 Axios CRUD Demo</h1>

      {error && <p style={{ color: 'red' }}>{error}</p>}

      {/* CREATE */}
      <section>
        <h2>➕ Create Post</h2>
        <input
          value={newTitle}
          onChange={e => setNewTitle(e.target.value)}
          placeholder="Post ka title likho..."
          style={{ width: '70%', padding: '8px', marginRight: '8px' }}
        />
        <button onClick={createPost}>Create</button>
      </section>

      <hr />

      {/* UPDATE FORM — tab dikhega jab edit click ho */}
      {editPost && (
        <section>
          <h2>✏️ Update Post #{editPost.id}</h2>
          <input
            value={editPost.title}
            onChange={e => setEditPost({ ...editPost, title: e.target.value })}
            style={{ width: '70%', padding: '8px', marginRight: '8px' }}
          />
          <button onClick={updatePost}>Save</button>
          <button onClick={() => setEditPost(null)} style={{ marginLeft: '8px' }}>Cancel</button>
        </section>
      )}

      <hr />

      {/* READ */}
      <section>
        <h2>📋 Posts List</h2>
        {loading && <p>Loading... ⏳</p>}
        {posts.map(post => (
          <div
            key={post.id}
            style={{ border: '1px solid #ccc', padding: '12px', marginBottom: '10px', borderRadius: '6px' }}
          >
            <strong>#{post.id}</strong> — {post.title}
            <div style={{ marginTop: '8px' }}>
              <button onClick={() => setEditPost({ id: post.id, title: post.title })}>
                ✏️ Edit
              </button>
              <button
                onClick={() => deletePost(post.id)}
                style={{ marginLeft: '8px', color: 'red' }}
              >
                🗑️ Delete
              </button>
            </div>
          </div>
        ))}
      </section>
    </div>
  );
}
```

---

## 🎙️ PART 4 — Code Walk-through Script (bolna hai)

### GET explain karte waqt:

> "Dekho bhai, `useEffect` mein `fetchPosts()` call kar rahe hain. Matlab jab component mount ho — yaani screen pe aaye — tab immediately posts fetch ho jayein. Aur `?_limit=5` ka matlab hai sirf 5 posts do, warna 100 aa jaate hain aur hum scroll karte reh jaate!"

```js
const res = await axios.get(`${BASE_URL}?_limit=5`);
setPosts(res.data); // res.data — ye Axios ka magic hai, JSON khud parse karta hai
```

> "Notice karo — `res.data` directly use kar rahe hain. Fetch mein `res.json()` karna padta, Axios ne woh kaam already kar diya!"

---

### POST explain karte waqt:

> "POST karo, response mein ID `101` aayegi — JSONPlaceholder simulate kar raha hai. Page refresh karo — woh post gayab! Kyunki data save hua hi nahi tha. Abhi ke liye ye theek hai concepts ke liye, baad mein real save karenge — aur spoiler: React ka ek bhi line nahi badlega."

```js
const res = await axios.post(BASE_URL, {
  title:  newTitle,
  body:   '...',
  userId: 1,
});
setPosts([res.data, ...posts]); // nayi post ko list ke upar daalo
```

---

### PATCH vs PUT — important concept:

> "Ek cheez clear kar deta hoon — PATCH aur PUT mein farak hota hai."

| Method | Matlab |
|--------|--------|
| **PUT** | Poora object replace karo |
| **PATCH** | Sirf kuch fields update karo |

```js
// PUT — sab bhejo warna baaki fields delete ho sakti hain
axios.put(`/posts/1`, { title: 'New', body: 'New body', userId: 1 });

// PATCH — sirf jo update karna hai woh bhejo
axios.patch(`/posts/1`, { title: 'New' }); // ✅ Efficient!
```

> "Real life mein agar tumhara naam change karna ho toh bank ko poora form nahi bharte na? Sirf naam wala column update karte ho. Wahi PATCH ka kaam hai."

---

### DELETE explain karte waqt:

> "Delete mein koi body nahi bhejni — sirf URL mein ID daalo aur kaho 'bhai, ye wala hatao.'"

```js
await axios.delete(`${BASE_URL}/${id}`);
setPosts(posts.filter(p => p.id !== id)); // UI se bhi hatao
```

> "Important: `await` ke baad koi `.data` nahi hai DELETE mein — server sirf `200 OK` bhejta hai matlab 'ho gaya bhai'."

---

### Finally block — bonus concept:

```js
try {
  setLoading(true);
  const res = await axios.get(BASE_URL);
  setPosts(res.data);
} catch (err) {
  setError(err.message);
} finally {
  setLoading(false); // error ho ya na ho — loading ZAROOR hatao
}
```

> "`finally` woh banda hai jo har situation mein kaam karta hai — success ho ya failure, ye run hoga. Loading spinner hatana hai na? `finally` mein daalo, tension nahi."

---

## 🔄 PART 5 — Phase 2: json-server pe Migrate karo

**Bolna hai:**
> "Okay bhai, ab woh moment aaya jiska maine promise kiya tha. JSONPlaceholder se real local server pe jaate hain — aur dekho kitni kam changes karni padti hain."

---

### Step 1 — json-server install karo

```bash
npm install -g json-server
```

> "Globally install kar rahe hain taaki kisi bhi project mein use kar sako."

---

### Step 2 — db.json banao (React project ke root mein)

**Bolna hai screen share karke:**
> "Ye file `src` ke andar nahi jaayegi — project ke root mein banao, jahan `package.json` hai."

```
axios-crud-demo/        ← yahan banao
├── src/
├── package.json
├── db.json             ← YE NAYA FILE
└── node_modules/
```

**`db.json` content:**

```json
{
  "posts": [
    { "id": 1, "title": "React Seekhna Shuru Kiya", "body": "Aaj pehla din hai" },
    { "id": 2, "title": "Axios Kya Hota Hai", "body": "HTTP requests easy ho gayi" },
    { "id": 3, "title": "CRUD Samajh Aaya", "body": "Create Read Update Delete" }
  ]
}
```

> "3 dummy posts daale hain — GET pe turant kuch dikhega. Blank screen se shuru karna boring hota hai, students sochte hain kuch toot gaya. 😄"

---

### Step 3 — json-server start karo (naya terminal)

```bash
json-server --watch db.json --port 3001
```

**Bolna hai:**
> "Ab do terminals chalenge simultaneously. React port 3000 pe, json-server port 3001 pe. Dono alag, koi fight nahi."

```
Terminal 1 → npm start                           → localhost:3000  (React)
Terminal 2 → json-server --watch db.json --port 3001  → localhost:3001  (API)
```

---

### Step 4 — App.js mein SIRF YEK LINE badlo ✂️

**Screen pe highlight karke dikhao:**

```jsx
// ❌ Pehle yeh tha
const BASE_URL = 'https://jsonplaceholder.typicode.com/posts';

// ✅ Bas yeh karo — ek line, poora baaki code same
const BASE_URL = 'http://localhost:3001/posts';
```

**Bolna hai:**
> "Bas. Ek. Line. Poori React file wahi hai. Axios wahi hai. Functions wahi hain. Ye tha BASE_URL variable banane ka fayda."

**Ab live dikhaao:**
> "POST karo — phir `db.json` file kholo. Entry wahan dikh rahi hai! DELETE karo — file se bhi hata gaya! Refresh karo — data wapas aaya! Ye hai real CRUD bhai."

---

### Phase 1 vs Phase 2 — Side by Side

| | JSONPlaceholder | json-server |
|--|----------------|-------------|
| Data save hota hai? | ❌ Simulate karta hai | ✅ db.json mein actually save |
| Internet chahiye? | ✅ Haan | ❌ Bilkul nahi |
| Refresh pe data? | ❌ Chala jaata hai | ✅ Wapas aata hai |
| React code change? | — | Sirf 1 line (BASE_URL) |
| Setup? | Zero | 2 min |

---

## 🔥 PART 6 — Bonus: Axios Instance (Pro tip)

**Bolna hai:**
> "Aur agar Axios Instance use karo toh migration aur bhi cleaner hoti hai — React code mein BASE_URL bhi nahi hogi."

**`src/api/axiosInstance.js`:**
```js
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3001',  // ← production mein sirf yahan change karo
  timeout: 5000,
  headers: {
    'Content-Type': 'application/json',
  },
});

export default api;
```

**App.js mein use:**
```js
import api from './api/axiosInstance';

// Ab BASE_URL bhi nahi likhna
const res = await api.get('/posts');
const res = await api.post('/posts', { title: 'New' });
```

> "Staging se production pe jaana ho? Ek jagah `baseURL` badlo — ho gaya. Ye hai DRY — Don't Repeat Yourself."

---

## 🎬 PART 7 — Closing Script

**Bolna hai:**
> "Okay bhai! Aaj humne kya kiya — ek line mein:"

### Session ka Journey:

```
JSONPlaceholder  →  concepts seekhe, zero setup, fake save
      ↓  (sirf 1 line change)
   json-server   →  real save, offline, db.json mein data
      ↓  (refactor)
 Axios Instance  →  production-ready, ek jagah URL
```

### Quick Recap Table:

| Operation | Axios Method | Kab use karein |
|-----------|-------------|----------------|
| **Read** | `axios.get(url)` | Data fetch karna ho |
| **Create** | `axios.post(url, data)` | Naya record banana ho |
| **Update** | `axios.patch(url, data)` | Kuch fields update karni ho |
| **Replace** | `axios.put(url, data)` | Poora record replace karna ho |
| **Delete** | `axios.delete(url)` | Record hatana ho |

> "Aur yaad rakho — `async/await` + `try/catch` = professional code. Ye dono hamesha saath chalte hain, jaise Maggi aur 2 minute. Ek ke bina dusra adhoora hai. 😄"

> "Next session mein Axios Interceptors dekhenge — jahan hum har request aur response ko beech mein pakad sakte hain, jaise highway toll naka. Milte hain next class mein! 👋"

---

## 📁 Final File Structure

```
axios-crud-demo/
├── src/
│   ├── App.js                    ← Saara CRUD code (Phase 1 & 2)
│   ├── api/
│   │   └── axiosInstance.js      ← Bonus: Reusable instance
│   └── index.js
├── db.json                       ← json-server ka database (Phase 2)
├── package.json
└── node_modules/
```

---

## ❓ Common Student Questions

**Q: JSONPlaceholder mein data save kyun nahi hota?**
> A: Ye fake API hai practice ke liye. Real project mein backend hoga (Node/Firebase) jo actual database mein save karega. json-server us real backend ka ek simple local version hai.

**Q: json-server aur React dono ek saath kaise chalenge?**
> A: Do alag terminals — ek `npm start`, doosra `json-server --watch db.json --port 3001`. Dono alag ports pe hain, koi conflict nahi.

**Q: Sirf ek line change karne se sab kaam kaisa kiya?**
> A: Kyunki humne `BASE_URL` ek variable mein rakha tha. Axios ko fark nahi padta URL kahan se aa rahi hai — woh sirf request bhejta hai. Ye hai abstraction ka fayda.

**Q: PATCH ke baad UI mein data update kyun kiya manually?**
> A: Server ne response diya, ab React ko bhi batana padega ki state update ho. React khud nahi jaanta server pe kya hua — `setPosts` se hum usse batate hain.

**Q: `async` function mein `await` na likho toh kya hoga?**
> A: Promise milega, data nahi. Console mein `Promise {<pending>}` dikhega aur tum console ko gaaliyan doge. 😄 Hamesha `await` likho.

**Q: db.json file manually edit kar sakte hain?**
> A: Haan! Normal JSON file hai. Direct edit karo — `--watch` flag ki wajah se json-server automatically reload ho jaata hai.

---

*Script ready hai — Recording shuru karo! 🎥*
