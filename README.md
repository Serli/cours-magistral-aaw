# Syllabus – Architecture des Applications Web  
## Master Informatique — Cours magistral (14 parties)

---

### 🧭 Objectif global du cours
Comprendre les **principes, composants et évolutions des architectures web**, de HTTP aux microservices et applications cloud-native.  
L’étudiant doit être capable, à la fin du cours, de **concevoir et critiquer une architecture web complète**, côté client et serveur.

---

## 🧩 Partie 1 — Introduction & enjeux du Web moderne

**Objectifs pédagogiques :**
- Comprendre la différence entre site web et application web.  
- Identifier les enjeux d’architecture : scalabilité, performance, sécurité, maintenabilité.

**Contenu :**
- Évolution du Web : de CGI à l’ère du cloud et de l’edge computing  
- Panorama des architectures : monolithique, client-serveur, N-tiers, microservices  
- Présentation du projet fil rouge du cours (mini-application à concevoir)

**Lectures conseillées :**
- *The Architecture of the World Wide Web* (W3C)  
- *Martin Fowler – Patterns of Enterprise Application Architecture* (chap. 1)

---

## 🧩 Partie 2 — Le protocole HTTP en profondeur

**Objectifs pédagogiques :**
- Comprendre le fonctionnement bas niveau du protocole HTTP.  
- Analyser les requêtes/réponses et en-têtes.

**Contenu :**
- Requête et réponse HTTP : structure, headers, status codes  
- Méthodes (GET, POST, PUT, DELETE…)  
- Cache-Control, Content-Type, CORS  
- Introduction à HTTPS et TLS  

**Travaux :**
- Analyse de requêtes avec `curl` et DevTools navigateur

---

## 🧩 Partie 3 — Les standards du Web

**Objectifs pédagogiques :**
- Comprendre le rôle de HTML, CSS et JavaScript.  
- Manipuler le DOM et les interactions client.

**Contenu :**
- HTML5 : structure sémantique  
- CSS3 : responsive design, layout  
- JS : événements, manipulation du DOM  
- Cycle de vie d’une page web  

**Travaux :**
- Création d’une page interactive simple (DOM + JS pur)

---

## 🧩 Partie 4 — Gestion de l’état et communication client-serveur

**Objectifs pédagogiques :**
- Comprendre la statelessness de HTTP et les mécanismes pour gérer l’état.  
- Savoir utiliser cookies, sessions et tokens.

**Contenu :**
- Cookies, session côté serveur  
- JWT, OAuth, OpenID  
- Stockage client : localStorage, sessionStorage, IndexedDB  

**Travaux :**
- Implémentation d’un petit login avec sessions et cookies

---

## 🧩 Partie 5 — Architectures côté serveur

**Objectifs pédagogiques :**
- Comprendre le rôle des serveurs web et des frameworks back-end.  
- Découvrir l’accès aux données et la sécurité.

**Contenu :**
- Apache, Nginx, Express, Django, Spring Boot  
- ORM et gestion des bases de données (SQL / NoSQL)  
- Caching : Redis, Memcached  
- Sécurité : OWASP Top 10  

**Travaux :**
- Mise en place d’une mini API Express + SQLite

---

## 🧩 Partie 6 — Architectures côté client

**Objectifs pédagogiques :**
- Comprendre la différence entre modèles “page reload” et “SPA”.  
- Découvrir les frameworks front modernes.

**Contenu :**
- Ajax et `fetch()`  
- JSON comme format standard  
- SPA : principes et fonctionnement  
- React / Vue / Angular : panorama  

**Travaux :**
- Construction d’une SPA minimaliste avec React (ou Vue)

---

## 🧩 Partie 7 — Les APIs REST

**Objectifs pédagogiques :**
- Concevoir et documenter une API RESTful.  
- Comprendre les bonnes pratiques de design.

**Contenu :**
- Ressources, endpoints, statelessness  
- Versionnement, pagination, HATEOAS  
- OpenAPI / Swagger  

**Travaux :**
- Conception d’une API REST + documentation OpenAPI

---

## 🧩 Partie 8 — Alternatives à REST : GraphQL, WebSockets

**Objectifs pédagogiques :**
- Comprendre les motivations derrière GraphQL.  
- Découvrir les architectures en temps réel.

**Contenu :**
- GraphQL vs REST : modèles, flexibilité, complexité  
- Subscriptions, WebSockets  
- Exemple : chat en temps réel  

**Travaux :**
- Petit chat en Node.js avec WebSockets

---

## 🧩 Partie 9 — Microservices et communication distribuée

**Objectifs pédagogiques :**
- Comprendre les limites du monolithe.  
- Concevoir une architecture microservices.

**Contenu :**
- Décomposition fonctionnelle  
- REST, gRPC, message brokers  
- Gestion de la cohérence et de la latence  

**Travaux :**
- Découpage d’un projet monolithique en microservices  

---

## 🧩 Partie 10 — Conteneurisation et orchestration

**Objectifs pédagogiques :**
- Comprendre le rôle de Docker et Kubernetes.  
- Gérer le déploiement et l’isolation des services.

**Contenu :**
- Dockerfile, images, volumes, réseaux  
- Docker Compose  
- Introduction à Kubernetes et pods  

**Travaux :**
- Conteneuriser les microservices développés en Partie 9

---

## 🧩 Partie 11 — Performance, scalabilité et résilience

**Objectifs pédagogiques :**
- Identifier les goulots d’étranglement d’une application web.  
- Connaître les patterns de performance et résilience.

**Contenu :**
- Load balancing, CDN  
- Patterns de résilience : retry, circuit breaker, fallback  
- Monitoring, observabilité (logs, metrics, traces)  

**Travaux :**
- Tests de charge et mise en place d’un reverse proxy Nginx

---

## 🧩 Partie 12 — Nouvelles tendances

**Objectifs pédagogiques :**
- Découvrir les architectures émergentes.  
- Comprendre leurs cas d’usage et contraintes.

**Contenu :**
- Serverless et FaaS (AWS Lambda, Cloud Functions)  
- Edge computing (Cloudflare Workers, Deno Deploy)  
- Progressive Web Apps (PWA)  

**Travaux :**
- Déploiement d’une fonction serverless simple  
- Création d’une PWA minimale

---

## 🧩 Partie 13 — Études de cas réelles

**Objectifs pédagogiques :**
- Analyser des architectures industrielles.  
- Identifier les choix de conception et leurs compromis.

**Contenu :**
- Étude d’un e-commerce (scalabilité, sécurité, cache)  
- Étude d’une messagerie temps réel  
- Étude d’une plateforme vidéo (CDN, transcoding)

**Travaux :**
- Présentation orale : analyse d’une architecture réelle choisie par groupe

---

## 🧩 Partie 14 — Synthèse & perspectives

**Objectifs pédagogiques :**
- Faire le lien entre tous les concepts vus.  
- Identifier les évolutions futures du Web.

**Contenu :**
- Récapitulatif des architectures vues  
- WebAssembly, Edge computing, IA embarquée  
- Conclusion : choix architecturaux selon contexte et contraintes

**Évaluation finale :**
- Projet fil rouge (application complète à présenter)  
- Oral de synthèse (analyse critique d’une architecture)

---

## 🎓 Modalités d’évaluation

| Évaluation | Pondération | Description |
|-------------|-------------|--------------|
| Mini-projets par partie | 40% | Exercices pratiques par thème |
| Projet fil rouge | 40% | Conception d’une architecture complète |
| Présentation orale | 20% | Étude de cas et soutenance |

---

## 📚 Ressources recommandées (avec liens)

- **Martin Fowler – Patterns of Enterprise Application Architecture**  
  - 🔗 [Site officiel](https://martinfowler.com/books/eaa.html)  
  - 📖 [Amazon](https://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420)

- **Sam Newman – Building Microservices (2ᵉ édition)**  
  - 🔗 [Page auteur](https://samnewman.io/books/building_microservices_2nd_edition/)  
  - 📖 [Amazon](https://www.amazon.com/Building-Microservices-Sam-Newman-ebook/dp/B09B5L4NVT)  
  - 💻 [O’Reilly Online Edition](https://www.oreilly.com/library/view/building-microservices-2nd/9781492034018/)

- **Mark Richards & Neal Ford – Fundamentals of Software Architecture**  
  - 📖 [Amazon](https://www.amazon.com/Fundamentals-Software-Architecture-Comprehensive-Characteristics/dp/1492043451)  
  - 💻 [O’Reilly Online Edition](https://www.oreilly.com/library/view/fundamentals-of-software/9781492043447/)

- **Gregor Hohpe & Bobby Woolf – Enterprise Integration Patterns**  
  - 🔗 [Site officiel](https://www.enterpriseintegrationpatterns.com/)  
  - 📖 [Amazon](https://www.amazon.com/Enterprise-Integration-Patterns-Designing-Deploying/dp/0321200683)

- **IETF RFC 7230–7235 (HTTP/1.1)**  
  - 🌐 [RFC 7230 – Message Syntax and Routing](https://datatracker.ietf.org/doc/html/rfc7230)

- **Mozilla Developer Network (MDN Web Docs)**  
  - 🌐 [developer.mozilla.org](https://developer.mozilla.org)

- **OWASP Web Security Testing Guide**  
  - 🌐 [owasp.org/www-project-web-security-testing-guide](https://owasp.org/www-project-web-security-testing-guide/)

- Lien vers le discord
  - [AAW - 2026/2027](https://discord.gg/MUvtHAHed)

---

