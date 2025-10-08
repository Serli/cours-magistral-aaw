# Architecture des Applications Web
## Partie II — HTTP en profondeur

---

## Objectifs de la séance
- Comprendre la structure des **messages HTTP** (requête/réponse)
- Maîtriser **méthodes**, **statuts** et **en-têtes** clés
- Savoir configurer **cache**, **négociation de contenu**, **CORS**
- Situer **HTTP/1.1**, **HTTP/2** et **HTTP/3 (QUIC)**
- Pratiquer avec **curl** et les **DevTools** du navigateur

---

## Modèle de communication
- **Client → Serveur** via TCP (HTTP/1.1, HTTP/2) ou QUIC (HTTP/3)
- **Stateless** : chaque requête porte le contexte nécessaire
- Ressource identifiée par une **URI** (schéma, host, path, query, fragment)

---

## Message HTTP — Requête
```
GET /api/users?limit=10 HTTP/1.1
Host: example.com
Accept: application/json
User-Agent: curl/8.0
```

- **Ligne de requête** : `Méthode SP URI SP Version`
- **En-têtes** : `Nom: Valeur`
- **Corps** : optionnel (POST/PUT/PATCH)

---

## Message HTTP — Réponse
```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=60
ETag: "a1b2c3"

{"items":[...]}
```

- **Ligne d’état** : `Version SP Code SP Raison`
- **En-têtes** : métadonnées de la ressource
- **Corps** : représentation (HTML, JSON, binaire…)

---

## Méthodes HTTP (principales)
- **GET** (sûre, idempotente) — lecture
- **HEAD** — métadonnées sans corps
- **POST** — création/traitement non idempotent
- **PUT** (idempotente) — remplacement total
- **PATCH** — modification partielle
- **DELETE** (idempotente) — suppression
- **OPTIONS** — capacités, prévol CORS

---

## Codes de statut (exemples)
- **2xx** : 200 OK, 201 Created, 204 No Content
- **3xx** : 301 Moved Permanently, 302 Found, 304 Not Modified
- **4xx** : 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests
- **5xx** : 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable

---

## En-têtes essentiels (1/2)
- **Content-Type**, **Content-Length**, **Content-Encoding**
- **Accept**, **Accept-Language**, **Accept-Encoding**
- **Authorization**, **WWW-Authenticate**
- **User-Agent**, **Referer**, **Origin**

---

## En-têtes essentiels (2/2)
- **Cache-Control** (no-store, no-cache, max-age, s-maxage)
- **ETag** / **If-None-Match**
- **Last-Modified** / **If-Modified-Since**
- **Vary** (Accept, Accept-Language, Origin)
- **Location** (redirections, 201 Created)

---

## Négociation de contenu
- **Serveur** choisit la meilleure représentation selon `Accept`, `Accept-Language`, etc.
- Réponses : `200 OK` (représentation choisie) ou `406 Not Acceptable`
- Bonnes pratiques : fixer des **valeurs par défaut** claires

---

## Caching HTTP — Bases
- **Cache-Control** : `max-age`, `public`, `private`, `no-store`
- **Validation** : `ETag` + `If-None-Match` → `304 Not Modified`
- **Heuristique** si date d’expiration absente (via `Last-Modified`)
- **CDN** : `s-maxage`, `stale-while-revalidate`, `stale-if-error`

---

## Caching — Exemple
```
Cache-Control: public, max-age=300, stale-while-revalidate=30
ETag: "v42"
```
Client peut réutiliser pendant 5 min, puis **revalider**.  
En cas d’erreur en amont, le cache peut servir une version **stale**.

---

## Conditionnels & Concurrence
- **Préconditions** : `If-Match` / `If-Unmodified-Since`
- Empêchent l’écrasement concurrent (renvoient **412 Precondition Failed**)
- Pattern : **lecture** ETag → **écriture** avec `If-Match`

---

## Sécurité transport — TLS
- **HTTPS** = HTTP + **TLS** (confidentialité, intégrité, authenticité)
- HSTS (`Strict-Transport-Security`) pour forcer HTTPS
- Certificats, chaînes de confiance, renouvellement automatique (ACME)

---

## Cookies, sessions, tokens
- `Set-Cookie`: `Secure`, `HttpOnly`, `SameSite` (Lax/Strict/None)
- Session côté serveur (identifiant stocké en cookie)
- **JWT** portés côté client (Authorization: Bearer ...) — attention à la révocation

---

## CORS — Cross-Origin Resource Sharing
- **Simple requests** vs **preflight** (OPTIONS)
- Réponses serveur : `Access-Control-Allow-Origin`, `-Methods`, `-Headers`, `-Credentials`
- Principe : le **serveur** décide qui peut appeler depuis quel **origin**

---

## HTTP/1.1 vs HTTP/2 vs HTTP/3
- **HTTP/1.1** : keep-alive, pipelining (peu utilisé), 1 flux/connexion
- **HTTP/2** : multiplexage, HPACK, priorité, binaire
- **HTTP/3 (QUIC)** : UDP + TLS intégré, démarrage plus rapide, résilience aux pertes
- Impact : **latence** réduite, meilleures performances mobiles

---

## Observabilité et limites de taux
- **Trace-Id** corrélé (logs, APM)
- **Retry-After**, **429 Too Many Requests**
- Idempotence côté client pour **retries** sûrs

---

## Outils — curl
```bash
# Requête GET avec en-tête personnalisé
curl -i -H "Accept: application/json" https://httpbin.org/get

# POST JSON
curl -i -X POST -H "Content-Type: application/json" \     -d '{"title":"Hello"}' https://httpbin.org/post

# Test CORS (prévol)
curl -i -X OPTIONS \  -H "Origin: https://example.org" \  -H "Access-Control-Request-Method: POST" \  https://api.example.com/resource
```

---

## Outils — DevTools navigateur
- Onglet **Network** : requêtes, en-têtes, timings (TTFB, content download)
- **Disable cache** pour tester les directives cache
- **Throttling** pour simuler 3G/4G, latence

---

## Bonnes pratiques récap
- Définir des **contrats d’API** clairs (types, statuts, erreurs)
- Utiliser **ETag** et **Cache-Control** pour réduire la latence
- Sécuriser **TLS** et **cookies** (Secure, HttpOnly, SameSite)
- Mettre en place **CORS** explicitement (origins autorisés)
- Observer : logs structurés, corrélation, métriques

---

## Pour la prochaine fois
- Mettre en pratique : mini-API + règles de cache + CORS contrôlé
- Transition vers la **Partie III — Architectures côté serveur**
