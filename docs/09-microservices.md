# Architecture des Applications Web  
## Partie IX — Microservices & communication distribuée  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre les **limites du monolithe** et les motivations du découpage.  
- Concevoir une **décomposition fonctionnelle** en microservices.  
- Identifier les **modes de communication** (REST, gRPC, messaging).  
- Gérer la **cohérence**, la **résilience** et l’**observabilité**.  

---

## Du monolithe au microservice

### Monolithe
- Une seule base de code, un seul déploiement.  
- Simplicité de développement initiale.  
- Mais : dépendances fortes, scalabilité limitée.  

### Microservices
- Petits services **indépendants**, communiquant via API ou messages.  
- Évolutivité, scalabilité, résilience accrue.  
- Mais complexité accrue (ops, réseau, monitoring).  

---

## Principes de découpage

- Basé sur le **domaine fonctionnel** (DDD, bounded contexts).  
- Chaque service : un **périmètre clair**, une **base de données propre**.  
- Communication par **contrat** explicite (API, events).  
- Éviter les **dépendances circulaires**.  

```text
+-------------+     +-------------+
| Service A   | --> | Service B   |
+-------------+     +-------------+
```

→ Faible couplage, forte cohésion.  

---

## Communication inter-services

| Type | Exemple | Cas d’usage | Couplage |
|------|----------|-------------|-----------|
| **REST** | HTTP/JSON | CRUD, synchro | Moyen |
| **gRPC** | HTTP/2, ProtoBuf | perfs, streaming | Moyen |
| **Messaging** | RabbitMQ, Kafka | events, async | Faible |

→ Choisir selon la **latence**, la **fréquence** et la **criticité**.  

---

## Patterns de communication

- **Request/Response** : REST, gRPC.  
- **Publish/Subscribe** : événements (Kafka, NATS).  
- **Event sourcing** : stockage sous forme d’événements.  
- **CQRS** : séparation commande / lecture.  

```text
Commande → Événement → Projection → Vue
```

---

## Exemple : découpage e-commerce

| Service | Responsabilité | Base de données |
|----------|----------------|----------------|
| **Users** | Authentification, profils | users.db |
| **Orders** | Commandes, paiements | orders.db |
| **Products** | Catalogue, stock | products.db |

→ Communication via API REST ou messages (async).  

---

## Cohérence & transactions distribuées

- Pas de **transaction globale** entre services.  
- Stratégies :  
  - **Saga pattern** (orchestrée ou chorégraphiée)  
  - **Messages compensatoires**  
  - **Idempotence** des opérations  
- Garanties faibles : **eventual consistency**.  

---

## Résilience & tolérance aux pannes

- **Retry** avec backoff exponentiel.  
- **Timeouts** explicites.  
- **Circuit breaker** : couper un service défaillant.  
- **Bulkhead** : isoler les ressources critiques.  
- **Fallbacks** : valeurs par défaut, cache local.  

```text
Service A → [CircuitBreaker] → Service B
```

---

## Observabilité

- **Logs** : centralisation (ELK, Loki).  
- **Metrics** : Prometheus, Grafana.  
- **Traces** : OpenTelemetry, Jaeger.  
- Corrélation : **traceId**, **spanId** dans les entêtes.  

```http
traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-00
```

---

## Déploiement & orchestration

- Conteneurs (Docker).  
- Orchestration (Kubernetes, Docker Swarm).  
- Patterns :  
  - **Sidecar** (proxy, observabilité)  
  - **Service Mesh** (Istio, Linkerd)  
  - **Autoscaling** (HPA, KEDA).  

---

## Sécurité & gouvernance

- Auth inter-services : **mTLS**, **JWT** signés.  
- Gestion des secrets : Vault, KMS.  
- Rate limiting, quotas, API Gateway.  
- Versionning & politiques d’accès.  

---

## Avantages et limites

| Avantages | Limites |
|------------|----------|
| Scalabilité indépendante | Complexité de déploiement |
| Isolation des pannes | Difficulté de debug |
| Équipes autonomes | Besoin de CI/CD robuste |
| Évolutivité rapide | Cohérence faible |

---

## Travaux pratiques

🎯 **Objectif :** découper une API monolithique.  
- Créer 3 microservices : `users`, `orders`, `products`.  
- Communication REST ou via file de messages.  
- Corréler les logs avec un **trace ID** commun.  
- Bonus : ajout d’un **circuit breaker** (Node.js, Python ou Java).  

---

## Partie suivante

### Partie X — Conteneurisation & orchestration
- Dockerfiles, images, réseaux, volumes.  
- Docker Compose & Kubernetes (pods, services).  
- Déploiement des microservices et scaling.  
