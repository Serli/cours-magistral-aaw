# ⚛️ React — Les bases
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

Remettre en tête les **fondamentaux de React (hooks)** pour être à l’aise avec une SPA :  
- Composants fonctionnels & **JSX** (⚠️ ce **n’est pas** du HTML)  
- **Props**, **state**, **événements**  
- **useState** & **useEffect** (cycle de vie)  
- Listes, **key**, composition, bonnes pratiques  
- Création rapide de projet avec **Vite**

---

## ⚙️ Qu’est‑ce que React ?

- **Librairie JavaScript** pour construire des **interfaces**.  
- Rendu basé sur un **Virtual DOM** et une **reconciliation** efficace.  
- **Unidirectionnalité des données** : parent → enfant via **props**.

> React ne gère ni le routing, ni les appels réseau par défaut : on ajoute des libs (React Router, SWR, etc.)

---

## 🧩 JSX : ce n’est **pas du HTML**

- **JSX** est une *syntaxe* qui **décrit l’UI** et **se compile en appels JavaScript** (`React.createElement`).  
- On peut mettre des **expressions JS** entre `{ }`.

```jsx
function Hello({ name }) {
  return <h1>Bonjour {name} !</h1>; // JSX → JS au build
}
```

**À retenir :** JSX ≠ HTML → attributs `className`, `htmlFor`, *camelCase* pour les props DOM (`onClick`, `tabIndex`, …).

---

## 🧠 Virtual DOM (schéma simplifié)

```
   État / Props             Composants (JSX)             DOM réel
       │                           │                        │
       ├── (render) ─────────────> │  Virtual DOM (arbre)   │
       │                           │     ┌─ diff ─┐         │
       │                           └───▶ │ patch  │ ───────▶│ (modifs minimales)
       │                                 └────────┘         │
   (setState) ▲                                           (mise à jour écran)
```

- React calcule un **diff** entre l’ancien et le nouveau Virtual DOM, puis **patch** le DOM réel **au minimum**.

---

## 🛠️ Créer un projet avec Vite (recommandé)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev    # lance le serveur de dev
```

**Structure minimale** :
```
my-app/
├─ index.html
├─ src/
│  ├─ main.jsx       # point d’entrée React
│  └─ App.jsx        # composant racine
└─ package.json
```

---

## 🧩 ReactDOM

Dans un projet React le point d’entrée se trouve souvent dans `src/main.jsx` :

```jsx
// main.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

```
index.html
└── <div id="root"></div>   ← React s’y monte
```

- `ReactDOM.createRoot(...)` : crée la racine React. 
- `.render(<App />)` : monte le composant racine dans le **DOM réel**.  
- `<React.StrictMode>` : active des vérifications supplémentaires en dev.  
- `document.getElementById("root")` : élément HTML où l’app s’affiche.

> React **ne remplace pas tout le HTML** : il s’insère dans un élément existant, puis gère cette zone via le **Virtual DOM**.

---

## 🧱 Composants fonctionnels

```jsx
export default function Badge({ label }) {
  return <span className="badge">{label}</span>;
}
```

- Un composant est **une fonction** qui **reçoit des `props`** et **retourne du JSX**.  
- Noms en **PascalCase** (`MyComponent`).

---

## 📦 Props & composition

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h3>{title}</h3>
      <div>{children}</div>
    </div>
  );
}

// Usage
<Card title="Infos"><p>Contenu passé comme children</p></Card>
```

- **Props** = données en lecture seule passées par le parent.  
- `children` permet de composer des UIs riches.

---

## 🔄 État local — `useState`

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Compteur : {count}
    </button>
  );
}
```

- **Jamais** modifier l’état directement (immutabilité).  
- `setCount` peut recevoir une **fonction** avec l’état courant.

---

## ⛓️ Événements

```jsx
<input
  value={name}
  onChange={(e) => setName(e.target.value)}
/>
<button onClick={handleSave}>Enregistrer</button>
```

- Événements en **camelCase** (`onClick`, `onChange`, …).  
- Le gestionnaire reçoit un **SyntheticEvent** (compatible entre navigateurs).

---

## ⏳ Effets — `useEffect`

```jsx
import { useEffect } from "react";

useEffect(() => {
  document.title = "Page chargée";
  return () => { document.title = "Bye"; }; // cleanup optionnel
}, []); // [] → une fois au montage
```

- Sans dépendances : s’exécute **après chaque rendu**.  
- `[]` : **au montage uniquement** (puis cleanup au démontage).  
- `[dep]` : à chaque fois que `dep` change.

⚠️ Attention fréquente : bien **déclarer les dépendances** (ESLint React Hooks aide).

---

## 🌐 Appels réseau (aperçu)

```jsx
const [users, setUsers] = useState([]);

useEffect(() => {
  let cancel = false;
  fetch("/api/users")
    .then(r => r.json())
    .then(d => { if (!cancel) setUsers(d); });
  return () => { cancel = true; };
}, []);
```

- `fetch()` renvoie une **Promise**.  
- Annuler/ignorer les updates si le composant est démonté.

---

## 🧮 Listes & clé `key`

```jsx
<ul>
  {items.map(item => (
    <li key={item.id}>{item.label}</li>
  ))}
</ul>
```

- **`key` stable et unique** par élément (éviter l’index du tableau si possible).

---

## ⬆️ Lever l’état (Lifting state up)

- Si deux composants doivent **partager** un état, le remonter dans leur **parent** et passer des **props**.  
- Alternatives : **Context**, librairies d’état (Redux, Zustand…) si besoin global.

---

## 🧰 Hooks essentiels (aperçu)

- `useState` : état local  
- `useEffect` : effets (synchro, réseau, timers)  
- `useRef` : référence mutable (DOM, cache) sans re‑rendu  
- `useContext` : partage de valeurs globales (thème, auth…)

> D’autres hooks existent (`useMemo`, `useCallback`, etc.) — utiles pour l’optimisation.

---

## ✅ Bonnes pratiques

- Un composant = **une responsabilité** claire.  
- Respecter l’**immutabilité** (copier au lieu de muter).  
- Nommer les handlers `on…` / `handle…`.  
- Découper l’UI en **petits composants réutilisables**.  
- Garder les effets **déterministes** (déps correctes).

---

## ⚠️ Erreurs fréquentes

- Oublier la **`key`** sur les listes.  
- Mettre des **effets inutiles** ou mal dépendants.  
- Modifier l’état **directement** (`state.push(...)`).  
- Confondre **JSX** et **HTML** (`class` vs `className`, `for` vs `htmlFor`).

---

## 🧪 Mini exemple complet

```jsx
// App.jsx
import { useState, useEffect } from "react";

export default function App() {
  const [name, setName] = useState("");
  const [time, setTime] = useState("");

  useEffect(() => {
    const id = setInterval(() => setTime(new Date().toLocaleTimeString()), 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <main>
      <h1>Bonjour {name || "👋"}</h1>
      <p>Il est {time || "…"}</p>
      <input
        placeholder="Votre nom"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
    </main>
  );
}
```

---

## 📚 Pour aller plus loin

- 🌐 Docs officielles : https://react.dev/reference/react 
- 🔌 React Router : https://reactrouter.com/  
- 📦 Vite : https://vitejs.dev/  

---

## 🧠 À retenir

- JSX **décrit** l’interface, **compile en JS** — ce n’est pas du HTML.  
- **Props** (lecture seule) et **state** (mutable via `setState`).  
- **useEffect** synchronise l’UI avec le monde externe.  
- **Virtual DOM** + reconciliation = mises à jour efficaces.  
- Démarrage rapide : **Vite + React**.
