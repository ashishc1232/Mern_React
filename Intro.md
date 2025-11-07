
## 1) Install React with Vite (JavaScript first; TypeScript notes included)

### Prereqs

* Node.js 18+ (check with `node -v`)
* npm 9+ or pnpm/yarn (examples use npm)

### Create the app

```bash
# Interactive scaffold
npm create vite@latest my-react-app

# When prompted:
# Framework: React
# Variant: JavaScript (or choose TypeScript if you prefer)
```

Then:

```bash
cd my-react-app
npm install
npm run dev
```

Vite starts a dev server (typically at `http://localhost:5173`) with hot-module replacement (HMR).

**TypeScript?** Choose “React + TypeScript” during scaffold. You’ll get `tsconfig.json` and `.tsx` files out of the box. Everything else below stays the same, with minor TS tweaks noted.

---

## 2) Setup ESLint & Prettier (modern, conflict-free)

Vite’s React template may include a basic ESLint setup if you select the option. If not—or you want a clean, modern setup—use ESLint’s “flat config” (ESLint 9+).

### Install dev dependencies

```bash
npm i -D eslint @eslint/js eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-react-refresh globals
npm i -D prettier eslint-config-prettier
```

> Why no `eslint-plugin-prettier`? Running Prettier separately is simpler and avoids noise in ESLint. `eslint-config-prettier` disables stylistic rules that conflict with Prettier.

### Add ESLint flat config: `eslint.config.js`

```js
// eslint.config.js
import js from "@eslint/js";
import globals from "globals";
import reactRefresh from "eslint-plugin-react-refresh";
import react from "eslint-plugin-react";

/** @type {import('eslint').Linter.FlatConfig[]} */
export default [
  {
    ignores: ["dist", "node_modules"],
  },
  {
    files: ["**/*.{js,jsx}"],
    languageOptions: {
      ecmaVersion: "latest",
      sourceType: "module",
      globals: {
        ...globals.browser,
        ...globals.node,
      },
      parserOptions: {
        ecmaFeatures: { jsx: true },
      },
    },
    plugins: {
      react,
      "react-refresh": reactRefresh,
    },
    settings: {
      react: { version: "detect" },
    },
    rules: {
      ...js.configs.recommended.rules,
      ...react.configs.recommended.rules,
      ...react.configs["jsx-runtime"].rules,

      // Good defaults
      "react-refresh/only-export-components": ["warn", { allowConstantExport: true }],
      "no-unused-vars": ["warn", { argsIgnorePattern: "^_", varsIgnorePattern: "^_" }],
      "no-console": ["warn", { allow: ["warn", "error"] }],
    },
  },
];
```

**TypeScript variant**

```bash
npm i -D typescript typescript-eslint @types/react @types/react-dom
```

`eslint.config.js` additions:

```js
import tseslint from "typescript-eslint";

export default [
  { ignores: ["dist", "node_modules"] },
  ...tseslint.configs.recommended, // add before your JS/React block
  // keep the React block from above but include ts/tsx in files:
  {
    files: ["**/*.{ts,tsx}"],
    // same languageOptions but with project-aware config if you want stricter rules
  },
];
```

### Add Prettier config

`prettier` is an opinionated formatter; you codify style, then let it handle formatting.

`package.json` scripts:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint .",
    "format": "prettier --write ."
  }
}
```

`.prettierrc`:

```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5",
  "printWidth": 100
}
```

`.prettierignore`:

```
dist
node_modules
```

**VS Code tips**

* Install “ESLint” and “Prettier” extensions.
* `.vscode/settings.json` (optional):

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  }
}
```

---

## 3) Create Your First Functional Component

### A minimal component

`src/components/Hello.jsx`

```jsx
export default function Hello({ name = "World" }) {
  return <h2>Hello, {name}!</h2>;
}
```

Use it in `src/App.jsx`

```jsx
import Hello from './components/Hello';

function App() {
  return (
    <main style={{ padding: 24 }}>
      <h1>My First Vite + React App</h1>
      <Hello name="React Developer" />
    </main>
  );
}

export default App;
```

Run:

```bash
npm run dev
```

**What’s happening conceptually**

* A **functional component** is a plain JavaScript function that returns JSX.
* **JSX** is syntax sugar for `React.createElement`, compiled by Vite’s SWC/ESBuild pipeline.
* Props are function parameters. Default props handled via default values or destructuring.

**TypeScript version**

```tsx
// src/components/Hello.tsx
type HelloProps = {
  name?: string;
};

export default function Hello({ name = "World" }: HelloProps) {
  return <h2>Hello, {name}!</h2>;
}
```

---

## 4) Understand the Vite + React Folder Structure

After scaffold:

```
my-react-app/
├─ node_modules/           # third-party packages (auto-generated)
├─ public/                 # static assets served at site root (no processing)
├─ src/
│  ├─ assets/              # images, fonts, SVGs imported in code
│  ├─ App.jsx/tsx          # root component
│  ├─ main.jsx/tsx         # entry, renders <App /> into DOM
│  └─ index.css            # global styles (optional)
├─ .gitignore
├─ index.html              # single HTML shell (Vite injects scripts)
├─ package.json            # scripts & dependencies
├─ vite.config.js/ts       # Vite build/dev config
└─ README.md
```

**Recommended structure as you grow**

```
src/
├─ components/             # reusable UI pieces
├─ pages/                  # route-level components (if using a router)
├─ hooks/                  # custom React hooks
├─ context/                # React Context providers
├─ lib/                    # utilities, API clients
├─ styles/                 # CSS/SCSS modules or global styles
├─ assets/                 # images, icons, fonts
├─ tests/                  # unit tests (Vitest/Jest)
└─ App.jsx / main.jsx
```

**Path aliases (optional)**
Install:

```bash
npm i -D @types/node
```

`vite.config.js`:

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

Now import like `import Hello from '@/components/Hello';`.

---

## 5) What each concept “means” (plain-language)

* **Vite**: A lightning-fast dev server and build tool. It serves native ES modules in dev and bundles for production with Rollup. Fast HMR, minimal config.
* **React**: A UI library for building component-based interfaces. Functional components + hooks are the modern approach.
* **ESLint**: A linter. It enforces code-quality rules (possible bugs, best practices) and catches common mistakes before runtime.
* **Prettier**: A formatter. It enforces a consistent code style automatically (quotes, commas, wrapping). It’s not about correctness—just style.
* **Functional Component**: A function that returns JSX. It’s stateless by default; you add state/effects using hooks like `useState`, `useEffect`.
* **Folder Structure**: A convention to keep code modular and discoverable, scaling well as your app grows.

---

## 6) Common Pitfalls & Quick Fixes

* Port already in use: change with `npm run dev -- --port=3000`.
* ESLint not running: ensure `eslint.config.js` exists and your script uses `eslint .`.
* Prettier not formatting: set VS Code default formatter to Prettier and enable `formatOnSave`.
* Images not loading from `public`: reference with absolute path `/logo.png`, not relative imports.
* Mixing default/named exports: be consistent; default exports are imported without braces.

---

## 7) Minimal Testing Setup (optional but useful)

Install Vitest + React Testing Library:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

`vite.config.js` test block:

```js
test: {
  environment: 'jsdom',
  setupFiles: './src/tests/setup.ts',
}
```

`src/tests/setup.ts`:

```ts
import '@testing-library/jest-dom';
```

---

## 8) Five Interview Questions

1. What problems does Vite solve compared to older bundlers, and how does its dev server achieve fast HMR?
2. In React, what are the differences between a functional component and a class component? Why are hooks preferred today?
3. Explain the roles of ESLint and Prettier. How do you prevent their rules from conflicting?
4. In a Vite + React app, describe the build process from `npm run build` to serving the final assets in production.
5. How would you structure a mid-sized React project and justify locations for components, pages, hooks, context, and utilities?

---

## 9) Copy-Paste Checklist

```bash
# 1) Scaffold
npm create vite@latest my-react-app
# React + JavaScript (or TypeScript)
cd my-react-app
npm install

# 2) ESLint + Prettier
npm i -D eslint @eslint/js eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-react-refresh globals
npm i -D prettier eslint-config-prettier

# 3) Add eslint.config.js and .prettierrc (from above)

# 4) Scripts
# "lint": "eslint .",
# "format": "prettier --write ."

# 5) First component
# src/components/Hello.jsx and import in App.jsx

# 6) Run
npm run dev
