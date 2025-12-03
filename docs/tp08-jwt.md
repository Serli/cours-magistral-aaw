# 🧪 TP8 — Authentification par JWT
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Utiliser un token signé.
- Comprendre les architectures stateless.

---

## 🧱 Étape 1 — Backend

- À la connexion :
  - Signer un JWT contenant : `{ "login": "admin", "exp": <timestamp> }`
  - Stocker le token dans un cookie HttpOnly
  - Créer un middleware pour :
    - lire le token,
    - vérifier la signature,
    - vérifier l’expiration.

---

## 🔎 Contraintes

- Difficile à révoquer.
