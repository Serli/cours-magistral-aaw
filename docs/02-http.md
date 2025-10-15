# Architecture des Applications Web  
## Partie II — Le protocole HTTP en profondeur  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre la structure des **messages HTTP** (requête/réponse).  
- Maîtriser **méthodes**, **statuts** et **en-têtes** clés.  
- Savoir configurer **cache**, **négociation de contenu**, **CORS**.  
- Situer **HTTP/1.1**, **HTTP/2** et **HTTP/3 (QUIC)**.  
- Pratiquer avec **curl** et les **DevTools** du navigateur.  

---

## Modèle de communication

- **Client → Serveur** via TCP (HTTP/1.1, HTTP/2) ou QUIC (HTTP/3).  
- **Stateless** : chaque requête porte le contexte nécessaire.  
- Ressource identifiée par une **URI** (schéma, host, path, query, fragment).  

---

## HTTP/1.x — fonctionnement général (séquentiel)

```
+--------------+                        +----------------+
|   Client     |                        |    Serveur     |
| (navigateur) |                        |  (web backend) |
+--------------+                        +----------------+
        |                                       |
        | 1️⃣ Connexion TCP (port 80/443).       |
        |-------------------------------------->|
        |                                       |
        | 2️⃣ Requête HTTP (texte)               |
        |   "GET /index.html HTTP/1.1".         |
        |   + headers + body                    |
        |-------------------------------------->|
        |                                       |
        | 3️⃣ Réponse HTTP                       |
        |   "HTTP/1.1 200 OK"                   |
        |   + headers + body                    |
        |<--------------------------------------|
        |                                       |
        | 4️⃣ Fermeture ou maintien (keep-alive) |
        |-------------------------------------->|
```

**Caractéristiques :**
- Une requête → une réponse.  
- Pipeline limité (1 requête à la fois par connexion TCP).  
- En-têtes en **texte brut**.  
- Connexions réutilisées via `Connection: keep-alive`.

---

## Message HTTP 1.X — Requête

```
GET /api/users?limit=10 HTTP/1.1
Host: example.com
Accept: application/json
User-Agent: curl/8.0
```

- **Ligne de requête** : `Méthode SP URI SP Version`.  
- **En-têtes** : `Nom: Valeur`.  
- **Corps** : optionnel (POST, PUT, PATCH).  

---

## Message HTTP 1.X — Réponse

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=60
ETag: "a1b2c3"

{"items":[...]}
```

- **Ligne d’état** : `Version SP Code SP Raison`.  
- **En-têtes** : métadonnées de la ressource.  
- **Corps** : représentation (HTML, JSON, binaire…).  

---

## Méthodes HTTP

- **GET** — lecture (sûre, idempotente).  
- **HEAD** — métadonnées sans corps.  
- **POST** — création / traitement non idempotent.  
- **PUT** — remplacement total (idempotente).  
- **PATCH** — modification partielle.  
- **DELETE** — suppression (idempotente).  
- **OPTIONS** — capacités, prévol CORS.  

---

## Codes de statut

| Famille | Exemples | Signification |
|----------|-----------|----------------|
| 2xx | 200 OK, 201 Created, 204 No Content | Succès |
| 3xx | 301 Moved Permanently, 304 Not Modified | Redirection / cache |
| 4xx | 400 Bad Request, 401 Unauthorized, 404 Not Found | Erreurs client |
| 5xx | 500 Internal Server Error, 503 Service Unavailable | Erreurs serveur |

---

## 📬 En-têtes essentiels (1/2)

| En-tête | Type | Description |
|----------|------|--------------|
| **Content-Type** | 📤 *Réponse / Requête* | Type MIME du contenu (`text/html`, `application/json`, etc.) |
| **Content-Length** | 📤 *Réponse / Requête* | Taille du corps (en octets). Absent si `Transfer-Encoding: chunked`. |
| **Content-Encoding** | 📤 *Réponse* | Méthode de compression (`gzip`, `br`, `deflate`). |
| **Accept** | 📥 *Requête* | Types MIME acceptés par le client. |
| **Accept-Language** | 📥 *Requête* | Langues préférées (`fr-FR`, `en-US`, ...). |
| **Accept-Encoding** | 📥 *Requête* | Méthodes de compression acceptées. |
| **Authorization** | 📥 *Requête* | Jeton d’accès (`Bearer <token>`, `Basic ...`). |
| **User-Agent** | 📥 *Requête* | Identifie le client (`Mozilla/5.0`, `curl/8.4.0`, etc.). |
| **Referer** | 📥 *Requête* | URL de la page d’origine (pour analytics, sécurité). |
| **Origin** | 📥 *Requête* | Origine de la requête (protocole + domaine + port), utilisée pour CORS. |

---

## 📬 En-têtes essentiels (2/2)

| En-tête | Type | Description |
|----------|------|--------------|
| **Cache-Control** | 📤 *Réponse* | Règles de cache (`no-store`, `max-age=60`, `s-maxage=300`). |
| **ETag** | 📤 *Réponse* | Identifiant unique de la version d’une ressource. |
| **If-None-Match** | 📥 *Requête* | Compare l’`ETag` pour valider le cache (→ `304 Not Modified`). |
| **Last-Modified** | 📤 *Réponse* | Date de dernière modification. |
| **If-Modified-Since** | 📥 *Requête* | Compare la date pour valider le cache (→ `304 Not Modified`). |
| **Location** | 📤 *Réponse* | Redirection (`302 Found`, `301 Moved`), ou ressource créée (`201 Created`). |
| **Set-Cookie** | 📤 *Réponse* | Définit un cookie côté client. |
| **Cookie** | 📥 *Requête* | Envoie les cookies vers le serveur. |


---

## 🧭 Négociation de contenu — principe général

Une **même ressource** peut avoir **plusieurs représentations** :  
- format (`application/json`, `application/xml`, `text/html`, etc.)  
- langue (`fr`, `en`, `es`...)  
- encodage (`gzip`, `br`, etc.)

👉 Le client exprime ses **préférences** avec des en-têtes `Accept-*`.  
👉 Le serveur choisit la **meilleure représentation** disponible.

```
Client                                              Serveur
───────                                             ────────
   │ GET /users/42                                     │
   │ Accept: application/json, application/xml;q=0.8.  │
   │ Accept-Language: fr, en;q=0.7                     │
   │ ────────────────────────────────────────────────▶ │
   │                                                   │ Choisit JSON (priorité 1.0)
   │ ◀──────────────────────────────────────────────── │
   │                   HTTP/1.1 200 OK                 │
   │                   Content-Type: application/json  │
   │                   Content-Language: fr            │
```

---

## 📦 Exemple — même ressource, deux formats possibles

### Requête du client
```
GET /users/42 HTTP/1.1
Host: api.example.com
Accept: application/json, application/xml;q=0.8
```

### Réponse possible (JSON)
```
HTTP/1.1 200 OK
Content-Type: application/json

{"id":42,"name":"Alice"}
```

### Réponse alternative (XML)
```
HTTP/1.1 200 OK
Content-Type: application/xml

<user><id>42</id><name>Alice</name></user>
```

➡️ Le serveur choisit selon la **préférence du client** (`q` = *quality factor*).  
➡️ `q=1.0` = priorité haute ; `q=0.8` = secondaire.

---

## ⚙️ Pondération des préférences (`q`)

### Exemple
```
Accept: text/html, application/json;q=0.9, */*;q=0.5
```

| Format demandé | Qualité (`q`) | Interprétation |
|----------------|----------------|----------------|
| `text/html` | 1.0 | préféré |
| `application/json` | 0.9 | accepté |
| `*/*` | 0.5 | fallback (tout format sinon) |

➡️ Si le serveur ne peut fournir **aucun format compatible** :  
`HTTP/1.1 406 Not Acceptable`

---

## 🌍 Négociation de langue (`Accept-Language`)

```
GET /page HTTP/1.1
Accept-Language: fr, en;q=0.8
```

| Ressource disponible | Résultat |
|----------------------|-----------|
| `/page.fr.html`      | ✅ Servie |
| `/page.en.html`      | fallback  |
| aucune dispo         | ❌ 406 Not Acceptable |

---

## 🧰 Caching HTTP — l’essentiel

- **Pourquoi ?** Réduire la latence et la charge serveur.
- **Où ?** Navigateur, proxy/CDN, reverse-proxy, application.
- **Deux approches :**
  - **Expiration** : on peut réutiliser sans demander au serveur (ex. `max-age`).
  - **Validation** : on demande “est-ce toujours frais ?” (ex. `ETag`, `If-None-Match`).

---

## 🧾 En-têtes clés (vue simple)

- **Cache-Control** (réponse) : règles d’expiration/partage  
  `max-age=60`, `s-maxage=300` (CDN), `public`/`private`, `no-store`, `no-cache`, `must-revalidate`
- **ETag** (réponse) : identifiant de version (hash) de la ressource
- **Last-Modified** (réponse) : date de dernière modification
- **If-None-Match** (requête) : “donne 304 si ETag identique”
- **If-Modified-Since** (requête) : “donne 304 si pas modifié depuis”
- **Age** (réponse) : âge (en s) de l’objet dans le cache intermédiaire

---

## ⏳ Expiration — exemple

Réponse cacheable pendant 10 minutes :

```
HTTP/1.1 200 OK
Cache-Control: public, max-age=600
Content-Type: text/css

/* styles.css ... */
```

➡️ Pendant 10 min, le navigateur/ CDN répond **sans** recontacter le serveur.

---

## 🔁 Validation avec ETag — séquence simple

### 1) Premier accès (pas de cache local)
```
Client                          Serveur
│ GET /users/42                ──▶ │
│                                  │ génère ETag "abc123"
│ ◀─────────────────────────────── │ HTTP/1.1 200 OK
│                                  │ ETag: "abc123"
│ (stocke la réponse + ETag)       │
```

### 2) Accès suivant (validation)
```
Client                          Serveur
│ GET /users/42               ──▶ │
│ If-None-Match: "abc123"         │ compare ETag
│ ◀────────────────────────────── │ HTTP/1.1 304 Not Modified
│                                 │ (pas de corps)
```

➡️ **304** = réutiliser la copie locale (économie de bande passante).

---

## 💡 ETag vs Last-Modified

- **ETag** : précis, change au moindre octet → idéal pour APIs/JSON.
- **Last-Modified** : simple, mais granularity = 1 seconde et dépend de l’horloge.

---

## ✍️ Contrôle de concurrence (If-Match) — éviter d’écraser des modifs

### Cas A — ETag à jour ⇒ mise à jour acceptée
```
Client                          Serveur
│ GET /users/42               ──▶ │
│ ◀────────────────────────────── │ 200 OK
│ (reçoit ETag "abc123")          │

│ PUT /users/42               ──▶ │
│ If-Match: "abc123"              │ ETag stocké = "abc123" → OK
│ { "name": "Alice X" }           │ met à jour, nouveau ETag "def456"
│ ◀────────────────────────────── │ 200 OK
│                                 │ ETag: "def456"
```

### Cas B — ETag périmé ⇒ rejet (prévention d’écrasement)
```
Client                          Serveur
│ PUT /users/42               ──▶ │
│ If-Match: "abc123"              │ ETag stocké = "def456" → conflit
│ { "name": "Alice Y" }           │
│ ◀────────────────────────────── │ 412 Precondition Failed
```

➡️ `If-Match` = “ne mets à jour **que si** la version n’a pas changé”.

---

## Sécurité du transport — HTTPS / TLS

- **HTTPS = TLS + HTTP** : confidentialité, intégrité, authenticité.  
- **HSTS** (`Strict-Transport-Security`) pour forcer HTTPS.  
- Certificats, chaînes de confiance, renouvellement (ACME / Let’s Encrypt).  

_(Cette partie sera détaillée plus tard)_

---

## Cookies, sessions, tokens

- `Set-Cookie` : options `Secure`, `HttpOnly`, `SameSite` (Lax / Strict / None).  
- Sessions côté serveur : ID stocké en cookie.  
- **JWT** portés côté client (`Authorization: Bearer ...`).  

_(Cette partie sera détaillée plus tard)_

---

## 🌐 CORS — Cross-Origin Resource Sharing

- Définit qui peut appeler une ressource depuis un autre domaine.  
- **Simple request** ou **preflight (OPTIONS)**.  
- Réponses serveur :  
  - `Access-Control-Allow-Origin`  
  - `Access-Control-Allow-Methods`  
  - `Access-Control-Allow-Headers`  
  - `Access-Control-Allow-Credentials`

---

## 🌐 CORS — principe général

CORS permet à un navigateur de **demander une ressource depuis un autre domaine**,  
tout en respectant la **politique de même origine** (*Same-Origin Policy*).

```
Origine = schéma + domaine + port

http://example.com      ≠     https://example.com
http://app.local:3000   ≠     http://api.local:8080
```

➡️ Sans CORS, les requêtes inter-origines sont **bloquées par le navigateur**.  
➡️ Avec CORS, le **serveur distant autorise explicitement** certains domaines.

---

## 🔁 CORS — requête simple

```
Client (frontend)
Origin: https://app.example.com
        │
        │ GET /data
        ▼
+-------------------------------+
|  Serveur API (api.example.com)|
+-------------------------------+
        │
        │ Access-Control-Allow-Origin: https://app.example.com
        │ Access-Control-Allow-Methods: GET
        ▼
Réponse acceptée ✅
```

**Simple request :**  
- Méthode = `GET`, `HEAD` ou `POST` (avec `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`).  
- Pas de pré-vérification `OPTIONS`.  
- Le navigateur laisse passer si le header `Access-Control-Allow-Origin` correspond à l’origine de la page.

---

## 🧭 CORS — pré-vérification (preflight)

Pour les requêtes **non simples** (méthodes PUT, DELETE, JSON custom, headers spéciaux) :

```
Client (navigateur)
Origin: https://app.example.com
        │
        │ ──▶ OPTIONS /users/42
        │      Access-Control-Request-Method: PUT
        │      Access-Control-Request-Headers: Content-Type, Authorization
        │
        ▼
+-------------------------------+
| Serveur API (api.example.com) |
+-------------------------------+
        │
        │ ◀── 200 OK
        │     Access-Control-Allow-Origin: https://app.example.com
        │     Access-Control-Allow-Methods: GET, POST, PUT
        │     Access-Control-Allow-Headers: Content-Type, Authorization
        │     Access-Control-Max-Age: 600
        ▼
Navigateur envoie ensuite la vraie requête PUT ✅
```

---

## 🧱 Résumé — rôles navigateur et serveur

| Étape | Acteur | Action |
|-------|---------|--------|
| 1️⃣ | Navigateur | Vérifie les en-têtes et les origines |
| 2️⃣ | Serveur | Répond avec `Access-Control-Allow-*` |
| 3️⃣ | Navigateur | Autorise ou bloque la requête |
| 4️⃣ | (Optionnel) | `Access-Control-Max-Age` pour éviter plusieurs preflights |

**Important :**
- CORS est **appliqué côté navigateur**, jamais côté serveur.  
- Les outils comme `curl` ou Postman **ne sont pas concernés**.

---

## HTTP/2 — connexion unique multiplexée

```
+--------------+                           +----------------+
|   Client     |                           |     Serveur    |
| (navigateur) |                           |     HTTP/2     |
+--------------+                           +----------------+
        |                                         |
        | 🔒 Connexion unique (TCP + TLS)         |
        |---------------------------------------->|
        |                                         |
        | 📤 Streams multiples :                  |
        |    - Stream 1: GET /index.html          |
        |    - Stream 2: GET /style.css           |
        |    - Stream 3: GET /script.js           |
        |---------------------------------------->|
        |                                         |
        | 📥 Réponses parallèles :                |
        |    - Stream 1: HTML                     |
        |    - Stream 2: CSS                      |
        |    - Stream 3: JS                       |
        |<----------------------------------------|
        |                                         |
        | Connexion persistante (binaire + HPACK) |
```

**Caractéristiques :**
- Une seule connexion TCP multiplexée.  
- **Frames binaires** et **compression HPACK**.  
- Réponses parallèles → meilleures performances.  
- Possibilité de **Server Push** (envoi proactif de ressources).

---

## Message HTTP/2 — Concepts clés

- Les messages HTTP/2 ne sont **plus textuels** mais **binaires**.  
- Les requêtes et réponses sont découpées en **frames** (trames).  
- Plusieurs flux peuvent coexister sur **une seule connexion TCP**.  

**Types de frames les plus courants :**
| Type | Description |
|------|--------------|
| `HEADERS` | Contient les en-têtes compressés (HPACK) |
| `DATA` | Contient le corps du message |
| `SETTINGS` | Négociation entre client et serveur |
| `RST_STREAM` | Annule un flux en cours |
| `PING` | Vérifie la latence de la connexion |

---

## Exemple — Requête et réponse HTTP/2 (vue logique)

### Requête (stream 1)
```
:method: GET
:scheme: https
:authority: api.example.com
:path: /users?limit=10
accept: application/json
user-agent: curl/8.4.0
```

➡️ Les pseudo-en-têtes `:` remplacent la ligne de requête.  
➡️ Envoi via une **frame HEADERS**, suivie éventuellement de **DATA**.

---

### Réponse (stream 1)
```
:status: 200
content-type: application/json
cache-control: max-age=60
etag: "xyz123"

{"items":[...]} 
```

➡️ Les métadonnées arrivent dans une **frame HEADERS**.  
➡️ Le corps JSON est fragmenté en une ou plusieurs **frames DATA**.  
➡️ Plusieurs réponses (streams) peuvent voyager simultanément sur une seule connexion.

---

## 🔀 Multiplexage HTTP/2 — Illustration

### Une seule connexion TCP, plusieurs flux simultanés
```
Connexion TCP unique
───────────────────────────────────────────────────────
| Stream 1 |███ HEADERS ███ DATA ██████████           |
| Stream 2 |     █ HEADERS ███ DATA ██████████        |
| Stream 3 |          █ HEADERS ███ DATA ██████       |
───────────────────────────────────────────────────────
```

- Chaque flux (`stream`) transporte une requête/réponse indépendante.  
- Les trames sont intercalées dans le flux binaire global.  
- Le protocole conserve l’ordre logique de chaque flux côté client/serveur.  
- Résultat : **moins de latence** et **une seule connexion TLS** partagée.

---

## Message HTTP/3 — Concepts clés

- Basé sur le protocole **QUIC**, lui-même construit sur **UDP**.  
- Reprend la sémantique de HTTP/2 (frames, multiplexage, pseudo-en-têtes).  
- Supprime les blocages liés à TCP (*head-of-line blocking*).  
- Intègre directement **TLS 1.3** dans QUIC (pas de handshake séparé).  

**Caractéristiques principales :**
| Élément | Description |
|----------|--------------|
| ✅ Multiplexage natif | Flux indépendants, sans interblocage |
| 🔒 Sécurité intégrée | TLS 1.3 embarqué dans QUIC |
| 🚀 Démarrage rapide | Connexion en 1 RTT (ou 0 RTT en reprise) |
| 🌍 Mobilité | Connexion conservée même si l’adresse IP change (roaming) |

---

## Exemple — Requête et réponse HTTP/3 (vue logique)

### Requête (stream 1)
```
:method: GET
:scheme: https
:authority: api.example.com
:path: /users?limit=10
accept: application/json
user-agent: curl/8.4.0 (HTTP/3)
```

➡️ Même structure logique que HTTP/2, mais envoyée via **QUIC stream**.  
➡️ Pas de TCP handshake — cryptage et transport combinés.  

---

### Réponse (stream 1)
```
:status: 200
content-type: application/json
cache-control: max-age=60
etag: "qwe789"

{"items":[...]}
```

➡️ Chaque flux HTTP/3 voyage dans un **flux QUIC indépendant**.  
➡️ Si un flux subit une perte de paquet, les autres continuent sans attente.  
➡️ Le protocole gère la retransmission et le chiffrement au niveau QUIC.  

---

## HTTP/1.1 vs HTTP/2 vs HTTP/3

| Version | Transport | Caractéristiques clés |
|----------|------------|------------------------|
| HTTP/1.1 | TCP | Keep-alive, 1 flux / connexion |
| HTTP/2 | TCP | Multiplexage, HPACK, binaire |
| HTTP/3 | QUIC (UDP) | TLS intégré, démarrage rapide, résilient |

→ Gains majeurs en **latence** et **performances mobiles**.  

---

## Outils — curl

```bash
# Requête GET
curl -i -H "Accept: application/json" https://httpbin.org/get

# POST JSON
curl -i -X POST -H "Content-Type: application/json"      -d '{"title":"Hello"}' https://httpbin.org/post

# Test CORS (prévol)
curl -i -X OPTIONS   -H "Origin: https://example.org"   -H "Access-Control-Request-Method: POST"   https://api.example.com/resource
```

---

## Outils — DevTools navigateur

- Onglet **Network** : en-têtes, timings, cache.  
- Option **Disable cache** pour tester les politiques.  
- **Throttling** : simuler 3G / 4G, latence réseau.  

---

## Bonnes pratiques HTTP

- Définir des **contrats d’API** clairs (types, statuts, erreurs).  
- Utiliser **ETag** et **Cache-Control** pour réduire la latence.  
- Sécuriser **TLS** et **cookies** (`Secure`, `HttpOnly`, `SameSite`).  
- Mettre en place **CORS** explicitement.  
- Observer : logs structurés, corrélation, métriques.  

---

## Partie suivante

### Partie III — Les standards du Web
- Comprendre le rôle fondamental de HTML, CSS et JavaScript dans le Web.
- Manipuler la structure du DOM et les interactions client.
- Identifier le cycle de vie d’une page web.
- Poser les bases du développement front-end moderne.
