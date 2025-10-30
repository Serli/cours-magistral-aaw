# 🧪 TP3 — API TODO Express + SQLite
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Partir du **TP2** (API TODO + front vanilla) et **ajouter la persistance** avec **SQLite**.
- Remplacer le stockage en mémoire par une **base de données embarquée** (`.db`).
- Respecter le **contrat API v1** (mêmes endpoints, mêmes champs en sortie).
- Aborder : **schéma**, **requêtes préparées**, **migrations simples**, **erreurs**.

---

## 📝 Énoncé

- Implémenter la persistance SQLite pour la ressource `Task` :
  - `id` (string — **clé primaire**)
  - `title` (string, non vide)
  - `priority` (`"URGENTE"|"NORMALE"|"FAIBLE"`)
  - `createdAt` (string ISO — généré **par l’API** comme au TP2)
- Endpoints **inchangés** :
  - `GET /api/v1/todos` — liste triée par `createdAt` desc
  - `POST /api/v1/todos` — crée une tâche
  - `DELETE /api/v1/todos/:id` — supprime une tâche

> ⚠️ **Ne changez pas le contrat** : les clients du TP2 doivent continuer à fonctionner.

---

## 🧰 Dépendances & structure

1. Installer SQLite (choix pédagogique : **better-sqlite3** pour sa simplicité synchrone) :
   ```bash
   npm i better-sqlite3
   ```
2. Structure conseillée :
   ```bash
   tp3-express-sqlite/
   ├── public/
   │   ├── index.html
   │   └── app.js
   ├── dao/
   │   └── index.js
   ├── db/
   │   ├── schema.sql
   │   └── index.js
   ├── server.js
   ├── package.json
   └── .gitignore
   ```
3. `.gitignore` minimal :
   ```gitignore
   *.db
   *.db-journal
   node_modules/
   ```

---

## 🧱 Schéma SQLite (mvp)

Créez `db/schema.sql` :
```sql
CREATE TABLE IF NOT EXISTS todos (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  priority TEXT NOT NULL CHECK (priority IN ('URGENTE','NORMALE','FAIBLE')),
  createdAt TEXT NOT NULL
);
CREATE INDEX IF NOT EXISTS idx_todos_createdAt ON todos(createdAt);
```

> On garde `createdAt` **défini côté API** (cohérent avec TP2). Alternative : `DEFAULT (strftime('%Y-%m-%dT%H:%M:%fZ','now'))` côté DB.

---

## 🧩 Initialisation DB

Créez `db/index.js` :
```js
const Database = require('better-sqlite3');
const fs = require('fs');
const path = require('path');

const DB_PATH = process.env.DB_PATH || path.join(__dirname, '..', 'data.db');
const db = new Database(DB_PATH, { verbose: process.env.SQL_DEBUG ? console.log : null });

// appliquer le schéma
const schema = fs.readFileSync(path.join(__dirname, 'schema.sql'), 'utf8');
db.exec(schema);

module.exports = { db };
```

> Option : activez `SQL_DEBUG=1` pour tracer les requêtes pendant le dev.

---

## 🔎 DAO minimal (requêtes préparées)

Créez `dao/index.js` :
```js
const { db } = require('../db');

const stmtList = db.prepare(`
  SELECT id, title, priority, createdAt
  FROM todos
  ORDER BY datetime(createdAt) DESC
`);

const stmtGet = db.prepare('SELECT id FROM todos WHERE id = ?');
const stmtInsert = db.prepare('INSERT INTO todos (id, title, priority, createdAt) VALUES (?,?,?,?)');
const stmtDelete = db.prepare('DELETE FROM todos WHERE id = ?');

module.exports.todoRepo = {
  list() { return stmtList.all(); },
  exists(id) { return !!stmtGet.get(id); },
  create(todo) { stmtInsert.run(todo.id, todo.title, todo.priority, todo.createdAt); },
  delete(id) { return stmtDelete.run(id).changes; }
};
```

---

## 🛠️ Intégration dans les routes (1/3)

```js
const express = require('express');
const { dao } = require('./dao'); // ou server.js directement
const app = express();

const PRIORITIES = new Set(['URGENTE','NORMALE','FAIBLE']);
function uid() { return Math.random().toString(36).slice(2, 10); }

app.use(express.json());
app.use(express.static('public'));

app.get('/api/v1/todos', (req, res) => {
  res.json(dao.list());
});
```
---

## 🛠️ Intégration dans les routes (2/3)

```js
app.post('/api/v1/todos', (req, res) => {
  const { title, priority } = req.body || {};
  if (typeof title !== 'string' || title.trim() === '') {
    return res.status(400).json({ error: 'title is required (non-empty string)' });
  }
  if (!PRIORITIES.has(priority)) {
    return res.status(400).json({ error: 'priority must be URGENTE | NORMALE | FAIBLE' });
  }
  const todo = { id: uid(), title: title.trim(), priority, createdAt: new Date().toISOString() };
  try {
    todoRepo.create(todo);
    return res.status(201).json(todo);
  } catch (e) {
    console.error('insert failed', e);
    return res.status(500).json({ error: 'DB insert failed' });
  }
});
```

---

## 🛠️ Intégration dans les routes (3/3)

```js
app.delete('/api/v1/todos/:id', (req, res) => {
  const id = req.params.id;
  try {
    const changes = todoRepo.delete(id);
    if (changes === 0) return res.status(404).json({ error: 'Not Found' });
    return res.status(204).end();
  } catch (e) {
    console.error('delete failed', e);
    return res.status(500).json({ error: 'DB delete failed' });
  }
});
```

---

## 🧪 Tests

```bash
# Créer
curl -s -X POST http://localhost:3000/api/v1/todos   -H 'Content-Type: application/json'   -d '{"title":"Acheter du café","priority":"NORMALE"}' | jq

# Lister
curl -s http://localhost:3000/api/v1/todos | jq

# Supprimer
curl -i -X DELETE http://localhost:3000/api/v1/todos/<ID>
```

> Redémarrez le serveur : les tâches doivent **persister** (fichier `data.db`).

---

## ♿ Front : aucun changement requis

- Le front **TP2** continue de fonctionner **à l’identique**.
- Vous pouvez ajouter un badge “(SQLite)” dans le header pour indiquer la version.

---

## 🧱 Bonnes pratiques & options

- **Transactions** pour opérations multiples :
  ```js
  const insertMany = db.transaction((arr) => {
    for (const t of arr) stmtInsert.run(t.id, t.title, t.priority, t.createdAt);
  });
  ```
- **Erreurs** : renvoyez des statuts précis (400/404/500) et loggez côté serveur.
- **Index** : déjà sur `createdAt`. Ajoutez-en si vous filtrez par `priority`.
- **Migrations** : stockez une version (`PRAGMA user_version`) si vous faites évoluer le schéma.
- **Sécurité** : toujours **paramétrer** les requêtes (pas de concat SQL), limiter la taille JSON (`express.json({ limit: '100kb' })`).

---

## ✨ Extensions (facultatives)

- `PATCH /api/v1/todos/:id` (mise à jour partielle)
- Filtre/tri côté API : `GET /todos?priority=URGENTE&sort=-createdAt`
- Tests automatisés (Jest/Vitest + Supertest) avec DB temporaire
- Script `npm run db:reset` pour recréer `data.db`
- Remplacer `uid()` par `crypto.randomUUID()`

---

## 📚 Ressources

- [better-sqlite3](https://github.com/WiseLibs/better-sqlite3)
- [SQLite](https://www.sqlite.org/lang.html)
- [Express](https://expressjs.com/)
