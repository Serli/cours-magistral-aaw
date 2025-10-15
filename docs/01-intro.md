# Architecture des Applications Web  
## Partie I — Introduction générale  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre ce qu’est une application web.  
- Identifier les enjeux d’architecture : performance, scalabilité, sécurité, maintenabilité.  
- Situer les grandes familles d’architectures dans l’évolution du Web.  

---

## De quoi parle-t-on ?

**Site web**  
→ Ensemble de pages statiques ou dynamiques diffusant de l’information.  

**Application web**  
→ Système interactif exploitant le navigateur comme interface applicative.  

**Exemples :** Gmail, Figma, Notion, Trello, Spotify Web.  

---

## Pourquoi une architecture ?

- Assurer la **cohérence** entre les couches (client, serveur, données).  
- Garantir la **scalabilité** (support de la charge, montée en puissance).  
- Maintenir la **performance** et la **sécurité**.  
- Faciliter l’**évolutivité** et la **maintenance**.  

---

## ⚙️ Scalabilité — Principe général

La **scalabilité** désigne la capacité d’un système à **s’adapter à la charge**  
(en augmentant ses ressources ou le nombre de serveurs).

Deux grands types :

| Type de scalabilité | Principe  | Exemple  |
|---------------------|-----------|----------|
| 🧱 **Verticale**     | On rend une machine plus puissante | + CPU, + RAM |
| 🕸️ **Horizontale**   | On ajoute plusieurs machines | cluster, load balancer |

**Objectif :** maintenir les performances quand la charge augmente  
(sans dégrader la disponibilité ni exploser les coûts).

---

## 🧱 Scalabilité verticale (scale-up)

### Principe
Augmenter la puissance d’un **nœud unique**.

```
Avant :                        Après :
+--------------------+        +----------------------+
|   Serveur unique   |        |   Même serveur       |
|  2 vCPU, 4 Go RAM  |        |  16 vCPU, 64 Go RAM  |
+--------------------+        +----------------------+
```

**Avantages :**
- Simple à mettre en place.  
- Pas de modification logicielle.  

**Limites :**
- Coût élevé des grosses machines.  
- Seuil physique maximal.  
- Risque de **single point of failure**.

---

## 🕸️ Scalabilité horizontale (scale-out)

### Principe
Ajouter plusieurs serveurs **en parallèle** pour répartir la charge.

```
            +------------------+
            |  Load Balancer   |
            +--------▲---------+
                     │
   ┌-----------------┼---------------┐
   ▼                 ▼               ▼
+---------+     +---------+     +---------+
| Serveur |     | Serveur |     | Serveur |
|   #1    |     |   #2    |     |   #3    |
+---------+     +---------+     +---------+
```

**Avantages :**
- Scalabilité quasi illimitée.  
- Résilience accrue (tolérance aux pannes).  

**Limites :**
- Complexité de synchronisation et de cohérence.  
- Gestion du **state** (sessions, cache) plus difficile.

---

## L’évolution des architectures Web

| Période | Modèle dominant | Exemple | Caractéristiques |
|----------|-----------------|----------|------------------|
| 1995–2000 | CGI / LAMP | PHP / Apache | Pages dynamiques simples |
| 2000–2010 | MVC côté serveur | Rails, Django | Séparation logique, réutilisabilité |
| 2010–2020 | SPA + API REST | React, Angular, Node | Découplage front/back |
| 2020–... | Microservices, Serverless, Edge | Kubernetes, AWS Lambda, Cloudflare Workers | Distribué, élastique, proche de l’utilisateur |

---

## De la page au système distribué

Une application web moderne = **écosystème de services** :  

- Front-end dynamique (SPA, PWA).  
- API REST ou GraphQL.  
- Services : authentification, stockage, cache, analytics.  
- Infrastructure cloud (CDN, load balancing, containers, edge).  

---

## Panorama des architectures

### Monolithique
- Application unique et centralisée.  
- Simple à développer.  
- Difficile à faire évoluer.  

---

## 🧱 Architecture monolithique

### Principe
Tout le code de l’application (interface, logique métier, accès aux données) est **dans un seul bloc déployable**.

```
+----------------------------------------------------+
|               Application monolithique             |
|  +----------------------------------------------+  |
|  | Présentation (UI, routes, templates)         |  |
|  +----------------------------------------------+  |
|  | Logique métier (services, règles, contrôles) |  |
|  +----------------------------------------------+  |
|  | Accès aux données (ORM, SQL, fichiers)       |  |
|  +----------------------------------------------+  |
+----------------------------------------------------+
                     │
                     ▼
              Base de données unique
```

**Caractéristiques :**
- Déploiement unique, simple à gérer.  
- Communication interne directe (même processus).  
- Devient difficile à maintenir / déployer à grande échelle.

---

### N-tiers
- Séparation logique : présentation, logique métier, données.  
- Architecture dominante dans les années 2000.  
- Favorise la modularité.  

---

## 🧩 Architecture N-tiers (3-tiers classique)

### Principe
Séparation en **couches logiques distinctes**, souvent réparties sur plusieurs serveurs.

```
+--------------------+     +--------------------+     +--------------------+
|   Présentation     |     |     Logique        |     |      Données       |
| (Front : navigateur| --> |  métier / serveur  | --> |   (Base SQL/NoSQL) |
|   ou appli mobile) |     | (API, backend)     |     |                    |
+--------------------+     +--------------------+     +--------------------+
         ↑                          ↑
         |   HTTP / REST            |
         +--------------------------+
```

**Caractéristiques :**
- Meilleure séparation des responsabilités.  
- Facilite la maintenance et la scalabilité horizontale.  
- Couplage plus faible que le monolithe, mais toujours un point central (backend).

---

### Microservices
- Modules indépendants communiquant via API.  
- Déploiement et scalabilité fine.  
- Plus complexe à orchestrer.  

---

## 🕸️ Architecture microservices

### Principe
Chaque fonctionnalité est un **service indépendant**, déployé séparément et communiquant via des API.

```
             +--------------------+
             |     Front-end      |
             | (SPA / Mobile App) |
             +--------------------+
                     │
           ┌─────────┼──────────────┬───────────────────────┐
           ▼         ▼              ▼                       ▼
   +------------+ +------------+ +------------+      +-------------+
   | Service A  | | Service B  | | Service C  | ...  | Service N   |
   | (Users)    | | (Orders)   | | (Billing)  |      | (Analytics) |
   +------------+ +------------+ +------------+      +-------------+
           │           │              │                     │
           └───────────┴──────────────┴─────────────────────┘
                        Message bus / API Gateway / DBs
```

**Caractéristiques :**
- Chaque service = cycle de vie et base de données propres.  
- Scalabilité fine, résilience accrue.  
- Complexité opérationnelle (observabilité, réseau, orchestration).

---

### Serverless
- Exécution à la demande (FaaS).  
- Aucune gestion d’infrastructure.  
- Facturation à l’usage.  
- Démarrage rapide, mais moins de contrôle.  

---

## ☁️ Architecture Serverless (FaaS)

### Principe
Le code est découpé en **fonctions autonomes** exécutées **à la demande**, sans serveur à gérer.

```
+---------------------+        +------------------------+
|   Application Web   |        |  Cloud Functions (FaaS)|
| (Frontend / Client) | -----> |   - AuthFunction       |
|                     |        |   - SaveOrderFunction  |
+---------------------+        |   - SendEmailFunction  |
                               +------------------------+
                                          │
                                          ▼
                                   Services cloud
                               (DB, stockage, API externes)
```

**Caractéristiques :**
- Pas de gestion de serveur → exécution à la demande.  
- Facturation à l’usage.  
- Scalabilité automatique.  
- Démarrage rapide, mais complexité sur le suivi, la latence et le débogage.

---

### Edge computing
- Exécution **au plus près des utilisateurs** (serveurs “en périphérie”).  
- Réduction de la **latence** et du **trafic vers le cloud**.  
- Déploiement distribué (PoP, CDN, zones géographiques).  
- Moins de persistance locale, mais grande rapidité.  

---

## 🌍 Architecture Edge Computing

### Principe
Certaines logiques ou fonctions sont exécutées **sur des serveurs périphériques**  
(CDN, PoP, terminaux IoT) **plutôt que dans le cloud central**.

```
+-------------------------------+
|       Cloud central           |
|  (Bases, backend, analytics)  |
+---------------▲---------------+
                │
        Synchronisation
                │
      ┌─────────┴───────────────┐
      ▼                         ▼
+-------------+         +-------------+
|  Edge Node  |         |  Edge Node  |
|  - Cache    |         |  - Auth     |
|  - Routing  |         |  - API log. |
+-------------+         +-------------+
      ▲                         ▲
      │                         │
      └── Utilisateurs finaux ──┘
```

**Caractéristiques :**
- Réduit la latence et les coûts de transfert.  
- Améliore la résilience et la rapidité perçue.  
- Exemple : Cloudflare Workers, AWS Lambda@Edge, Deno Deploy.

---

## 🧠 En résumé

| Modèle          | Points forts                        | Limites principales                  |
|-----------------|-------------------------------------|--------------------------------------|
| Monolithique    | Simplicité, cohérence               | Difficulté d’évolution               |
| N-tiers         | Modularité, séparation logique      | Couplage entre couches               |
| Microservices   | Scalabilité, résilience             | Complexité, supervision difficile    |
| Serverless      | Élasticité, coût à l’usage          | Moins de contrôle, cold start        |
| Edge Computing  | Latence minimale, proximité client  | Ressources limitées, persistance réduite |

---

## L’architecture, une affaire de compromis

> “There is no silver bullet.”

Chaque modèle implique des choix :  
- **Complexité** vs **flexibilité**  
- **Coût** vs **performance**  
- **Vitesse de développement** vs **maintenabilité**

---

## Retours d'expérience

> Comment construire une licorne sur un monolithe
>
> (N. Domenjoud & N. De Nayer - Doctolib)

<iframe width="560" height="315" src="https://www.youtube.com/embed/ui_FlkhtxRE?si=aXSXE0pF420e5ODM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Devoxx France 2019]

---

## Retours d'expérience

> Comment le COVID a révolutionné Doctolib
>
> (D.Gageot / N.De Nayer)

<iframe width="560" height="315" src="https://www.youtube.com/embed/uo73C2ck1aU?si=dxWkcHfLDqd2zZLg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Devoxx France 2021]

---

## Partie suivante

### Partie II — Le protocole HTTP en profondeur
- Structure des requêtes et réponses.  
- Méthodes, statuts, en-têtes.  
- Notion de statelessness.  
