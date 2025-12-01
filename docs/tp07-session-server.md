# 🧪 TP7 — Sessions côté serveur (Stateful)
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Manipuler les cookies.
- Persister l’état de connexion côté client.

---


## 🧱 Étape 1 — Backend

- Lors du login ajouter un cookie contenant le login

```js
const cookieParser = require('cookie-parser')
app.use(cookieParser());
...

app.post('/api/login', (req, res)=>{
    ...
    res.cookie('authentification', USER_LOGIN);
    ...
});
```
- Créer une route /api/me qui retournera l'utilisateur correspondant au ccokie
```js
app.get("/api/me", (req, res) => {
    const cookieValue = req.cookies.authentification;
    ...
});
```

---

## 🧩 Étape 2 — Frontend

- Au chargement, envoyer GET /me.
- Si la route répond OK → l’utilisateur est connecté.
- Sinon → on affiche le composant de connection.
---

## 🔎 Contraintes

- Cookie lisible et manipulable.
