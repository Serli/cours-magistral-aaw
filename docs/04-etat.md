# Architecture des Applications Web  
## Partie IV — Gestion de l’état & communication client-serveur  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre **statelessness** HTTP et les approches de **gestion d’état**.  
- Mettre en œuvre **sessions**, **cookies**, **tokens** (JWT, OAuth2, etc.).  
- Découvrir les **flux modernes** (PKCE, OIDC).  
- Connaître les **stockages côté client** et leurs implications.  
- Sécuriser les échanges : **CSRF**, **XSS**, **SameSite**, **CORS**.  

---

## HTTP est sans état (stateless)

- Chaque requête est **indépendante** : le serveur ne garde pas de mémoire du client.  
- L’état doit être reconstruit à chaque requête, via :
  - **Identifiants** (session ID, token, cookie),
  - **Stockage client** (cookies, localStorage, etc.),
  - **Back-end** (BD, cache, services).  

---

## 🍪 Cookies, sessions, tokens — principes généraux

Les applications web doivent **mémoriser qui est connecté** après authentification.

Trois grands modèles :

| Modèle | Stockage | Vérification | Exemple |
|---------|-----------|---------------|----------|
| **Session** | Serveur (base, mémoire) | ID stocké dans un cookie | PHP, Django, Express-session |
| **Token opaque** | Base côté serveur | Jeton transmis dans l’en-tête | OAuth2 bearer token |
| **JWT (JSON Web Token)** | Côté client (auto-portant) | Signature vérifiée localement | Auth0, Keycloak, Cognito |

---

## 🔐 Sessions côté serveur

### Principe
- Le serveur crée une **entrée de session** (ex. `{id: abc123, user: 42}`)  
- Le client reçoit un **cookie** (`Set-Cookie: SESSIONID=abc123; HttpOnly; Secure`)

```
Client                     Serveur
│ POST /login ──────────────▶ │ crée session {id:abc123, user:42}
│ ◀────────────────────────── │ Set-Cookie: SESSIONID=abc123
│                             │
│ GET /profile ─────────────▶ │ retrouve session[abc123]
│ Cookie: SESSIONID=abc123    │
│ ◀────────────────────────── │ 200 OK
```

**Avantages :** simple, révocable.  
**Inconvénients :** nécessite un **store centralisé** (Redis, DB) pour le scaling.

---

## 🪙 Tokens opaques (Bearer tokens)

- Jeton aléatoire **non décodable** transmis dans l’en-tête :  
  `Authorization: Bearer abc123xyz`  
- Serveur valide le jeton dans une base (`active_tokens`).  
- Revocation immédiate possible.

**Utilisé par :** OAuth2, APIs, communications inter-services.

**Avantages :** indépendant des cookies, compatible mobile/API.  
**Limites :** stockage serveur toujours nécessaire.

---

## 🔏 JSON Web Token (JWT)

### Structure
```
xxxxx.yyyyy.zzzzz
```

| Partie | Contenu | Format |
|---------|----------|---------|
| Header | algorithme, type | JSON (Base64) |
| Payload | données (claims) | JSON (Base64) |
| Signature | vérification d’intégrité | HMAC / RSA |

Exemple :
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.eyJ1c2VyX2lkIjoxMjMsImV4cCI6MTcyMDAwMDAwMH0
.t9v_dCtfUR2M2rxO_hEIzVvOQ1B4oUZtME_HzN8YF2E
```

**Caractéristiques :**
- Auto-portant → pas besoin de base pour valider.  
- Vérifié par signature (`HS256`, `RS256`).  
- ⚠️ Revocation difficile → prévoir **durée courte + refresh token**.  

---

## 🧠 Comparatif rapide

| Critère | Session | Token opaque | JWT |
|----------|----------|---------------|------|
| Stock côté serveur | ✅ Oui | ✅ Oui | ❌ Non |
| Révocable instantanément | ✅ Oui | ✅ Oui | ⚠️ Non |
| Scalabilité | ⚠️ moyenne | ✅ bonne | ✅ excellente |
| Sécurité (vol de jeton) | dépend du cookie | dépend du header | idem |
| Idéal pour | applis web classiques | APIs OAuth | SPA/mobile |

---

## 🧩 Bonnes pratiques

### Côté client
- Cookies : `Secure`, `HttpOnly`, `SameSite=Lax/Strict`.  
- Éviter `localStorage` pour les tokens (vulnérable XSS).  
- Préférer `sessionStorage` ou mémoire pour l’access token.  

### Côté serveur
- JWT courts (quelques minutes) + refresh token sécurisé.  
- Vérifier `Origin` / `Referer` pour requêtes cross-site.  
- Limiter les **scopes** (`aud`, `iss`, `exp`, `nbf`).  

---

## 🔑 OAuth 2.1 — délégation d’accès

- Permet à un service tiers d’accéder à une ressource **au nom d’un utilisateur**,  
  sans lui donner directement ses identifiants.

**Exemple :**
> "Autoriser Google Drive à accéder à vos photos Facebook."

---

### Principe général

- 1️⃣ L’utilisateur s’authentifie sur le **serveur d’autorisation (Authorization Server)**.  
- 2️⃣ Il **donne son consentement** à l’application tierce (client).  
- 3️⃣ L’application reçoit un **access token**.  
- 4️⃣ Ce token est utilisé pour accéder à l’API du fournisseur.

```
+-------------+        +------------------+        +---------------------+
| Application | -----> |  Authorization   | -----> | API de la ressource |
|  (Client)   |        |    Server        |        |  (Resource Server)  |
+-------------+        +------------------+        +---------------------+
       │                       ▲                           ▲
       └── access token ───────┘                           │
             utilisé pour appeler l’API -------------------┘
```

---

## 👤 OpenID Connect — authentification basée sur OAuth2

- **OIDC** est une **extension d’OAuth2** utilisée pour *savoir qui est l’utilisateur*.  
- En plus du `access_token`, le serveur renvoie un **ID Token** (souvent un JWT).  
- Ce `id_token` contient les informations d’identité (nom, email, etc.).

**Exemple d’usage :**
> "Se connecter avec Google" ou "Login via GitHub"

```
Client (votre app)       Authorization Server            API
      │                          │                        │
      │---- Auth user ---------->│                        │
      │<--- id_token, access ----│                        │
      │---- appelle API -------->│---- vérifie token ---->│
```

---

## 💡 Exemple concret — "Se connecter avec Google"

- 1️⃣ Votre application redirige l’utilisateur vers Google pour s’authentifier.  
- 2️⃣ L’utilisateur se connecte (Google vérifie son mot de passe).  
- 3️⃣ Google redirige votre app avec :  
   - un **access_token** pour accéder aux API Google,  
   - un **id_token** (JWT) contenant le profil utilisateur.  
- 4️⃣ Votre application lit le `id_token` et affiche le nom/email.

**Contenu du ID Token (exemple JSON)**

```json
{
  "iss": "https://accounts.google.com",
  "sub": "1234567890",
  "email": "user@example.com",
  "name": "Alice Dupont",
  "aud": "your-app-id",
  "exp": 1720000000
}
```

---

## 🧩 En résumé

| Concept | Rôle | Type de jeton |
|----------|------|---------------|
| **OAuth 2.1** | Délégation d’accès (autorisation) | `access_token` |
| **OpenID Connect (OIDC)** | Authentification (qui est l’utilisateur) | `id_token` |

**À retenir :**
- OAuth = *"je donne accès"*,  
- OIDC = *"je m’identifie"*.

---

## Partie suivante

### Partie V — Architectures côté serveur
- Serveurs web et frameworks back-end  
- Accès aux données : SQL/NoSQL, ORM, cache  
- Sécurité et exposition d’API  
