# React Hooks Deep Dive

---

## 1. What Are Hooks?

Before React 16.8, function components were basically useless for anything real. They could only receive props and return JSX. No state. No lifecycle. Nothing. You had to use class components for everything important.

Then hooks arrived and flipped everything. Now function components can do it all — and with cleaner code.

> **Simple definition:** Hooks are special functions that let you "hook into" React features (like state and lifecycle) from inside a function component.

You can always spot a hook — it starts with `use`:
`useState`, `useEffect`, `useRef`, `useContext`...

### Two Golden Rules

1. **Only call hooks at the top level** — never inside `if`, loops, or nested functions
2. **Only call hooks inside React function components**

```jsx
//  WRONG — hook inside a condition
function Bad() {
  if (isLoggedIn) {
    const [name, setName] = useState(""); // React will crash
  }
}

//  CORRECT — always at the top, unconditionally
function Good() {
  const [name, setName] = useState("");
  if (isLoggedIn) { /* use name here */ }
}
```

Why? React tracks hooks by the **order they are called**. If you call them conditionally, the order can change between renders and React loses track of which state belongs to which hook.

---

## 2. useState — Your Component's Memory

### The Problem

Every time React re-renders a component, the function runs again from scratch. Regular variables reset. They have no memory between renders.

function BrokenLightBulb() {
  let isOn = false; // resets every render

  return (
    <button onClick={() => {
      isOn = !isOn; // toggles value
      console.log(isOn); // logs correctly
      // but React never re-renders, so UI stays stuck
    }}>
      Bulb is: {isOn ? "ON" : "OFF"}
    </button>
  );
}
###  with useState
import { useState } from "react";

function LightBulb() {
  const [isOn, setIsOn] = useState(false);

  return (
    <button onClick={() => setIsOn(!isOn)}>
      Bulb is: {isOn ? "ON" : "OFF"}
    </button>
  );
}

### Syntax

```jsx
const [value, setValue] = useState(initialValue);
//     ^read    ^write              ^starting value
```

This is just array destructuring. `useState` returns `[currentValue, setterFunction]` and you name them whatever you want.

### Working Example — Like Button

```jsx
import { useState } from "react";

function LikeButton() {
  const [likes, setLikes] = useState(0);
  const [liked, setLiked] = useState(false);

  const handleLike = () => {
    if (!liked) {
      setLikes(likes + 1);
      setLiked(true);
    } else {
      setLikes(likes - 1);
      setLiked(false);
    }
  };

  return (
    <button onClick={handleLike} style={{ fontSize: "1.2rem", padding: "8px 16px" }}>
      {liked ? "❤️" : "🤍"} {likes} Likes
    </button>
  );
}

export default LikeButton;
```

**What's happening:**
- Click once → `liked` becomes `true`, `likes` goes to `1`
- Click again → `liked` becomes `false`, `likes` drops back to `0`
- React re-renders every time a setter is called

### Working Example — Food Cart

Multiple independent states working together. Add items, see the total update live.

```jsx
import { useState } from "react";

const MENU = [
  { id: 1, name: "🍕 Pizza", price: 12 },
  { id: 2, name: "🍔 Burger", price: 9 },
  { id: 3, name: "🌮 Taco", price: 7 },
  { id: 4, name: "🍜 Ramen", price: 11 },
];

function FoodCart() {
  const [cart, setCart] = useState([]);
  const [customerName, setCustomerName] = useState("");

  const addToCart = (item) => {
    // Never push! Always create a new array
    setCart([...cart, { ...item, cartId: Date.now() }]);
  };

  const removeItem = (cartId) => {
    setCart(cart.filter((item) => item.cartId !== cartId));
  };

  const total = cart.reduce((sum, item) => sum + item.price, 0);

  return (
    <div style={{ padding: "1rem", fontFamily: "sans-serif" }}>
      <h2>🍽️ Street Food Menu</h2>

      <div>
        <input
          value={customerName}
          onChange={(e) => setCustomerName(e.target.value)}
          placeholder="Your name"
          style={{ padding: "6px", marginBottom: "12px" }}
        />
      </div>

      <div style={{ display: "flex", gap: "8px", flexWrap: "wrap" }}>
        {MENU.map((item) => (
          <button
            key={item.id}
            onClick={() => addToCart(item)}
            style={{ padding: "8px 12px", cursor: "pointer" }}
          >
            {item.name} — ${item.price}
          </button>
        ))}
      </div>

      <h3>🛒 Cart {customerName ? `for ${customerName}` : ""}</h3>

      {cart.length === 0 && (
        <p style={{ color: "gray" }}>Cart is empty. Add some food!</p>
      )}

      {cart.map((item) => (
        <div
          key={item.cartId}
          style={{ display: "flex", justifyContent: "space-between", maxWidth: "300px" }}
        >
          <span>{item.name} — ${item.price}</span>
          <button onClick={() => removeItem(item.cartId)}>✕</button>
        </div>
      ))}

      {cart.length > 0 && (
        <p style={{ marginTop: "12px" }}>
          <strong>Total: ${total}</strong>
        </p>
      )}
    </div>
  );
}

export default FoodCart;
```

**Key lessons:**
- `cart` is an array in state — totally valid
- Never do `cart.push(item)` — always spread: `[...cart, newItem]`
- `filter` to remove — creates a new array, doesn't mutate the original
- `Date.now()` as a unique ID so each cart item is individually removable

### The Functional Update Form

When new state depends on old state, pass a function instead of a value:

```jsx
// ❌ Risky — reads count from the render's closure
//    In async situations this can be a stale value
setCount(count + 1);

// ✅ Safe — always gets the absolute freshest value
setCount(prev => prev + 1);
```

A concrete case where it matters:

```jsx
import { useState } from "react";

function SpamCounter() {
  const [count, setCount] = useState(0);

  const addFive = () => {
    // React batches these before re-rendering.
    // All 5 calls happen with count still = 0.
    // So result is 1, not 5. Bug!
    // setCount(count + 1); // x5

    // With functional form, each call gets the result of the last.
    // Result is 5. Correct!
    setCount(p => p + 1);
    setCount(p => p + 1);
    setCount(p => p + 1);
    setCount(p => p + 1);
    setCount(p => p + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={addFive}>+5 at once</button>
    </div>
  );
}

export default SpamCounter;
```

### useState — Mental Model

> **useState is a sticky note on your monitor.** Your component function re-runs every render (the whiteboard gets erased), but the sticky note survives. The setter function is your pen — without it, React never knows you changed something.

---

## 3. useEffect — Talking to the Outside World

### The Problem

React components are supposed to be pure — same inputs, same output, every time. No surprises.

But real apps need to reach *outside* the component:
- Fetch data from an API
- Start a timer
- Listen for keyboard events
- Change `document.title`

These are **side effects** — they affect something outside the component's render output.

`useEffect` says: *"After you finish rendering, go do this."*

### Syntax

```jsx
useEffect(() => {
  // your effect

  return () => {
    // cleanup (optional but important)
  };
}, [dependencies]);
```

### The Three Modes

```jsx
// Mode 1: No dependency array — runs after EVERY render
useEffect(() => {
  console.log("Runs after every single render. Usually a bug.");
});

// Mode 2: Empty array — runs ONCE after first render
useEffect(() => {
  console.log("Runs once when component mounts. Good for setup.");
}, []);

// Mode 3: Dependency array — runs when those values change
useEffect(() => {
  console.log("userId changed. Fetch the new user.");
}, [userId]);
```

### Working Example — Live Character Counter

Updates the browser tab title on every keystroke. A real side effect.

```jsx
import { useState, useEffect } from "react";

const LIMIT = 280;

function TweetBox() {
  const [text, setText] = useState("");

  useEffect(() => {
    const remaining = LIMIT - text.length;
    if (text.length === 0) {
      document.title = "New Tweet";
    } else if (remaining < 0) {
      document.title = `⚠️ ${Math.abs(remaining)} over limit!`;
    } else {
      document.title = `${remaining} chars left`;
    }

    return () => {
      document.title = "My App"; // Reset when component unmounts
    };
  }, [text]);

  const remaining = LIMIT - text.length;
  const isOver = remaining < 0;

  return (
    <div style={{ padding: "1rem", fontFamily: "sans-serif", maxWidth: "400px" }}>
      <h2>🐦 Tweet Composer</h2>
      <textarea
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="What's happening?"
        rows={5}
        style={{ width: "100%", padding: "8px", boxSizing: "border-box" }}
      />
      <p style={{ color: isOver ? "red" : remaining < 30 ? "orange" : "green" }}>
        {isOver
          ? `✂️ ${Math.abs(remaining)} chars over limit`
          : `${remaining} characters remaining`}
      </p>
      <button disabled={isOver || text.length === 0}>
        Tweet
      </button>
      <p style={{ fontSize: "0.8rem", color: "gray" }}>
        (Check your browser tab to see the side effect!)
      </p>
    </div>
  );
}

export default TweetBox;
```

### Working Example — Pokémon Lookup (Real API)

This uses the free public PokéAPI — it actually works. Paste and run it.

```jsx
import { useState, useEffect } from "react";

function PokemonCard({ name }) {
  const [pokemon, setPokemon] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Reset everything when name changes
    setPokemon(null);
    setLoading(true);
    setError(null);

    fetch(`https://pokeapi.co/api/v2/pokemon/${name.toLowerCase()}`)
      .then((res) => {
        if (!res.ok) throw new Error(`"${name}" not found!`);
        return res.json();
      })
      .then((data) => {
        setPokemon(data);
        setLoading(false);
      })
      .catch((err) => {
        setError(err.message);
        setLoading(false);
      });
  }, [name]); // Re-fetch every time name changes

  if (loading) return <p>⏳ Loading {name}...</p>;
  if (error)   return <p style={{ color: "red" }}>❌ {error}</p>;

  const typeColors = {
    fire: "#F08030", water: "#6890F0", grass: "#78C850",
    electric: "#F8D030", psychic: "#F85888", normal: "#A8A878",
    poison: "#A040A0", flying: "#98D8D8", bug: "#A8B820",
    rock: "#B8A038", ghost: "#705898", dragon: "#7038F8",
  };

  return (
    <div
      style={{
        border: "1px solid #ddd",
        borderRadius: "12px",
        padding: "1rem",
        maxWidth: "220px",
        textAlign: "center",
        fontFamily: "sans-serif",
        background: "#fafafa",
      }}
    >
      <p style={{ color: "gray", margin: 0 }}>#{String(pokemon.id).padStart(3, "0")}</p>
      <h2 style={{ textTransform: "capitalize", margin: "4px 0" }}>{pokemon.name}</h2>
      <img
        src={pokemon.sprites.other["official-artwork"].front_default || pokemon.sprites.front_default}
        alt={pokemon.name}
        style={{ width: "140px", height: "140px", objectFit: "contain" }}
      />
      <div style={{ display: "flex", gap: "6px", justifyContent: "center", marginBottom: "8px" }}>
        {pokemon.types.map((t) => (
          <span
            key={t.type.name}
            style={{
              padding: "2px 10px",
              borderRadius: "12px",
              background: typeColors[t.type.name] || "#888",
              color: "#fff",
              fontSize: "0.8rem",
              textTransform: "capitalize",
            }}
          >
            {t.type.name}
          </span>
        ))}
      </div>
      <div style={{ fontSize: "0.85rem", color: "#555" }}>
        <p style={{ margin: "4px 0" }}>📏 {pokemon.height / 10}m &nbsp; ⚖️ {pokemon.weight / 10}kg</p>
        <p style={{ margin: "4px 0" }}>
          <strong>Abilities:</strong>{" "}
          {pokemon.abilities.map((a) => a.ability.name).join(", ")}
        </p>
        <p style={{ margin: "4px 0" }}>
          <strong>Base HP:</strong> {pokemon.stats[0].base_stat}
        </p>
      </div>
    </div>
  );
}

function PokemonSearch() {
  const [query, setQuery] = useState("");
  const [search, setSearch] = useState("pikachu");

  const handleSearch = (e) => {
    e.preventDefault();
    if (query.trim()) setSearch(query.trim());
  };

  return (
    <div style={{ padding: "1rem", fontFamily: "sans-serif" }}>
      <h1>🔍 Pokémon Lookup</h1>

      <form onSubmit={handleSearch} style={{ display: "flex", gap: "8px", marginBottom: "1rem" }}>
        <input
          value={query}
          onChange={(e) => setQuery(e.target.value)}
          placeholder="Enter pokémon name..."
          style={{ padding: "6px 10px", fontSize: "1rem" }}
        />
        <button type="submit" style={{ padding: "6px 12px" }}>
          Search
        </button>
      </form>

      <div style={{ display: "flex", gap: "8px", flexWrap: "wrap", marginBottom: "1rem" }}>
        {["pikachu", "charizard", "mewtwo", "snorlax", "eevee"].map((name) => (
          <button
            key={name}
            onClick={() => { setQuery(name); setSearch(name); }}
            style={{ padding: "4px 10px", cursor: "pointer", textTransform: "capitalize" }}
          >
            {name}
          </button>
        ))}
      </div>

      <PokemonCard name={search} />
    </div>
  );
}

export default PokemonSearch;
```

**What's happening:**
- `PokemonSearch` owns the input state and only triggers a real search on form submit
- `PokemonCard` gets `name` as a prop — when it changes, `useEffect` fires and fetches
- Three states handle the three possible conditions: loading, error, data
- The quick-pick buttons let you jump between pokémon to see the effect re-run

### Working Example — Pomodoro Timer

Shows `useEffect` with cleanup, and how `isRunning` acts as the on/off switch.

```jsx
import { useState, useEffect } from "react";

function PomodoroTimer() {
  const [seconds, setSeconds] = useState(25 * 60);
  const [isRunning, setIsRunning] = useState(false);
  const [sessions, setSessions] = useState(0);

  useEffect(() => {
    if (!isRunning) return; // Effect does nothing when paused

    const interval = setInterval(() => {
      setSeconds((prev) => {
        if (prev <= 1) {
          clearInterval(interval);
          setIsRunning(false);
          setSessions((s) => s + 1);
          return 0;
        }
        return prev - 1;
      });
    }, 1000);

    // Cleanup: this runs when isRunning becomes false (pause)
    // or when the component unmounts
    return () => clearInterval(interval);
  }, [isRunning]); // Re-run when isRunning changes

  const reset = () => {
    setIsRunning(false);
    setSeconds(25 * 60);
  };

  const mins = String(Math.floor(seconds / 60)).padStart(2, "0");
  const secs = String(seconds % 60).padStart(2, "0");

  const progress = ((25 * 60 - seconds) / (25 * 60)) * 100;

  return (
    <div style={{ textAlign: "center", padding: "2rem", fontFamily: "sans-serif" }}>
      <h1>🍅 Pomodoro Timer</h1>

      <div
        style={{
          fontSize: "4rem",
          fontWeight: "bold",
          color: seconds < 60 ? "red" : "inherit",
        }}
      >
        {mins}:{secs}
      </div>

      <div style={{ background: "#eee", borderRadius: "8px", height: "8px", margin: "1rem auto", maxWidth: "300px" }}>
        <div
          style={{
            background: "#e74c3c",
            height: "100%",
            borderRadius: "8px",
            width: `${progress}%`,
            transition: "width 1s linear",
          }}
        />
      </div>

      <div style={{ display: "flex", gap: "8px", justifyContent: "center", marginTop: "1rem" }}>
        <button
          onClick={() => setIsRunning((r) => !r)}
          style={{ padding: "10px 24px", fontSize: "1rem", cursor: "pointer" }}
          disabled={seconds === 0}
        >
          {isRunning ? "⏸ Pause" : "▶ Start"}
        </button>
        <button
          onClick={reset}
          style={{ padding: "10px 24px", fontSize: "1rem", cursor: "pointer" }}
        >
          🔄 Reset
        </button>
      </div>

      {seconds === 0 && (
        <p style={{ color: "green", marginTop: "1rem" }}>
          ✅ Session complete! Take a 5-minute break.
        </p>
      )}
      <p style={{ color: "gray", marginTop: "0.5rem" }}>
        Completed sessions: {sessions}
      </p>
    </div>
  );
}

export default PomodoroTimer;
```

**What's happening step by step:**
1. User clicks Start → `isRunning` becomes `true`
2. `useEffect` fires (because `isRunning` changed) → `setInterval` starts
3. User clicks Pause → `isRunning` becomes `false`
4. `useEffect` cleanup runs → `clearInterval` stops the timer
5. `useEffect` runs again with `isRunning = false` → hits `return` immediately, does nothing
6. Click Start again → new interval starts fresh

### The Cleanup Function — Why It Matters

```jsx
// What happens WITHOUT cleanup:
useEffect(() => {
  window.addEventListener("scroll", handleScroll);
  // No cleanup ❌
}, [someValue]);

// Every time someValue changes:
// Render 1  → 1 scroll listener
// Render 2  → 2 scroll listeners (both fire!)
// Render 50 → 50 scroll listeners all firing at once
// Memory leak. App crawls. Users leave bad reviews.
```

```jsx
// WITH cleanup:
useEffect(() => {
  window.addEventListener("scroll", handleScroll);

  return () => window.removeEventListener("scroll", handleScroll); // ✅
}, [someValue]);

// Before re-running, old listener is removed first.
// Always exactly 1 listener. Clean.
```

> **Rule:** If you start something that keeps running — timer, listener, subscription — you must return a cleanup function that stops it.

### useEffect — Mental Model

> **useEffect is a contractor.** You settle into your house first (render completes). Then the contractor goes to work — drilling, running cables, setting up (side effects). The cleanup function is them tidying tools before the next job. If they don't clean up, power tools keep running in your walls forever.

---

## 4. useState vs useEffect — The Core Difference

| | useState | useEffect |
|---|---|---|
| **Purpose** | Store a value | Run code after render |
| **Think of it as** | Memory | Actions |
| **Triggers re-render?** | Yes (via setter) | No (but often calls setState) |
| **Runs when?** | When setter is called | After render, based on deps |
| **Used for** | text, numbers, booleans, arrays, objects | fetch, timers, listeners, DOM |

**The decision:**
- "I need to remember something" → `useState`
- "I need to do something after render" → `useEffect`

### Both Together — Search with Debounce

The most common real-world pattern: `useState` holds data, `useEffect` reacts to it.

```jsx
import { useState, useEffect } from "react";

// Free joke API — no key needed
function JokeSearch() {
  const [category, setCategory] = useState("programming");
  const [joke, setJoke] = useState(null);
  const [loading, setLoading] = useState(false);

  const CATEGORIES = ["programming", "misc", "dark", "pun", "spooky", "christmas"];

  useEffect(() => {
    setLoading(true);
    setJoke(null);

    fetch(`https://v2.jokeapi.dev/joke/${category}?type=twopart`)
      .then((r) => r.json())
      .then((data) => {
        setJoke(data);
        setLoading(false);
      });
  }, [category]); // Re-fetch when category changes

  return (
    <div style={{ padding: "1rem", fontFamily: "sans-serif", maxWidth: "500px" }}>
      <h2>😂 Joke Generator</h2>

      <div style={{ display: "flex", gap: "6px", flexWrap: "wrap", marginBottom: "1rem" }}>
        {CATEGORIES.map((cat) => (
          <button
            key={cat}
            onClick={() => setCategory(cat)}
            style={{
              padding: "4px 12px",
              cursor: "pointer",
              background: category === cat ? "#333" : "#eee",
              color: category === cat ? "#fff" : "#333",
              border: "none",
              borderRadius: "4px",
              textTransform: "capitalize",
            }}
          >
            {cat}
          </button>
        ))}
      </div>

      {loading && <p>⏳ Fetching a {category} joke...</p>}

      {joke && !loading && (
        <div
          style={{
            padding: "1rem",
            background: "#fffbe6",
            borderRadius: "8px",
            border: "1px solid #ffe58f",
          }}
        >
          <p style={{ fontWeight: "bold" }}>🎤 {joke.setup}</p>
          <p style={{ color: "#555" }}>🥁 {joke.delivery}</p>
          <p style={{ fontSize: "0.75rem", color: "gray" }}>Category: {joke.category}</p>
        </div>
      )}

      <button
        onClick={() => {
          // Force re-fetch same category by temporarily switching away
          // Better pattern: use a separate "trigger" state
          setJoke(null);
          setLoading(true);
          fetch(`https://v2.jokeapi.dev/joke/${category}?type=twopart`)
            .then((r) => r.json())
            .then((data) => { setJoke(data); setLoading(false); });
        }}
        style={{ marginTop: "1rem", padding: "8px 16px", cursor: "pointer" }}
      >
        🔄 New Joke
      </button>
    </div>
  );
}

export default JokeSearch;
```

**What's happening:**
- `category` state controls which joke category to fetch
- Clicking a category button updates state → triggers re-render → `useEffect` sees `category` changed → fetches new joke
- `loading` and `joke` state control what's shown on screen
- `useState` holds everything; `useEffect` does the fetching

---

## Quick Reference

```jsx
// ─── useState ───────────────────────────────────────────────

const [value, setValue] = useState(0);           // number
const [name, setName] = useState("");            // string
const [flag, setFlag] = useState(false);         // boolean
const [list, setList] = useState([]);            // array
const [obj, setObj] = useState({ a: 1 });        // object

setValue(42);                                    // set directly
setValue(prev => prev + 1);                      // use previous value
setList(prev => [...prev, newItem]);             // add to array
setList(prev => prev.filter(x => x.id !== id)); // remove from array
setObj(prev => ({ ...prev, key: "new" }));       // update one field


// ─── useEffect ──────────────────────────────────────────────

useEffect(() => { });                            // every render (rare)
useEffect(() => { }, []);                        // once on mount
useEffect(() => { }, [x, y]);                    // when x or y changes

useEffect(() => {                                // with cleanup
  const id = setInterval(fn, 1000);
  return () => clearInterval(id);
}, []);

useEffect(() => {                                // safe async fetch
  let cancelled = false;
  fetch(url)
    .then(r => r.json())
    .then(data => {
      if (!cancelled) setData(data); // ignore if unmounted
    });
  return () => { cancelled = true; };
}, [url]);
```
