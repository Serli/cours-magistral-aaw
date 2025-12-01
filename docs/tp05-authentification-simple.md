# 🧪 TP5 — Authentification simple via formulaire
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Implémenter un flux de login minimal.
- Contrôler l’accès au front via React.

---


## 🧱 Étape 1 — Backend

- Créer une route POST /api/login
- Elle doit retourner une 200 en cas de succés
- Elle doit retourner une 401 en cas d'échec

---

## 🧩 Étape 2 — Frontend

- Créer /login avec un formulaire.
- Soumission → requête POST vers /login.
- Si succès → affichage du composant todo.
- Empêcher /todo d’être accessible si isAuthenticated = false.

---

## 🔎 Contraintes

- Aucune persistance (pas de cookie).
- Un refresh = déconnexion.
