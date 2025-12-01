# 🧪 TP9 — Sessions sécurisées en base de données
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectifs

- Mise en place d’un système de sessions professionnel.
- Gestion de l’expiration.
- Table dédiée.
---


## 🧱 Étape 1 — Backend

- Créer une table :
 ```sql
sessions (
id uuid primary key,
user_id int,
created_at datetime,
expires_at datetime
)
```


- À la connexion :

    - Insérer une session en DB.
    - Placer sessionId dans un cookie HttpOnly.
    - Retourner OK.

- Le middleware :

    - vérifie sessionId

    - charge la session

    - contrôle l’expiration

    - charge l’utilisateur

- Route /logout :

    - supprime la session en base.
