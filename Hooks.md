import { useState, useEffect, useRef } from "react";

// ============================================================
// 🎭 HOOK ACADEMY: useState & useEffect Deep Dive
// ============================================================

const lessons = [
  {
    id: 1,
    hook: "useState",
    emoji: "🧠",
    title: "useState — Your Component's Memory",
    tagline: "Without it, your component is a goldfish. With it, it's an elephant.",
    concept: `Imagine your component is a person. Every time React re-renders, that person gets hit with a memory-erasing ray. 
    
Regular variables? GONE. Function parameters? GONE. Your grandma's birthday? GONE.

useState is the protective helmet that saves certain values from the memory ray. It says: "Hey React, remember THIS value even when you re-render."`,
    code: `// ❌ WITHOUT useState — Component amnesia
function BrokenCounter() {
  let count = 0; // Gets reset to 0 on EVERY render 😭
  
  return (
    <button onClick={() => { count++; console.log(count); }}>
      Count: {count} // Always shows 0, no matter what
    </button>
  );
}

// ✅ WITH useState — Photographic memory
function WorkingCounter() {
  const [count, setCount] = useState(0); // "Remember this!"
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count} // Updates correctly! 🎉
    </button>
  );
}`,
    analogy: "useState is like a sticky note on your monitor. Even when your brain (component) resets, the note (state) remembers what you wrote.",
    demo: "counter",
  },
  {
    id: 2,
    hook: "useState",
    emoji: "🎮",
    title: "useState — Multiple States & Objects",
    tagline: "One hook isn't enough? Stack 'em like pancakes.",
    concept: `You can have as many useState calls as you want! Each one stores ONE piece of state independently.

Think of each useState as a separate drawer in a dresser. Socks in drawer 1, shirts in drawer 2, that embarrassing photo in drawer 3. They don't interfere with each other!

IMPORTANT RULE: Never mutate state directly. Always use the setter function. React won't know you changed it otherwise — like editing someone's diary with invisible ink and wondering why they act the same.`,
    code: `function UserProfile() {
  const [name, setName] = useState("Alex");
  const [age, setAge] = useState(25);
  const [isOnline, setIsOnline] = useState(false);
  
  // ❌ WRONG — Direct mutation (React can't see this!)
  // name = "Bob"; // React: "Did something happen? I see nothing."
  
  // ✅ RIGHT — Using setter (React gets notified!)
  const birthday = () => setAge(age + 1); // React: "Ah yes! Re-render time!"
  
  return (
    <div>
      <p>{name} is {age} years old</p>
      <button onClick={() => setIsOnline(!isOnline)}>
        {isOnline ? "🟢 Online" : "🔴 Offline"}
      </button>
      <button onClick={birthday}>🎂 Happy Birthday!</button>
    </div>
  );
}`,
    analogy: "Calling the setter function is like screaming 'REACT! I CHANGED SOMETHING!' into the void. Without it, React is peacefully unaware.",
    demo: "profile",
  },
  {
    id: 3,
    hook: "useEffect",
    emoji: "🌍",
    title: "useEffect — Talking to the Outside World",
    tagline: "Your component is a hermit. useEffect is its phone.",
    concept: `React components are pure and self-contained — they just take props, use state, and return JSX. But real apps need to DO things: fetch data, set timers, subscribe to events, update the document title...

These are called "side effects" — things that reach outside the component's bubble.

useEffect says: "After rendering, go do this thing in the outside world."

The three modes of useEffect:
• No dependency array → runs after EVERY render (rarely what you want)
• Empty array [] → runs ONCE after first render (perfect for "on mount")
• [dep1, dep2] → runs when those values change`,
    code: `function DocumentTitle({ username }) {
  // 🌍 Side effect: changing document title (outside React!)
  useEffect(() => {
    document.title = \`Hello, \${username}!\`;
    
    // Cleanup: reset when component unmounts or username changes
    return () => {
      document.title = "My App";
    };
  }, [username]); // Only re-run when username changes
  
  return <h1>Welcome, {username}</h1>;
}

function DataFetcher() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    // Runs ONCE after first render ([] = empty deps)
    fetch("https://api.example.com/data")
      .then(r => r.json())
      .then(d => setData(d));
  }, []); // [] means "only on mount"
  
  return <div>{data ? data.message : "Loading..."}</div>;
}`,
    analogy: "useEffect is like a personal assistant who waits until you finish your presentation (render), then goes off to make calls, book meetings, and update the whiteboard (side effects).",
    demo: "effect",
  },
  {
    id: 4,
    hook: "useEffect",
    emoji: "🧹",
    title: "useEffect Cleanup — Always Clean Your Room",
    tagline: "If you start something, you better finish it. Mom was right.",
    concept: `Some side effects are like party guests — they don't leave on their own. Event listeners, timers, subscriptions... if you don't clean them up, they'll pile up in memory like unwashed dishes.

The cleanup function (returned from useEffect) runs:
• Before the effect runs again (when deps change)
• When the component unmounts (leaves the page)

Without cleanup: Every re-render adds ANOTHER listener. Soon you have 50 scroll listeners all fighting for attention. It's chaos. It's a memory leak. It's a bug waiting to embarrass you in production.`,
    code: `function WindowSize() {
  const [size, setSize] = useState({ w: window.innerWidth, h: window.innerHeight });
  
  useEffect(() => {
    const handleResize = () => {
      setSize({ w: window.innerWidth, h: window.innerHeight });
    };
    
    // 🎉 Start the party — add listener
    window.addEventListener("resize", handleResize);
    
    // 🧹 Clean up — remove listener when done
    // Without this: listeners multiply like rabbits 🐰🐰🐰
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // Only attach once
  
  return <p>Window: {size.w} × {size.h}</p>;
}

// Timer example — cleanup prevents the zombie timer
function Countdown({ seconds }) {
  const [time, setTime] = useState(seconds);
  
  useEffect(() => {
    const timer = setInterval(() => setTime(t => t - 1), 1000);
    
    return () => clearInterval(timer); // Kill the timer when done!
    // Without this: timer keeps running after component unmounts 👻
  }, []);
  
  return <p>{time > 0 ? \`⏱ \${time}s\` : "💥 BOOM!"}</p>;
}`,
    analogy: "The cleanup function is your mom saying 'if you open something, close it.' Opened a socket? Close it. Started a timer? Clear it. Subscribed to something? Unsubscribe.",
    demo: "cleanup",
  },
];

// =====================
// DEMO COMPONENTS
// =====================

function CounterDemo() {
  const [count, setCount] = useState(0);
  const [history, setHistory] = useState([]);

  const increment = () => {
    setCount((c) => c + 1);
    setHistory((h) => [...h, `+1 → ${count + 1}`]);
  };
  const decrement = () => {
    setCount((c) => c - 1);
    setHistory((h) => [...h, `-1 → ${count - 1}`]);
  };
  const reset = () => {
    setCount(0);
    setHistory((h) => [...h, "reset → 0"]);
  };

  return (
    <div style={styles.demo}>
      <div style={styles.demoTitle}>🎮 Live Demo: Counter</div>
      <div style={styles.bigNumber}>{count}</div>
      <div style={styles.btnRow}>
        <button style={styles.demoBtn} onClick={decrement}>
          − 1
        </button>
        <button style={{ ...styles.demoBtn, background: "#374151" }} onClick={reset}>
          Reset
        </button>
        <button style={styles.demoBtn} onClick={increment}>
          + 1
        </button>
      </div>
      {history.length > 0 && (
        <div style={styles.history}>
          <div style={styles.historyTitle}>State history:</div>
          <div style={styles.historyList}>
            {history.slice(-5).map((h, i) => (
              <span key={i} style={styles.historyItem}>
                {h}
              </span>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}

function ProfileDemo() {
  const [name, setName] = useState("Alex");
  const [mood, setMood] = useState("😐");
  const [level, setLevel] = useState(1);
  const moods = ["😐", "😄", "😎", "🤩", "🥳"];

  return (
    <div style={styles.demo}>
      <div style={styles.demoTitle}>👤 Live Demo: Multiple States</div>
      <div style={styles.profileCard}>
        <div style={styles.profileEmoji}>{mood}</div>
        <div style={styles.profileName}>{name}</div>
        <div style={styles.profileLevel}>Level {level} Developer</div>
      </div>
      <div style={styles.btnRow}>
        <button
          style={styles.demoBtn}
          onClick={() => setMood(moods[(moods.indexOf(mood) + 1) % moods.length])}
        >
          Change Mood
        </button>
        <button style={styles.demoBtn} onClick={() => setLevel((l) => l + 1)}>
          Level Up! ⬆
        </button>
      </div>
      <input
        style={styles.input}
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Change your name"
      />
    </div>
  );
}

function EffectDemo() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const [fetched, setFetched] = useState(false);
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    let interval;
    if (isRunning) {
      interval = setInterval(() => setSeconds((s) => s + 1), 1000);
    }
    return () => clearInterval(interval);
  }, [isRunning]);

  const fetchData = () => {
    setPosts([]);
    setFetched(false);
    setTimeout(() => {
      setPosts(["Post #1: React Hooks 🔥", "Post #2: useEffect Magic ✨", "Post #3: useState Tips 💡"]);
      setFetched(true);
    }, 1500);
  };

  return (
    <div style={styles.demo}>
      <div style={styles.demoTitle}>⚡ Live Demo: useEffect in Action</div>
      <div style={styles.effectGrid}>
        <div style={styles.effectBox}>
          <div style={styles.effectLabel}>⏱ Timer (interval side effect)</div>
          <div style={styles.timerNum}>{seconds}s</div>
          <div style={styles.btnRow}>
            <button style={styles.demoBtn} onClick={() => setIsRunning((r) => !r)}>
              {isRunning ? "⏸ Pause" : "▶ Start"}
            </button>
            <button
              style={{ ...styles.demoBtn, background: "#374151" }}
              onClick={() => {
                setSeconds(0);
                setIsRunning(false);
              }}
            >
              Reset
            </button>
          </div>
        </div>
        <div style={styles.effectBox}>
          <div style={styles.effectLabel}>🌐 Data Fetch (async side effect)</div>
          <button style={{ ...styles.demoBtn, marginBottom: 8 }} onClick={fetchData}>
            Fetch Posts
          </button>
          {!fetched && posts.length === 0 && <div style={styles.fetchHint}>Press button to fetch</div>}
          {!fetched && posts.length === 0 && (
            <div id="loader" style={{ display: "none", color: "#9ca3af" }}>
              Loading...
            </div>
          )}
          {fetched && posts.map((p, i) => <div key={i} style={styles.post}>{p}</div>)}
        </div>
      </div>
    </div>
  );
}

function CleanupDemo() {
  const [mounted, setMounted] = useState(true);
  const [log, setLog] = useState([]);

  const addLog = (msg) => setLog((l) => [`[${new Date().toLocaleTimeString()}] ${msg}`, ...l.slice(0, 6)]);

  function TimerWidget({ onLog }) {
    const [t, setT] = useState(0);

    useEffect(() => {
      onLog("✅ Effect started — timer running");
      const id = setInterval(() => setT((x) => x + 1), 1000);
      return () => {
        onLog("🧹 Cleanup ran — timer stopped");
        clearInterval(id);
      };
    }, []);

    return (
      <div style={styles.timerWidget}>
        <span style={{ color: "#34d399" }}>Timer Component</span> — {t}s elapsed
      </div>
    );
  }

  return (
    <div style={styles.demo}>
      <div style={styles.demoTitle}>🧹 Live Demo: Cleanup Function</div>
      <div style={styles.btnRow}>
        <button
          style={styles.demoBtn}
          onClick={() => {
            setMounted((m) => {
              if (!m) addLog("🔧 Component mounted");
              return !m;
            });
          }}
        >
          {mounted ? "💀 Unmount Component" : "🔧 Mount Component"}
        </button>
      </div>
      {mounted && <TimerWidget onLog={addLog} />}
      <div style={styles.logBox}>
        <div style={styles.logTitle}>Effect Log:</div>
        {log.length === 0 && <div style={styles.logHint}>Mount/unmount to see cleanup in action</div>}
        {log.map((l, i) => (
          <div key={i} style={styles.logLine}>
            {l}
          </div>
        ))}
      </div>
    </div>
  );
}

const demoMap = {
  counter: CounterDemo,
  profile: ProfileDemo,
  effect: EffectDemo,
  cleanup: CleanupDemo,
};

// =====================
// MAIN APP
// =====================

export default function HookAcademy() {
  const [activeLesson, setActiveLesson] = useState(0);
  const [showCode, setShowCode] = useState(false);
  const [tab, setTab] = useState("concept");

  const lesson = lessons[activeLesson];
  const DemoComponent = demoMap[lesson.demo];

  return (
    <div style={styles.root}>
      {/* Header */}
      <div style={styles.header}>
        <div style={styles.headerBadge}>React Hooks Academy</div>
        <h1 style={styles.headerTitle}>
          useState <span style={styles.amp}>&</span> useEffect
        </h1>
        <p style={styles.headerSub}>The two hooks you'll use in literally every React component, ever.</p>
      </div>

      {/* Hook type pills */}
      <div style={styles.hookPills}>
        <div style={styles.pillGroup}>
          <div style={styles.pillLabel}>🧠 useState</div>
          <div style={styles.pillSub}>Memory. State. Values.</div>
        </div>
        <div style={styles.pillVs}>VS</div>
        <div style={styles.pillGroup}>
          <div style={styles.pillLabel}>🌍 useEffect</div>
          <div style={styles.pillSub}>Side effects. World. Time.</div>
        </div>
      </div>

      {/* Lesson navigation */}
      <div style={styles.lessonNav}>
        {lessons.map((l, i) => (
          <button
            key={l.id}
            style={{
              ...styles.lessonBtn,
              ...(activeLesson === i ? styles.lessonBtnActive : {}),
              borderColor: l.hook === "useState" ? "#818cf8" : "#34d399",
              color: activeLesson === i ? "#fff" : "#9ca3af",
              background: activeLesson === i
                ? l.hook === "useState" ? "#4f46e5" : "#065f46"
                : "transparent",
            }}
            onClick={() => {
              setActiveLesson(i);
              setTab("concept");
            }}
          >
            <span>{l.emoji}</span>
            <span style={styles.lessonBtnText}>Lesson {l.id}</span>
            <span style={styles.lessonHookTag}>{l.hook}</span>
          </button>
        ))}
      </div>

      {/* Lesson content */}
      <div style={styles.lessonCard}>
        <div style={styles.lessonHeader}>
          <span style={styles.lessonEmoji}>{lesson.emoji}</span>
          <div>
            <div style={styles.lessonHookBadge}>{lesson.hook}</div>
            <h2 style={styles.lessonTitle}>{lesson.title}</h2>
            <p style={styles.lessonTagline}>"{lesson.tagline}"</p>
          </div>
        </div>

        {/* Tabs */}
        <div style={styles.tabs}>
          {["concept", "code", "demo"].map((t_) => (
            <button
              key={t_}
              style={{ ...styles.tab, ...(tab === t_ ? styles.tabActive : {}) }}
              onClick={() => setTab(t_)}
            >
              {t_ === "concept" ? "💡 Concept" : t_ === "code" ? "💻 Code" : "🎮 Demo"}
            </button>
          ))}
        </div>

        {tab === "concept" && (
          <div>
            <div style={styles.conceptText}>
              {lesson.concept.split("\n").map((line, i) =>
                line.trim() ? <p key={i} style={styles.conceptPara}>{line.trim()}</p> : null
              )}
            </div>
            <div style={styles.analogyBox}>
              <span style={styles.analogyIcon}>🔑</span>
              <div>
                <div style={styles.analogyLabel}>Analogy</div>
                <div style={styles.analogyText}>{lesson.analogy}</div>
              </div>
            </div>
          </div>
        )}

        {tab === "code" && (
          <div style={styles.codeBlock}>
            <pre style={styles.pre}>{lesson.code}</pre>
          </div>
        )}

        {tab === "demo" && (
          <div>
            <DemoComponent />
          </div>
        )}
      </div>

      {/* Quick Reference */}
      <div style={styles.quickRef}>
        <h3 style={styles.quickRefTitle}>📊 Quick Reference: When to use which?</h3>
        <div style={styles.quickRefGrid}>
          <div style={styles.quickRefCard}>
            <div style={styles.quickRefHook}>🧠 useState</div>
            <div style={styles.quickRefItems}>
              <div style={styles.quickRefItem}>✅ Storing a counter, name, list</div>
              <div style={styles.quickRefItem}>✅ Toggle: open/closed, on/off</div>
              <div style={styles.quickRefItem}>✅ Form input values</div>
              <div style={styles.quickRefItem}>✅ Any value that makes component re-render</div>
              <div style={{ ...styles.quickRefItem, color: "#f87171" }}>❌ NOT for side effects</div>
            </div>
          </div>
          <div style={styles.quickRefCard}>
            <div style={{ ...styles.quickRefHook, color: "#34d399" }}>🌍 useEffect</div>
            <div style={styles.quickRefItems}>
              <div style={styles.quickRefItem}>✅ Fetching data from an API</div>
              <div style={styles.quickRefItem}>✅ Setting up timers/intervals</div>
              <div style={styles.quickRefItem}>✅ Adding event listeners</div>
              <div style={styles.quickRefItem}>✅ Syncing with external systems</div>
              <div style={{ ...styles.quickRefItem, color: "#f87171" }}>❌ NOT for computing values</div>
            </div>
          </div>
        </div>
      </div>

      {/* Footer */}
      <div style={styles.footer}>
        <span style={styles.footerText}>
          Master these two hooks and you can build almost anything in React. 🚀
        </span>
      </div>
    </div>
  );
}

// =====================
// STYLES
// =====================

const styles = {
  root: {
    minHeight: "100vh",
    background: "#0a0a0f",
    color: "#e2e8f0",
    fontFamily: "'Courier New', Courier, monospace",
    padding: "0 0 60px 0",
  },
  header: {
    textAlign: "center",
    padding: "48px 24px 32px",
    background: "linear-gradient(180deg, #0f0f1a 0%, #0a0a0f 100%)",
    borderBottom: "1px solid #1e1e2e",
  },
  headerBadge: {
    display: "inline-block",
    padding: "4px 14px",
    borderRadius: 20,
    background: "#1e1b4b",
    color: "#a5b4fc",
    fontSize: 12,
    letterSpacing: 2,
    textTransform: "uppercase",
    marginBottom: 16,
  },
  headerTitle: {
    fontSize: "clamp(28px, 5vw, 52px)",
    fontWeight: 900,
    color: "#fff",
    margin: "0 0 8px 0",
    letterSpacing: -1,
  },
  amp: {
    color: "#818cf8",
  },
  headerSub: {
    color: "#6b7280",
    fontSize: 14,
    margin: 0,
  },
  hookPills: {
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    gap: 24,
    padding: "24px 16px",
    flexWrap: "wrap",
  },
  pillGroup: {
    textAlign: "center",
    padding: "16px 32px",
    border: "1px solid #1e1e2e",
    borderRadius: 12,
    background: "#0f0f1a",
    minWidth: 160,
  },
  pillLabel: {
    fontSize: 18,
    fontWeight: 700,
    color: "#fff",
  },
  pillSub: {
    fontSize: 11,
    color: "#6b7280",
    marginTop: 4,
  },
  pillVs: {
    fontSize: 20,
    fontWeight: 900,
    color: "#374151",
  },
  lessonNav: {
    display: "flex",
    gap: 8,
    padding: "0 16px 16px",
    overflowX: "auto",
    justifyContent: "center",
    flexWrap: "wrap",
  },
  lessonBtn: {
    display: "flex",
    alignItems: "center",
    gap: 8,
    padding: "8px 16px",
    borderRadius: 8,
    border: "1px solid",
    cursor: "pointer",
    fontSize: 12,
    fontFamily: "'Courier New', monospace",
    transition: "all 0.2s",
  },
  lessonBtnActive: {},
  lessonBtnText: {
    fontWeight: 700,
  },
  lessonHookTag: {
    fontSize: 10,
    opacity: 0.7,
  },
  lessonCard: {
    maxWidth: 800,
    margin: "0 auto 24px",
    padding: "0 16px",
  },
  lessonHeader: {
    display: "flex",
    gap: 20,
    alignItems: "flex-start",
    padding: "28px 28px 0",
    background: "#0f0f1a",
    borderRadius: "12px 12px 0 0",
    border: "1px solid #1e1e2e",
    borderBottom: "none",
  },
  lessonEmoji: {
    fontSize: 40,
    lineHeight: 1,
    marginTop: 4,
  },
  lessonHookBadge: {
    fontSize: 11,
    color: "#818cf8",
    letterSpacing: 2,
    textTransform: "uppercase",
    marginBottom: 4,
  },
  lessonTitle: {
    margin: "0 0 4px 0",
    fontSize: "clamp(16px, 3vw, 22px)",
    fontWeight: 700,
    color: "#fff",
  },
  lessonTagline: {
    margin: 0,
    fontSize: 13,
    color: "#6b7280",
    fontStyle: "italic",
  },
  tabs: {
    display: "flex",
    gap: 0,
    background: "#0f0f1a",
    border: "1px solid #1e1e2e",
    borderTop: "1px solid #1e1e2e",
    borderBottom: "none",
    padding: "12px 20px 0",
  },
  tab: {
    padding: "8px 20px",
    background: "transparent",
    border: "none",
    borderBottom: "2px solid transparent",
    color: "#6b7280",
    cursor: "pointer",
    fontSize: 13,
    fontFamily: "'Courier New', monospace",
    transition: "all 0.2s",
  },
  tabActive: {
    color: "#fff",
    borderBottom: "2px solid #818cf8",
  },
  conceptText: {
    padding: "24px 28px",
    background: "#0f0f1a",
    border: "1px solid #1e1e2e",
    borderTop: "none",
  },
  conceptPara: {
    margin: "0 0 12px 0",
    lineHeight: 1.7,
    fontSize: 14,
    color: "#d1d5db",
  },
  analogyBox: {
    display: "flex",
    gap: 12,
    padding: "16px 20px",
    background: "#0c1a0e",
    border: "1px solid #1a3a1e",
    borderTop: "none",
    borderRadius: "0 0 12px 12px",
  },
  analogyIcon: { fontSize: 24 },
  analogyLabel: {
    fontSize: 11,
    color: "#34d399",
    letterSpacing: 1,
    textTransform: "uppercase",
    marginBottom: 4,
  },
  analogyText: {
    fontSize: 13,
    color: "#86efac",
    lineHeight: 1.6,
  },
  codeBlock: {
    background: "#050508",
    border: "1px solid #1e1e2e",
    borderTop: "none",
    borderRadius: "0 0 12px 12px",
    overflow: "hidden",
  },
  pre: {
    margin: 0,
    padding: "24px",
    fontSize: "12px",
    lineHeight: 1.8,
    color: "#a5b4fc",
    overflowX: "auto",
    whiteSpace: "pre",
  },
  demo: {
    background: "#0f0f1a",
    border: "1px solid #1e1e2e",
    borderTop: "none",
    borderRadius: "0 0 12px 12px",
    padding: "24px",
  },
  demoTitle: {
    fontSize: 13,
    color: "#818cf8",
    marginBottom: 16,
    letterSpacing: 1,
    textTransform: "uppercase",
  },
  bigNumber: {
    fontSize: 72,
    fontWeight: 900,
    color: "#fff",
    textAlign: "center",
    margin: "16px 0",
  },
  btnRow: {
    display: "flex",
    gap: 8,
    justifyContent: "center",
    flexWrap: "wrap",
    marginTop: 8,
  },
  demoBtn: {
    padding: "10px 20px",
    background: "#4f46e5",
    color: "#fff",
    border: "none",
    borderRadius: 8,
    cursor: "pointer",
    fontSize: 14,
    fontFamily: "'Courier New', monospace",
    fontWeight: 700,
  },
  history: {
    marginTop: 16,
    padding: 12,
    background: "#050508",
    borderRadius: 8,
    border: "1px solid #1e1e2e",
  },
  historyTitle: {
    fontSize: 11,
    color: "#6b7280",
    marginBottom: 8,
  },
  historyList: {
    display: "flex",
    gap: 8,
    flexWrap: "wrap",
  },
  historyItem: {
    fontSize: 11,
    padding: "2px 8px",
    background: "#1e1e2e",
    borderRadius: 4,
    color: "#a5b4fc",
  },
  profileCard: {
    textAlign: "center",
    padding: "16px 0",
  },
  profileEmoji: {
    fontSize: 48,
    marginBottom: 8,
  },
  profileName: {
    fontSize: 22,
    fontWeight: 700,
    color: "#fff",
  },
  profileLevel: {
    fontSize: 12,
    color: "#818cf8",
    marginTop: 4,
  },
  input: {
    width: "100%",
    marginTop: 12,
    padding: "10px 14px",
    background: "#050508",
    border: "1px solid #374151",
    borderRadius: 8,
    color: "#fff",
    fontSize: 14,
    fontFamily: "'Courier New', monospace",
    boxSizing: "border-box",
  },
  effectGrid: {
    display: "grid",
    gridTemplateColumns: "1fr 1fr",
    gap: 16,
  },
  effectBox: {
    padding: 16,
    background: "#050508",
    border: "1px solid #1e1e2e",
    borderRadius: 8,
  },
  effectLabel: {
    fontSize: 11,
    color: "#6b7280",
    marginBottom: 12,
  },
  timerNum: {
    fontSize: 40,
    fontWeight: 900,
    color: "#34d399",
    marginBottom: 8,
  },
  fetchHint: {
    fontSize: 12,
    color: "#4b5563",
    fontStyle: "italic",
  },
  post: {
    fontSize: 12,
    padding: "6px 0",
    borderBottom: "1px solid #1e1e2e",
    color: "#a5b4fc",
  },
  timerWidget: {
    padding: 16,
    background: "#050508",
    border: "1px solid #065f46",
    borderRadius: 8,
    fontSize: 14,
    marginTop: 16,
    marginBottom: 12,
  },
  logBox: {
    marginTop: 12,
    padding: 12,
    background: "#050508",
    borderRadius: 8,
    border: "1px solid #1e1e2e",
    minHeight: 80,
  },
  logTitle: {
    fontSize: 11,
    color: "#6b7280",
    marginBottom: 8,
  },
  logHint: {
    fontSize: 12,
    color: "#374151",
    fontStyle: "italic",
  },
  logLine: {
    fontSize: 12,
    color: "#d1d5db",
    padding: "3px 0",
    borderBottom: "1px solid #0f0f1a",
  },
  quickRef: {
    maxWidth: 800,
    margin: "0 auto 24px",
    padding: "0 16px",
  },
  quickRefTitle: {
    fontSize: 16,
    color: "#9ca3af",
    marginBottom: 12,
    fontWeight: 700,
  },
  quickRefGrid: {
    display: "grid",
    gridTemplateColumns: "1fr 1fr",
    gap: 16,
  },
  quickRefCard: {
    padding: 20,
    background: "#0f0f1a",
    border: "1px solid #1e1e2e",
    borderRadius: 12,
  },
  quickRefHook: {
    fontSize: 16,
    fontWeight: 700,
    color: "#818cf8",
    marginBottom: 12,
  },
  quickRefItems: {
    display: "flex",
    flexDirection: "column",
    gap: 6,
  },
  quickRefItem: {
    fontSize: 12,
    color: "#9ca3af",
  },
  footer: {
    textAlign: "center",
    padding: "24px 16px",
    borderTop: "1px solid #1e1e2e",
    maxWidth: 800,
    margin: "0 auto",
  },
  footerText: {
    fontSize: 14,
    color: "#4b5563",
  },
};
