# 🧪 TP2 — API TODO avec Express + Front Vanilla
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Développer une **API REST** avec **Express** pour gérer des tâches (TODOs).
- Implémenter **création**, **liste**, **suppression** (CRUD partiel).
- Ajouter une **page HTML** qui consomme l’API : table d’affichage, formulaire de création, bouton de suppression (vanilla JS).
- Appliquer de bonnes pratiques : **statuts HTTP**, **validation**, **middlewares**, **CORS**/**static**.

---

## 📝 Énoncé

- **Ressource** : `Task` avec champs **obligatoires** :
  - `title` (string, non vide)
  - `priority` (enum : `"URGENTE" | "NORMALE" | "FAIBLE"`)
  - `createdAt` (ISO date, string) — défini **par l’API** à la création
  - `id` (string) — généré par l’API
- **Endpoints** (v1) :
  - `GET /api/v1/todos` → liste toutes les tâches
  - `POST /api/v1/todos` → crée une tâche
  - `DELETE /api/v1/todos/:id` → supprime une tâche

> Pas de base de données pour ce TP : stockage **en mémoire** (dans le process Node). ⚠️ Les données disparaissent au redémarrage.

---

## 🧰 Mise en place du projet

1. Créez le dossier `tp2-express-todos/`
2. Initialisez le projet Node :
   ```bash
   npm init -y
   npm i express
   npm i -D nodemon
   ```
3. Ajoutez un script dev dans `package.json` :
   ```json
   {
     "scripts": {
       "dev": "nodemon server.js"
     }
   }
   ```
4. Fichiers à prévoir :
   ```bash
   tp2-express-todos/
   ├── public/
   │   ├── index.html
   │   └── app.js
   ├── server.js
   └── package.json
   ```

---

## 🧱 Étape 1 — Serveur Express minimal

```js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.static('public'));

app.listen(PORT, () => {
  console.log(`API ready on http://localhost:${PORT}`);
});
```

---

## 🧩 Étape 2 — Modèle & stockage en mémoire

```js
const todos = []; // { id, title, priority, createdAt }

function uid() { 
  return Math.random().toString(36).slice(2, 10); 
}
```

---

## 🧪 Étape 3 — Endpoints (contrats)

- GET /api/v1/todos
- POST /api/v1/todos
- DELETE /api/v1/todos/:id

---

## 🔎 Étape 4 — Validation minimale

```js
const PRIORITIES = new Set(["URGENTE", "NORMALE", "FAIBLE"]);
```

---

## 🛠️ Étape 5 — Implémentation (extraits guidés)

```js
app.post('/api/v1/todos', (req, res) => {
  const { title, priority } = req.body;
  const todo = { 
    id: uid(), 
    title: title.trim(), 
    priority, 
    createdAt: new Date().toISOString() 
  };
  todos.push(todo);
  res.status(201).json(todo);
});
```

---

## 🌐 CORS & mêmes origines

- Le front est servi par **le même serveur Express** (`public/`) → pas besoin de CORS.

---

## 🖥️ Étape 6 — Front minimal (vanilla)

### HTML

```html
<main>
  <h1>TODOs</h1>
  <form id="new-todo">
    <input name="title" placeholder="Nouvelle tâche" required />
    <select name="priority">
      <option>URGENTE</option>
      <option selected>NORMALE</option>
      <option>FAIBLE</option>
    </select>
    <button type="submit">Ajouter</button>
  </form>

  <table>
    <thead><tr><th>Titre</th><th>Priorité</th><th>Créée</th><th></th></tr></thead>
    <tbody id="rows"></tbody>
  </table>
</main>
```

---

## 🖥️ Étape 6 — Front minimal (vanilla)

### JS - chargement des TODO

```js
const API = '/api/v1/todos';
const rows = document.querySelector('#rows');
const form = document.querySelector('#new-todo');

function fmt(dateStr){ return new Date(dateStr).toLocaleString(); }

async function load(){
  const res = await fetch(API);
  const data = await res.json();
  rows.innerHTML = data.map(t => `
    <tr>
      <td>${t.title}</td>
      <td>${t.priority}</td>
      <td>${fmt(t.createdAt)}</td>
      <td><button data-id="${t.id}" class="del">✖</button></td>
    </tr>`).join('');
}

load();
```

---

## 🖥️ Étape 6 — Front minimal (vanilla)

### JS - Ajout d'un TODO

```js
form.addEventListener('submit', async (e) => {
  e.preventDefault();
  const fd = new FormData(form);
  const body = { title: fd.get('title'), priority: fd.get('priority') };
  const res = await fetch(API, { 
    method: 'POST', 
    headers: { 'Content-Type': 'application/json' }, 
    body: JSON.stringify(body) 
  });
  if (!res.ok) { alert('Erreur'); return; }
  form.reset();
  await load();
});
```

---

## 🖥️ Étape 6 — Front minimal (vanilla)

### JS - Suppression d'un TODO
```js
rows.addEventListener('click', async (e) => {
  if (e.target.classList.contains('del')) {
    const id = e.target.dataset.id;
    const res = await fetch(`${API}/${id}`, { method: 'DELETE' });
    if (res.status === 204) await load();
  }
});

```

---

## 📚 Ressources

- [Express Docs](https://expressjs.com/)
- [MDN Fetch API](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API)
- [HTTP Cats](https://http.cat/)
