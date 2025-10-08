# Architecture des Applications Web  
## Partie IV — Gestion de l’état & communication client-serveur  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre **statelessness** HTTP et les approches de **gestion d’état**.  
- Mettre en œuvre **sessions**, **cookies**, **tokens** (JWT).  
- Connaître **OAuth2 / OpenID Connect** et les flux modernes (PKCE).  
- Stocker côté client : **localStorage**, **sessionStorage**, **IndexedDB**.  
- Sécuriser les échanges : **CSRF**, **XSS**, **SameSite**, CORS.  

---

## HTTP est sans état (stateless)

- Chaque requête est **indépendante** : le serveur ne garde pas le contexte.  
- L’état applicatif est reconstruit via :  
  - **Identifiants** (session ID, token),  
  - **Stockage client** (cookies, storage),  
  - **Back-end** (base de données, cache).  

---

## Cookies — bases

- Créés par le serveur via `Set-Cookie`.  
- Propriétés : `Secure`, `HttpOnly`, `SameSite` (`Lax` par défaut recommandé), `Domain`, `Path`, `Expires/Max-Age`.  
- Usages : **sessions**, préférences, A/B testing.  
- ⚠️ **Ne pas** stocker d’infos sensibles en clair.  

---

## Sessions côté serveur

- Le serveur stocke l’état (ex. utilisateur) et remet un **session ID** au client (cookie).  
- Avantages : révocation simple, contrôle fin.  
- Inconvénients : **scalabilité** (nécessite un **store partagé** : Redis) et/ou **sticky sessions** au load balancer.  

```text
Client <—cookie sid—> LB → App (stateless) → Redis (session store)
```

---

## Tokens portés par le client (JWT)

- **JWT** (JSON Web Token) : `header.payload.signature` (signé, non chiffré).  
- Transport typique : en-tête `Authorization: Bearer <token>`.  
- Avantages : **stateless** côté serveur, pas de store session.  
- Points d’attention :  
  - **Révocation** difficile → prévoir **courte durée** + **refresh token**.  
  - **Taille** du token (impact sur headers).  
  - Pas de données sensibles dans le **payload**.  

---

## Où stocker le token côté client ?

- **Cookie** `Secure; HttpOnly; SameSite=Lax/Strict` → protège contre XSS, limite CSRF selon SameSite.  
- **localStorage / sessionStorage** → simple mais **vulnérable XSS**.  
- Bon compromis : **cookie HttpOnly** pour le refresh, **mémoire** pour l’access token.  

---

## CSRF & SameSite

- **CSRF** : action malveillante déclenchée depuis un autre site.  
- Protections :  
  - Cookies `SameSite=Lax` (par défaut) ou `Strict`.  
  - **Anti-CSRF token** synchronisé (double-submit / synchronizer token).  
  - Méthodes non idempotentes **protégées** (POST/PUT/DELETE).  

---

## XSS & en-têtes utiles

- **XSS** : injection de script exécuté dans le navigateur.  
- Mesures : échapper la sortie, CSP (Content Security Policy), `HttpOnly`, validation stricte.  
- En-têtes utiles :  
  - `Content-Security-Policy`  
  - `X-Content-Type-Options: nosniff`  
  - `Referrer-Policy`  
  - `Strict-Transport-Security` (HSTS)  

---

## OAuth 2.1 & OpenID Connect (OIDC)

- **OAuth2** : délégation d’accès (autorisations).  
- **OIDC** : authentification **fédérée** (ID Token).  
- **PKCE** (Proof Key for Code Exchange) : sécurise les apps publiques (SPA/mobile).  

**Flux conseillé pour SPA :** Authorization Code + **PKCE** (pas d’implicite).  

---

## Cycle d’authentification (SPA moderne)

1. Redirection vers l’**Authorization Server** (avec **code_challenge** PKCE).  
2. Retour avec **code** → échange contre **access token** (+ **refresh**).  
3. Appelle l’API avec `Authorization: Bearer <access_token>`.  
4. **Renouvellement** via refresh token (en **cookie HttpOnly**).  
5. **Logout** → révocation côté AS + purge des cookies / mémoire.  

---

## Stockage côté client

| Stockage | Portée | Persistance | Sécurité |
|---------|--------|-------------|---------|
| **Cookies** | Par domaine | Jusqu’à expiration | `HttpOnly`, `Secure`, `SameSite` |
| **sessionStorage** | Onglet | Jusqu’à fermeture onglet | Exposé au JS |
| **localStorage** | Domaine | Persistant | Exposé au JS |
| **IndexedDB** | Domaine | Persistant (base clé/valeur) | Exposé au JS |

→ Règle d’or : **ne jamais** stocker de secrets longs terme dans le storage exposé au JS.  

---

## CORS — rappel rapide

- Autorise les appels **cross-origin** côté navigateur.  
- **Preflight (OPTIONS)** si en-têtes/méthodes non simples.  
- Le **serveur** contrôle : `Access-Control-Allow-Origin/Methods/Headers/Credentials`.  
- ⚠️ `Access-Control-Allow-Credentials: true` **interdit** `*` sur `Allow-Origin`.  

---

## Cache & authentification

- Ne **jamais** mettre en cache des réponses privées sur des caches partagés.  
- Utiliser `Cache-Control: no-store` pour ressources sensibles.  
- Pour les API : `Cache-Control: private, no-store` ou `no-cache` + ETag selon besoin.  

---

## Exemple — Login avec session (Express)

```js
import express from "express";
import session from "express-session";
import RedisStore from "connect-redis";

const app = express();
app.use(express.json());
app.use(session({
  store: new RedisStore({ /* client */ }),
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: { secure: true, httpOnly: true, sameSite: "lax" }
}));

app.post("/login", async (req, res) => {
  // Vérifier les identifiants...
  req.session.userId = "123";
  res.status(204).end();
});

app.get("/me", (req, res) => {
  if (!req.session.userId) return res.status(401).end();
  res.json({ id: req.session.userId });
});
```

---

## Exemple — API protégée par JWT (middleware)

```js
import jwt from "jsonwebtoken";

function auth(req, res, next) {
  const authz = req.headers.authorization || "";
  const token = authz.startsWith("Bearer ") ? authz.slice(7) : null;
  if (!token) return res.status(401).json({ error: "missing token" });
  try {
    req.user = jwt.verify(token, process.env.JWT_PUBLIC_KEY, { algorithms: ["RS256"] });
    next();
  } catch {
    res.status(401).json({ error: "invalid token" });
  }
}
```

---

## Bonnes pratiques (récap)

- Préférer **Authorization Code + PKCE** pour SPA / mobile.  
- **Cookies HttpOnly** pour refresh, **access token** en mémoire.  
- **SameSite=Lax** par défaut, **Strict** si possible.  
- Protéger contre **XSS/CSRF** (CSP, tokens anti-CSRF).  
- Sessions **centralisées** (Redis) en environnement distribué.  
- Journaux d’audit : connexions, révocations, erreurs d’auth.  

---

## Partie suivante

### Partie V — Architectures côté serveur
- Serveurs web et frameworks back-end.  
- Accès aux données : SQL/NoSQL, ORM, cache.  
- Sécurité et exposition d’API.  
