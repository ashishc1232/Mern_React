### useContext Hook (React) – Unique Example

The `useContext` hook is used to share data globally between components **without passing props manually at every level**.

---

## Example: Language Selector App

We will create a small app where:

* The selected language is stored globally
* Any component can read or change the language

---

## 1. Create Context

**LanguageContext.js**

```javascript
import { createContext } from "react";

const LanguageContext = createContext();

export default LanguageContext;
```

---

## 2. Create Context Provider

**LanguageProvider.js**

```javascript
import { useState } from "react";
import LanguageContext from "./LanguageContext";

const LanguageProvider = ({ children }) => {
  const [language, setLanguage] = useState("English");

  const changeLanguage = (lang) => {
    setLanguage(lang);
  };

  return (
    <LanguageContext.Provider value={{ language, changeLanguage }}>
      {children}
    </LanguageContext.Provider>
  );
};

export default LanguageProvider;
```

---

## 3. Wrap App with Provider

**main.jsx or index.js**

```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import LanguageProvider from "./LanguageProvider";

ReactDOM.createRoot(document.getElementById("root")).render(
  <LanguageProvider>
    <App />
  </LanguageProvider>
);
```

---

## 4. Consume Context using useContext

### Component 1: Display Language

**LanguageDisplay.js**

```javascript
import { useContext } from "react";
import LanguageContext from "./LanguageContext";

const LanguageDisplay = () => {
  const { language } = useContext(LanguageContext);

  return <h2>Selected Language: {language}</h2>;
};

export default LanguageDisplay;
```

---

### Component 2: Change Language

**LanguageSelector.js**

```javascript
import { useContext } from "react";
import LanguageContext from "./LanguageContext";

const LanguageSelector = () => {
  const { changeLanguage } = useContext(LanguageContext);

  return (
    <div>
      <button onClick={() => changeLanguage("English")}>English</button>
      <button onClick={() => changeLanguage("Hindi")}>Hindi</button>
      <button onClick={() => changeLanguage("Marathi")}>Marathi</button>
    </div>
  );
};

export default LanguageSelector;
```

---

## 5. Use Components in App

**App.js**

```javascript
import LanguageDisplay from "./LanguageDisplay";
import LanguageSelector from "./LanguageSelector";

const App = () => {
  return (
    <>
      <LanguageDisplay />
      <LanguageSelector />
    </>
  );
};

export default App;
```

---

## Why useContext is used here

* `language` state is shared across multiple components
* No prop drilling
* Clean and centralized state management

---

##  Remember

* `createContext()` creates a context
* `Provider` supplies the data
* `useContext(ContextName)` consumes the data
* Best for theme, language, auth user, settings


* useContext with useReducer
* Common mistakes in useContext
