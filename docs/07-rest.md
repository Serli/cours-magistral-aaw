# Architecture des Applications Web  
## Partie VII — Les APIs REST  
**Master Informatique**

---

## Objectifs de la partie

- Concevoir et documenter une **API RESTful**.  
- Modéliser des **ressources** et des **relations**.  
- Maîtriser **versionnement**, **pagination**, **filtrage**, **cache**.  
- Standardiser les **erreurs** et les **contrats** (OpenAPI).  

---

## Principes REST (rappels)

- **Ressources** identifiées par des **URI** (`/users/42`).  
- **Représentations** (JSON) transférées via **HTTP**.  
- **Méthodes** sémantiques : GET/POST/PUT/PATCH/DELETE.  
- **Stateless** : chaque requête est autonome.  
- **Cacheable** : directives HTTP (`Cache-Control`, `ETag`).  
- **HATEOAS** (optionnel en pratique) : liens de navigation.  

---

## Modéliser les ressources

- Pluriel pour les collections : `/users`, `/orders`.  
- Identifiant stable pour l’item : `/users/{id}`.  
- Relations :  
  - **Imbriquées** : `/users/{id}/orders`  
  - **Liens** : champ `links` dans la réponse.  

```json
{
  "id": 42,
  "name": "Alice",
  "links": [{ "rel": "orders", "href": "/users/42/orders" }]
}
```

---

## Méthodes & idempotence

- **GET** (sûr, idempotent) → lecture.  
- **POST** (non idempotent) → création/traitement.  
- **PUT** (idempotent) → remplacement complet.  
- **PATCH** (partiel) → modification ciblée.  
- **DELETE** (idempotent) → suppression.  

**Idempotence** : répéter l’appel ne change pas le résultat final.  

---

## Nommage & conventions d’URL

- Noms **nouns**, pas de verbes : `/invoices` pas `/getInvoices`.  
- Hiérarchie claire : `/shops/{id}/products`.  
- **Filtrage** : `/products?category=books&minPrice=10`.  
- **Tri** : `/products?sort=-createdAt,name`.  
- **Pagination** : `/products?page=3&pageSize=20` **ou** cursors (`?cursor=...`).  

---

## Requêtes : champs & expansions

- **Projection** (champs partiels) : `?fields=id,name,price`.  
- **Expansion** (relations) : `?expand=items,customer`.  
- Avantage : **limiter** la taille des réponses → meilleures perfs.  

---

## Codes de statut — mapping recommandé

| Opération | Succès | Cas fréquents |
|-----------|--------|---------------|
| GET /resources | **200** | 404 si non trouvé |
| POST /resources | **201** + `Location` | 400/422 si invalid |
| PUT /resource/{id} | **200** ou **204** | 404, 409 (conflit) |
| PATCH /resource/{id} | **200** | 400, 404, 409 |
| DELETE /resource/{id} | **204** | 404 |

---

## Représentations & média types

- Par défaut **JSON** (`application/json`).  
- **JSON Lines** pour streaming, **CSV** pour export.  
- Versionnement via **media type** (optionnel) : `application/vnd.api+json;version=2`.  

---

## Validation & erreurs (RFC 7807)

- Format **Problem Details** (`application/problem+json`).  

```json
{
  "type": "https://errors.example.com/validation",
  "title": "Unprocessable Entity",
  "status": 422,
  "detail": "email est invalide",
  "instance": "/users",
  "errors": { "email": ["format invalide"] }
}
```

- Avantage : structure **uniforme** des erreurs.  

---

## Pagination : offset vs cursor

- **Offset/limit** : simple, mais coûteux sur gros volumes.  
- **Cursor** : robuste (stable), performant.  

```json
{
  "items": [...],
  "next": "/products?cursor=eyJpZCI6MTIzfQ"
}
```

---

## Concurrence & ETags

- Réponses avec **ETag**.  
- Mise à jour conditionnelle avec `If-Match`.  

```http
PUT /users/42
If-Match: "v3"
Content-Type: application/json
```

→ Si l’ETag ne correspond pas : **412 Precondition Failed**.  

---

## Cache HTTP (rappels)

- Caches **privés** (navigateur) et **partagés** (CDN).  
- `Cache-Control: public, max-age=300, s-maxage=600`.  
- Revalidation : `ETag` + `If-None-Match` → **304 Not Modified**.  

---

## Sécurité minimale d’une API REST

- **TLS** partout.  
- **AuthN/AuthZ** : OAuth2 / OIDC, scopes.  
- **Rate limiting** (ex. 429 + `Retry-After`).  
- **CORS** explicite si front séparé.  
- **Logs** d’audit : accès, erreurs, tentatives.  

---

## Versionnement d’API

- Stratégies :  
  - **URI** : `/v1/...` (simple, explicite)  
  - **Header** : `Accept: application/vnd.company.v2+json`  
  - **Ressource** : champs **évolutifs** et **compatibles** (ajouter > enlever).  
- Règle : **compatibilité ascendante** tant que possible.  

---

## Documenter avec OpenAPI (Swagger)

- Spécifier **contrats** : endpoints, schémas, statuts.  
- Générer : **SDK**, **stubs**, **tests**, **portails dev**.  

```yaml
openapi: 3.0.3
info: { title: Store API, version: 1.0.0 }
paths:
  /products:
    get:
      summary: Liste des produits
      parameters:
        - in: query
          name: page
          schema: { type: integer, minimum: 1 }
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  items:
                    type: array
                    items: { $ref: '#/components/schemas/Product' }
components:
  schemas:
    Product:
      type: object
      properties: { id: { type: integer }, name: { type: string } }
```

---

## Exemple d’API REST — produits

```http
GET /api/products?sort=-createdAt&page=2&pageSize=20
200 OK
Content-Type: application/json

{ "items": [{ "id": 1, "name": "Livre" }], "page": 2, "pageSize": 20, "total": 240 }
```

```http
POST /api/products
201 Created
Location: /api/products/101
```

---

## Bonnes pratiques (récap)

- Contrats **stables** et **documentés** (OpenAPI).  
- **Statuts** et **erreurs** uniformes (RFC 7807).  
- **Pagination** et **filtrage** systématiques pour les listes.  
- **ETag** + préconditions pour éviter les conflits.  
- **Cache** côté client/CDN quand c’est possible.  
- **Observabilité** : logs, traces, métriques.  

---

## Outils utiles

- **Insomnia / Postman** (tests, collections).  
- **Prism** (mock server OpenAPI).  
- **Stoplight / Redocly** (portail développeurs).  
- **Spectral** (linting OpenAPI).  

---

## Partie suivante

### Partie VIII — Alternatives à REST : GraphQL, WebSockets
- GraphQL : schéma, requêtes, mutations, subscriptions.  
- WebSockets : temps réel, rooms, broadcast.  
- Choisir l’approche adaptée au besoin.  
