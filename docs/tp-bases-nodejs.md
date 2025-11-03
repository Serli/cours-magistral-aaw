# 🧱 Node.js — Les bases  
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

Comprendre l’**environnement Node.js** pour exécuter du JavaScript **côté serveur** :  
- Exécuter un script, gérer les **modules** (ESM `import`/`export`)  
- Utiliser **npm** & `package.json`  
- Lire/écrire des fichiers (`fs`)  
- Exposer un **serveur HTTP** minimal  
- Saisir l’**asynchronisme** (promises, async/await)  
- Aperçu rapide d’**Express** et de la **structure de projet**

---

## 🧠 Qu’est‑ce que Node.js ?

- **Runtime JavaScript** basé sur **V8** (moteur de Chrome)  
- Fournit des **APIs système** (fichiers, réseau, processus)  
- Conçu pour des **IO non bloquantes** et un modèle **événementiel**

> Browser = JS pour le front  
> Node.js = JS pour le back (scripts, API, CLIs, workers)

---

## ⚙️ Installer & exécuter

- Installer via **nodejs.org** ou **nvm** (gestion multi‑versions)  
- Vérifier : `node -v`, `npm -v`

```bash
node script.mjs
```

> Extension `.mjs` = module ES ; en alternative, `"type": "module"` dans `package.json`

⚠️ Deux systèmes de modules existent en Node.js. 
Comprendre la différence est essentiel pour éviter les erreurs.

---

## 🏛️ CommonJS (CJS)

Historique, et encore courant !

**Import**
```js
const fs = require('fs');
const utils = require('./utils');
```

**Export**
```js
module.exports = function doSomething() { console.log('hello') };
// ou
exports.doSomething = () => console.log('hello');
```

---

## 📦 Modules ES (ESM)

### `math.mjs`
```js
export function add(a, b) { return a + b; }
export const PI = 3.14159;
export default function square(x) { return x * x; }
```

### `main.mjs`
```js
import square, { add, PI } from "./math.mjs";
console.log(add(2, 3), square(4), PI);
```

**À retenir :**  
- Un **export default** par fichier max (optionnel).  
- Les imports relatifs doivent inclure l’**extension** en ESM.

---

## ⚙️ Activer ESM dans node

### Option 1 : `package.json`
```json
{
  "type": "module"
}
```

### Option 2 : `.mjs`
```bash
node app.mjs
```

---

## 🧾 npm & package.json

Créer un projet :  
```bash
mkdir demo && cd demo
npm init -y
```

`package.json` (extrait) :  
```json
{
  "name": "demo",
  "type": "module",
  "scripts": {
    "start": "node src/server.mjs",
    "dev": "node --watch src/server.mjs"
  },
  "dependencies": {},
  "devDependencies": {}
}
```

> `"type": "module"` active les **ES Modules** partout (sans `.mjs`).

---

## 🗂️ Structure de projet type

```
demo/
├─ src/
│  ├─ server.mjs
│  ├─ routes/
│  │  └─ users.mjs
│  └─ lib/
│     └─ db.mjs
├─ .env              # variables d’environnement
├─ package.json
└─ README.md
```

**Conseil :** séparer **routes**, **services/lib**, **config**.

---

## 🔐 Variables d’environnement

- Accès via `process.env` (ex. `process.env.PORT`)  
- Charger depuis un fichier `.env` (en dev) avec **dotenv**

```bash
npm i dotenv
```

```js
// src/config.mjs
import "dotenv/config"; // charge .env automatiquement
export const PORT = process.env.PORT ?? 3000;
```

`.env` :  
```
PORT=4000
API_KEY=xxx
```

> Ne **committez pas** les secrets : utilisez `.gitignore`.

---

## 📁 Lire / écrire des fichiers (fs/promises)

```js
// src/files.mjs
import { readFile, writeFile } from "node:fs/promises";

await writeFile("data.txt", "Hello Node!");
const content = await readFile("data.txt", "utf8");
console.log(content);
```

- Version moderne : **promesses** (`node:fs/promises`)  
- Alternatives : callbacks (`fs.readFile`) ou streams (grands fichiers).

---

## 🌐 Serveur HTTP minimal (sans framework)

```js
// src/server.mjs
import http from "node:http";

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello from Node.js");
  } else {
    res.writeHead(404).end("Not Found");
  }
});

server.listen(3000, () => console.log("http://localhost:3000"));
```

**À retenir :** gestion **manuelle** des routes, en‑têtes, corps, etc.

---

## 🔄 Cycle d’une requête HTTP (schéma)

```
Client (navigateur)  ──►  Socket TCP  ──►  Node.js (Event Loop)
                                       [request arrive]
                                       │ callback (createServer)
                                       ▼
                               Votre logique (routes, IO, DB)
                                       │
                                       ▼
                         Réponse HTTP ◄─ writeHead()/end()
```

> **Event Loop** orchestre les callbacks non bloquants (IO, timers, réseau).

---

## ⏳ Asynchronisme en Node.js

Trois styles historiques :  
1) **Callbacks** (ancien)  
2) **Promises** (standard moderne)  
3) **async/await** (syntaxe moderne sur Promises)

```js
// Promise
readFile("data.txt", "utf8")
  .then(console.log)
  .catch(console.error);

// async/await
try {
  const txt = await readFile("data.txt", "utf8");
  console.log(txt);
} catch (e) { console.error(e); }
```

**Règle d’or :** évitez le **code bloquant** (synchrones) sur le thread principal.

---

## 🧩 Express — serveur minimal

```bash
npm i express
```

```js
// src/server.mjs
import express from "express";
const app = express();

app.use(express.json()); // parse JSON

app.get("/health", (req, res) => res.json({ ok: true }));
app.post("/users", (req, res) => res.status(201).json(req.body));

app.listen(3000, () => console.log("http://localhost:3000"));
```

**Avantages :** routing, middlewares, JSON, erreurs… **plus productif** que `http` brut.

---

## 🧪 Middlewares Express (aperçu)

```js
// logger simple
app.use((req, _res, next) => {
  console.log(req.method, req.path);
  next();
});

// route protégée (exemple)
app.get("/me", (req, res) => {
  // vérifier un token…
  res.json({ id: 1, name: "Alice" });
});
```

> Un **middleware** peut lire/modifier `req`/`res`, ou court‑circuiter la réponse.

---

## 🧰 Outils utiles

- **nodemon** ou `node --watch` → rechargement à chaud en dev  
- **ESLint** + **Prettier** → qualité / formatage  
- **dotenv** → variables d’environnement  
- **cross-env** → set env vars portable (Win/macOS/Linux)

```bash
npm i -D nodemon eslint prettier
```

---

## ⚠️ Attention fréquente

- Oublier `await` → promesse non attendue (réponse vide, race conditions)  
- Bloquer l’event loop (boucles longues, crypto sync, `fs.readFileSync`)  
- Mélanger CommonJS (`require`) et ESM (`import`) **sans config adaptée**  
- Exposer des secrets (`.env`) dans le dépôt

---

## 🧭 Modules noyau (vue d’ensemble)

- `node:fs` (fichiers), `node:http` (serveur), `node:path` (chemins)  
- `node:os` (infos système), `node:url` (URLs), `node:crypto` (hash, HMAC)  
- `node:stream` (flux), `node:events` (émission/écoute d’événements)

> Préfixe **`node:`** recommandé pour les modules intégrés.

---

## 🧠 À retenir

- Node.js = runtime JS serveur, **IO non bloquantes** + **event loop**  
- Utiliser **ES Modules**, **npm** et un `package.json` propre  
- Préférer **Promises/async‑await**  
- Pour des APIs : **Express** simplifie énormément le code  
- Soigner la **structure de projet** + variables d’environnement

---

## 📚 Pour aller plus loin

- 🌐 [nodejs.org – Docs](https://nodejs.org/api/)  
- 📘 [MDN – Guide Promises](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Promise)  
- 🧩 [Express – Getting Started](https://expressjs.com/)  
- 🔧 [npm – Official Docs](https://docs.npmjs.com/)
