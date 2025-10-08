# Architecture des Applications Web  
## Partie V — Architectures côté serveur  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre les **composants du back-end** et leurs rôles.  
- Connaître les **serveurs web** (Apache, Nginx…) et les **frameworks** applicatifs.  
- Maîtriser les options de **persistance** (SQL / NoSQL) et le **caching**.  
- Appliquer les fondamentaux de **sécurité** (OWASP Top 10).  
- Exposer une **API** propre, testable et documentée.  

---

## Chaîne de traitement côté serveur

```
Client → Reverse proxy (Nginx/Caddy) → Application (Express/Django/Spring) →
Services (cache, auth) → Base de données (SQL/NoSQL) → Observabilité (logs, métriques)
```

- Découpler les responsabilités pour faciliter la maintenance et l’ops.  

---

## Serveurs web & reverse proxies

| Outil | Usage principal | Atouts | Cas d’usage |
|------|------------------|--------|-------------|
| **Nginx** | Reverse proxy, servir statiques | Performance, simplicité | Terminaison TLS, load balancing |
| **Apache** | Serveur HTTP historique | Modules riches | Hébergement mutualisé, CGI/PHP |
| **Caddy** | HTTP/2/3 + TLS auto | Config minimale | Sites/API avec HTTPS immédiat |
| **Traefik** | Proxy dynamique | Intégration Docker/K8s | Routage par labels/ingress |

---

## Frameworks d’application

| Langage | Frameworks | Points clés |
|--------|------------|-------------|
| **JS/TS** | Express, NestJS, Fastify | Écosystème NPM, asynchrone |
| **Python** | Django, Flask, FastAPI | Rapidité de dev, typage Pydantic (FastAPI) |
| **Java** | Spring Boot, Quarkus, Micronaut | Robuste, outillage mature |
| **PHP** | Laravel, Symfony | MVC complet, artisan/console |
| **Go / Rust** | Fiber, Gin / Axum, Actix | Performances, faible empreinte |

---

## Organisation recommandée (API REST)

```
/src
  /routes      # Définition des endpoints
  /controllers # Orchestration des cas d'usage
  /services    # Logique métier réutilisable
  /models      # Accès données (ORM / queries)
  /middlewares # Auth, validation, logs, erreurs
  /tests       # Tests unitaires / d’intégration
```

- Séparer **transport**, **métier**, **persistance**.  

---

## Persistance des données

**SQL** (PostgreSQL, MySQL, MariaDB, SQLite)  
- Schéma **structuré**, ACID, intégrité référentielle.  
- Idéal pour CRM, finance, transactions.  

**NoSQL** (MongoDB, Redis, Cassandra)  
- Schéma **souple**, forte scalabilité / débit.  
- Idéal pour cache, événements, analytics, catalogues.  

---

## ORM, Query Builder, SQL brut

- **ORM** (TypeORM, Django ORM, Hibernate) : productivité, migrations.  
- **Query Builder** (Knex, SQLAlchemy Core) : contrôle des requêtes.  
- **SQL brut** : performance maximale, lisibilité à documenter.  

**Règle** : choisir l’outil selon **complexité** et **maturité** de l’équipe.  

---

## Caching côté serveur

- **Redis / Memcached** pour : sessions, quotas, résultats de requêtes, verrous.  
- Stratégies : **cache-aside**, **read-through**, **write-through**, **write-behind**.  
- Invalidations : par **clé**, par **tag**, par **TTL**.  

```text
if cache.hit(key) → return value
else value = db.query(...); cache.set(key, value, ttl)
```

---

## Sécurité — OWASP (extraits)

- **Injection** (SQL/NoSQL/LDAP) → requêtes paramétrées, ORM, validation forte.  
- **Broken Auth** → utiliser des **gestionnaires d’auth** éprouvés, MFA, sessions sécurisées.  
- **Sensitive Data Exposure** → TLS, chiffrement au repos, rotation des secrets.  
- **XSS/CSRF** → échappement, CSP, SameSite, anti-CSRF tokens.  
- **Security Misconfiguration** → headers de sécurité, patching, moindre privilège.  

Réf. : <https://owasp.org/www-project-top-ten/>

---

## Validation & erreurs

- **Validation entrées** (JSON schema, Zod, Joi, Pydantic).  
- Normaliser les réponses d’erreur (ex. RFC 7807 “Problem Details”).  
- Ne pas **divulguer** d’infos sensibles dans les messages d’erreur.  

---

## Exemple — Mini API Express + SQLite

```js
import express from "express";
import Database from "better-sqlite3";

const db = new Database(":memory:");
db.exec("CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, email TEXT UNIQUE)");

const app = express();
app.use(express.json());

app.get("/api/users", (req, res) => {
  const rows = db.prepare("SELECT id, name, email FROM users").all();
  res.json({ items: rows });
});

app.post("/api/users", (req, res) => {
  const { name, email } = req.body || {};
  if (!name || !email) return res.status(422).json({ error: "name/email requis" });
  const info = db.prepare("INSERT INTO users (name, email) VALUES (?, ?)").run(name, email);
  res.status(201).json({ id: info.lastInsertRowid });
});
```

→ Base **embarquée** pour prototypage ; basculez ensuite vers PostgreSQL/MySQL.  

---

## Logs & observabilité

- **Logs structurés** (JSON) + corrélation (`trace_id`, `span_id`).  
- **Métriques** : requêtes/sec, p95 latence, erreurs 5xx.  
- **Traces** : OpenTelemetry → Jaeger/Zipkin.  
- **Dashboards** : Grafana, Kibana.  

---

## Déploiement & CI/CD

- **Containers** : Docker/Podman, multi-stage builds.  
- **Reverse proxy** devant l’app (Nginx/Traefik).  
- **CI/CD** : lint/tests, build image, déploiement staging/prod.  
- **Secrets** : vault / variables chiffrées.  

---

## Bonnes pratiques (récap)

- Routes **cohérentes** et **idempotentes** quand possible.  
- **Limiter** la surface d’attaque (headers, CORS, rate limiting).  
- **Timeouts** & **retries** côté client et serveur.  
- **Migrations** DB versionnées et testées.  
- **Documentation** d’API (OpenAPI/Swagger).  

---

## Partie suivante

### Partie VI — Architectures côté client
- Différence “page reload” vs SPA.  
- Ajax / fetch, JSON.  
- Panorama React / Vue / Angular ; PWA.  
