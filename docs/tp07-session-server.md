# 🧪 TP7 — Sessions côté serveur (Stateful)
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Comprendre l’authentification stateful.
- Gérer un store de sessions en mémoire.

---


## 🧱 Étape 1 — Backend

- À la connexion :

1. Générer un sessionId.

```js
sessions[sessionId] = { login, createdAt };
```
    
2. Envoyer un cookie contenant cet id


3. Créer un middleware :

```js
function auth(req, res, next) { ... }
```

Les routes TODO doivent utiliser ce middleware.

```js
app.use("/api/todos/*", auth)
```

## 🔎 Contraintes

- Session propre à l'instance de serveur
