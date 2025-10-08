# Architecture des Applications Web  
## Partie VIII — Alternatives à REST : GraphQL, WebSockets  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre **quand** et **pourquoi** sortir du modèle REST.  
- Découvrir **GraphQL** (schema, query, mutation, subscription).  
- Maîtriser les bases des **WebSockets** pour le **temps réel**.  
- Situer **SSE**, **gRPC-web** et **WebTransport** dans le paysage.  

---

## Pourquoi des alternatives à REST ?

- **Sous/Sur‑récupération** (under/over‑fetching).  
- **Multiples allers‑retours** pour composer des vues.  
- **Temps réel** (chat, présence, trading…), difficile avec HTTP pur.  
- Besoin d’un **contrat typé** et **auto‑documenté** côté front.  

---

## Panorama rapide

| Besoin | Technologie | Points forts |
|--------|-------------|--------------|
| Requêtes **flexibles** sur un graphe | **GraphQL** | Schéma typé, une requête → données exactes |
| **Temps réel** bi‑directionnel | **WebSockets** | Duplex, faible latence |
| Push **server → client** unidirectionnel | **SSE** | Simple, HTTP, reconnection auto |
| RPC binaire performant (navigateur) | **gRPC‑web** | Contrats proto, streaming |
| Transports récents (UDP, faible latence) | **WebTransport** | Successeur potentiel de WebRTC data‑channels |

---

## GraphQL — concepts clés

- **Schéma typé** (SDL) : `type Query`, `type Mutation`, `type Subscription`.  
- **Query** : le client **déclare** la forme exacte des données.  
- **Resolvers** : fonctions qui récupèrent les données.  
- **Introspection** : API auto‑documentée (GraphiQL, Apollo Studio).  

```graphql
type User { id: ID!, name: String!, email: String! }
type Query { user(id: ID!): User, users(limit: Int): [User!]! }
type Mutation { createUser(name: String!, email: String!): User! }
type Subscription { userCreated: User! }
```

---

## GraphQL — exemple de requête

```graphql
query GetUsers {
  users(limit: 2) { id name email }
}
```

Réponse : uniquement les champs demandés.  
→ Réduit **over‑fetching** et **allers‑retours**.  

---

## GraphQL — mutation & subscription

```graphql
mutation {
  createUser(name: "Alice", email: "alice@example.com") {
    id name
  }
}
```

```graphql
subscription {
  userCreated { id name }
}
```

- Temps réel via **WebSocket** (GraphQL over WS) ou **SSE**.  

---

## GraphQL — points d’attention

- **N+1 queries** côté resolvers → utiliser **batching**/**caching** (ex. DataLoader).  
- **Contrôle d’accès** au **champ** (field‑level auth).  
- **Validation** et **limitation** : depth/complexity limit, persisted queries.  
- **Versionnement** : éviter les breaking changes, **déprécier** les champs.  
- **Supergraph** : **fédération** (Apollo Federation), schema stitching.  

---

## WebSockets — principes

- **Connexion persistante** après un **handshake HTTP**.  
- Communication **full‑duplex** client ↔ serveur.  
- Idéal pour : **chat**, **collaboration**, **notifications**, **marchés**.  

```js
const ws = new WebSocket("wss://example.com/socket");
ws.onopen = () => ws.send(JSON.stringify({ type: "hello" }));
ws.onmessage = (e) => console.log("message", e.data);
```

---

## WebSockets — architecturer à l’échelle

- **Stateful** : nécessite **sticky sessions** ou **pub/sub** (Redis, NATS, Kafka).  
- **Rooms / channels** : diffusion sélective.  
- **Backpressure** & quotas : éviter la saturation.  
- **Reconnect** & **heartbeats** (ping/pong).  
- Auth : **JWT** au handshake et renouvellement.  

---

## WebSockets vs SSE vs HTTP Polling

| Critère | WebSockets | SSE | Polling |
|--------|------------|-----|---------|
| Direction | Bidirectionnel | Server → Client | Client → Serveur |
| Scalabilité | Complexe (stateful) | Simple (HTTP) | Coûteux |
| Compat. proxies | Variable | Excellente | Excellente |
| Cas d’usage | Chat, jeux, trading | Notifications, flux | Simplicité, fallback |

---

## gRPC‑web & WebTransport (aperçu)

- **gRPC‑web** : RPC sur HTTP/2, **proto** contrats, streaming limité côté navigateur.  
- **WebTransport** : basé sur **HTTP/3/QUIC**, faible latence, datagrams.  
→ Options pour besoins **haute performance** spécifiques.  

---

## Sécurité — bonnes pratiques

- **Auth** : OAuth2/OIDC, **JWT** courts, scopes.  
- **Rate limiting** et **quotas**.  
- **CORS** (GraphQL sur domaine séparé).  
- **Validation** stricte des messages (WS/SSE).  
- Chiffrement : **TLS** partout.  

---

## Exemple — Resolver GraphQL (Node/Apollo)

```js
export const resolvers = {
  Query: {
    users: (_, { limit }, { dataSources }) => dataSources.db.listUsers({ limit })
  },
  Mutation: {
    createUser: (_, args, { dataSources, user }) => {
      if (!user) throw new Error("unauthorized");
      return dataSources.db.createUser(args);
    }
  }
};
```

---

## Exemple — Diffusion WebSocket (Node)

```js
import { WebSocketServer } from "ws";
const wss = new WebSocketServer({ port: 8080 });
wss.on("connection", (ws) => {
  ws.on("message", (msg) => {
    for (const client of wss.clients) {
      if (client.readyState === 1) client.send(msg); // broadcast
    }
  });
});
```

---

## Outils & écosystème

- **GraphQL** : Apollo Server/Client, GraphiQL, Apollo Studio, urql.  
- **WS** : Socket.IO, ws, uWebSockets.js, NATS, Redis pub/sub.  
- Tests/perf : **k6**, **Gatling**, **Locust**, **Artillery**.  

---

## Quand choisir quoi ?

- **REST** : CRUD standard, caches HTTP, simplicité, exposition publique.  
- **GraphQL** : front complexe, agrégation de sources, mobile (réseau limité).  
- **WebSockets** : **temps réel**, interactions bidirectionnelles.  
- **SSE** : push léger, compatibilité réseau.  

---

## Partie suivante

### Partie IX — Microservices & communication distribuée
- Décomposition, contrats & orchestration.  
- REST/gRPC/events ; cohérence, latence, résilience.  
- Observabilité bout‑en‑bout.  
