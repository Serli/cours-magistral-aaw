# 🧪 TP8 — Authentification par JWT
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Utiliser un token signé.
- Comprendre les architectures stateless.
---


## 🧱 Étape 1 — Backend

- À la connexion :

  1. Signer un JWT contenant :

        ```js
        { "login": "admin", "exp": <timestamp> }
        ```

  2. Stocker le token dans un cookie HttpOnly
  3. Créer un middleware pour :

     - lire le token,
     - vérifier la signature,
     - vérifier l’expiration.

---

## 🔎 Contraintes

- Difficile à révoquer.
