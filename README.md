# Architecture des Applications Web  
## Master Informatique — Cours magistral

---

## I. Introduction générale

1. **Définition et enjeux des applications web**
   - Différence entre application web et site web  
   - Pourquoi les architectures web sont cruciales (scalabilité, performance, sécurité)  
   - Évolution historique : CGI → LAMP → Cloud-native  

2. **Panorama des architectures**
   - Monolithique  
   - Client-serveur  
   - N-tiers  
   - Microservices et serverless  

---

## II. Fondamentaux du Web

1. **Le protocole HTTP**
   - Structure d’une requête et d’une réponse  
   - Méthodes : GET, POST, PUT, DELETE…  
   - Codes de statut  
   - En-têtes : Content-Type, Accept, Cache-Control, CORS, etc.  

2. **Les standards du Web**
   - HTML, CSS, JavaScript  
   - Modèle DOM  
   - Rôle des navigateurs  

3. **Communication et état**
   - Statelessness de HTTP  
   - Cookies, sessions, tokens  
   - Stockage côté client (localStorage, IndexedDB)  

---

## III. Architectures côté serveur

1. **Serveurs Web et Application Servers**
   - Apache, Nginx  
   - Middleware (Express, Spring Boot, Django)  

2. **Accès aux données**
   - Drivers et ORM  
   - SQL vs NoSQL  
   - Caching (Redis, Memcached)  

3. **Sécurité côté serveur**
   - Authentification & autorisation  
   - OWASP Top 10  
   - HTTPS / TLS  

---

## IV. Architectures côté client

1. **Modèle classique**
   - Pages servies depuis le serveur  
   - Rechargement complet  

2. **Ajax et le passage vers des applications dynamiques**
   - XMLHttpRequest et `fetch()`  
   - JSON comme format d’échange standard  

3. **Les Single Page Applications (SPA)**
   - Concept et fonctionnement  
   - Frameworks modernes : React, Angular, Vue  
   - Avantages et limites  

---

## V. APIs et intégration

1. **REST**
   - Principes : ressources, uniformité, statelessness  
   - Bonnes pratiques de design : versionnement, pagination, HATEOAS  

2. **GraphQL**
   - Différences avec REST  
   - Cas d’usage et limites  

3. **WebSockets & temps réel**
   - Différences avec HTTP  
   - Exemples : chat, notifications  

4. **API sécurisées**
   - OAuth 2.0, OpenID Connect  
   - JWT (JSON Web Tokens)  

---

## VI. Architectures distribuées et scalabilité

1. **Monolithes vs microservices**
   - Découpage fonctionnel  
   - Communication inter-services : REST, gRPC, message brokers  

2. **Orchestration et conteneurs**
   - Docker, Kubernetes  
   - Patterns d’orchestration  

3. **Performance et disponibilité**
   - Load balancing  
   - CDN  
   - Patterns de résilience : circuit breaker, retry, fallback  

---

## VII. Nouvelles tendances

1. **Serverless et FaaS (Functions as a Service)**
   - AWS Lambda, Azure Functions  
   - Avantages et contraintes  

2. **Jamstack**
   - Sites statiques + API + CDN  
   - Exemples : Netlify, Vercel  

3. **Progressive Web Apps (PWA)**
   - Service Workers  
   - Mode hors-ligne  
   - Installation sur mobile  

---

## VIII. Études de cas et perspectives

1. **Étude d’architectures réelles**
   - Exemple d’une application e-commerce  
   - Exemple d’un service de streaming vidéo  
   - Exemple d’une messagerie temps réel  

2. **Perspectives**
   - WebAssembly  
   - Edge computing  
   - Intelligence artificielle embarquée côté client  

---

## Structure pédagogique suggérée

- **Durée :** 12 à 14 semaines  
- **Format :** Cours magistral + études de cas + mini-projets  
- **Objectif :** Comprendre, concevoir et critiquer des architectures d’applications web modernes.  

---
