# 🧪 TP4 — Monorepo Node : Express (API) + React/Vite (client)
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Reprendre l’API TODO du **TP2** (mémoire) ou **TP3** (SQLite) côté **server**.
- Refaire le **front en React** (Vite), **sans React Router** (single page).
- Organiser le projet en **monorepo npm workspaces** : `client/` + `server/`.
- Mettre en place un **proxy Vite** → `/api` vers l’API Express.

---

## 🧰 Structure & Workspaces

```
tp4-monorepo/
├─ package.json            # root, déclare les workspaces
├─ client/                 # app React (Vite)
│  ├─ index.html
│  ├─ src/
│  │  ├─ main.jsx
│  │  └─ App.jsx
│  └─ vite.config.js
└─ server/                 # API Express (TP2/TP3)
   └─ server.js
```

### package.json (root)
```json
{
  "name": "tp4-monorepo",
  "private": true,
  "workspaces": ["client", "server"],
  "scripts": {
    "dev": "npm run -w server dev & npm run -w client dev",
    "build": "npm -w client run build",
    "start": "npm run -w server start"
  }
}
```
> Variante : installez `concurrently` et remplacez `dev` par
> `concurrently "npm:server:dev" "npm:client:dev"`.

---

## 🚀 Initialisation rapide

1) **Root**
```bash
mkdir tp4-monorepo && cd tp4-monorepo
npm init -y
```

---

## 🚀 Initialisation rapide

2) **Server** (copiez votre TP2/TP3)
```bash
mkdir server && cd server
npm init -y
npm i express
npm i -D nodemon
```
`server/package.json` :
```json
{
  "name": "server",
  "type": "commonjs",
  "scripts": {
    "dev": "nodemon server.js",
    "start": "node server.js"
  },
  "dependencies": { "express": "^4.19.0" },
  "devDependencies": { "nodemon": "^3.0.0" }
}
```
> Si vous venez du TP3, ajoutez `better-sqlite3` et vos fichiers `db/`.

---

## 🚀 Initialisation rapide

3) **Client** (Vite + React)
```bash
cd ..
npm create vite@latest client -- --template react
cd client
npm i
```

---

## 🌐 Proxy Vite → API Express

`client/vite.config.js` :
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true
      }
    }
  }
})
```

---

## 🧱 API Express (rappel)

- **Endpoints** : 
  - `GET /api/v1/todos`
  - `POST /api/v1/todos`
  - `DELETE /api/v1/todos/:id`

---

## 🖥️ React — squelette minimal

`client/src/main.jsx`
```jsx
import React from 'react'
import { createRoot } from 'react-dom/client'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

---

## 🖥️ React — squelette minimal

`client/src/App.jsx`
```jsx
import { useEffect, useState } from 'react'

const API = '/api/v1/todos'
const PRIORITIES = ['URGENTE', 'NORMALE', 'FAIBLE']

export default function App() {
  const [todos, setTodos] = useState([])
  const [form, setForm] = useState({ title: '', priority: 'NORMALE' })
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState('')

  async function load() {
    /* TODO */
  }

  async function createTodo(e) {
    /* TODO */
  }

  async function remove(id) {
    /* TODO */
  }

  useEffect(() => { load() }, [])
```

---

## 🖥️ React — squelette minimal

`client/src/App.jsx`
```jsx
  return (
    <main className="container">
      <h1>TODOs</h1>

      <form onSubmit={createTodo} className="row">
        <input
          value={form.title}
          onChange={e => setForm(f => ({ ...f, title: e.target.value }))}
          placeholder="Nouvelle tâche"
          required
        />
        <select
          value={form.priority}
          onChange={e => setForm(f => ({ ...f, priority: e.target.value }))}
        >
          {PRIORITIES.map(p => (<option key={p}>{p}</option>))}
        </select>
        <button type="submit">Ajouter</button>
      </form>
```
---

## 🖥️ React — squelette minimal

`client/src/App.jsx`
```jsx
      {error && <p role="alert" className="error">{error}</p>}
      {loading ? <p>Chargement…</p> : (
        <table>
          <thead>
            <tr><th>Titre</th><th>Priorité</th><th>Créée</th><th/></tr>
          </thead>
          <tbody>
            {todos.map(t => (
              <tr key={t.id}>
                <td>{t.title}</td>
                <td>{t.priority}</td>
                <td>{new Date(t.createdAt).toLocaleString()}</td>
                <td><button onClick={() => remove(t.id)} aria-label={\`Supprimer \${t.title}\`}>✖</button></td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </main>
  )
}
```

---

## 🧪 Lancer en dev

Deux terminaux :
```bash
npm run -w server dev
npm run -w client dev
```

Ou bien depuis la racine :
```bash
npm run dev
```

- Front : `http://localhost:5173`
- API : `http://localhost:3000`

---

## ♻️ Build & déploiement

- `npm -w client run build` → `client/dist/`.
- En prod, servez `client/dist` via Express si souhaité.
