# Architecture des Applications Web  
## Partie XI — Performance, scalabilité & résilience  
**Master Informatique**

---

## Objectifs de la partie

- Identifier les **goulots d’étranglement** d’une application web.  
- Comprendre la **scalabilité** horizontale et verticale.  
- Maîtriser les **patterns de résilience**.  
- Mettre en place du **monitoring** et des **tests de charge**.  

---

## Performance — notions clés

| Indicateur | Description |
|-------------|-------------|
| **Latence** | Temps pour répondre à une requête |
| **Throughput (RPS)** | Nombre de requêtes par seconde |
| **TTFB** | Temps avant le premier octet |
| **Apdex / P95** | Satisfaction utilisateur |
| **Error rate** | Pourcentage d’erreurs sur le total |

→ Objectif : améliorer la **perception utilisateur** et la **stabilité système**.  

---

## Optimisations applicatives

- Minimiser les **appels réseau**.  
- Caching des **données fréquemment utilisées**.  
- Optimisation SQL (index, requêtes, pagination).  
- Compression (Gzip, Brotli).  
- Minification JS/CSS, lazy loading.  

---

## Caching — plusieurs niveaux

| Niveau | Exemple | Durée |
|---------|----------|--------|
| Navigateur | Cache-Control, ETag | court |
| CDN | Cloudflare, Akamai | moyen |
| Serveur | Redis, Memcached | long |

→ Les caches se **complètent**, pas se remplacent.  

---

## Scalabilité — principes

- **Verticale** : augmenter les ressources (CPU, RAM).  
- **Horizontale** : multiplier les instances.  

```text
Client → Load Balancer → [ API #1 | API #2 | API #3 ]
```

- Statelessness = clé de la scalabilité horizontale.  

---

## Load balancing — stratégies

- **Round Robin** (équitable).  
- **Least Connections** (équilibrage intelligent).  
- **IP Hash** (sticky sessions).  
- Outils : Nginx, HAProxy, Traefik, Envoy.  

---

## Patterns de résilience

- **Retry** + **backoff exponentiel**.  
- **Timeouts** explicites.  
- **Circuit Breaker** pour couper les appels défaillants.  
- **Fallback** : renvoyer une valeur par défaut.  
- **Bulkhead** : isoler les ressources critiques.  

```text
Service A → [CircuitBreaker] → Service B
```

---

## Exemple — Circuit Breaker (pseudo-code)

```python
if failures > 3:
    state = "OPEN"
elif state == "OPEN" and cooldown_elapsed():
    state = "HALF_OPEN"

try:
    call_service()
    reset_failures()
except Exception:
    record_failure()
```

---

## Observabilité — les 3 piliers

| Pilier | Objectif | Outils |
|---------|-----------|--------|
| **Logs** | Contexte textuel | ELK, Loki |
| **Metrics** | Mesures chiffrées | Prometheus, Grafana |
| **Traces** | Suivi des requêtes | OpenTelemetry, Jaeger |

→ Corréler avec un **traceId** unique.  

---

## Monitoring — bonnes pratiques

- Avoir des **dashboards** simples.  
- Définir des **seuils d’alerte** (P95 latency, CPU, erreurs).  
- Alerter sur la **tendance**, pas le pic unique.  
- **SLI/SLO/SLA** : indicateurs, objectifs, engagements.  

---

## Tests de charge

- Simuler le trafic pour détecter les limites.  
- Mesurer : **RPS**, **latence moyenne**, **P95**, **erreurs**.  

**Outils :**
- `k6`, `Gatling`, `Locust`, `Artillery`.  

```bash
k6 run test.js
```

```js
import http from "k6/http";
export default function() {
  http.get("https://example.com/api");
}
```

---

## Exemple — reverse proxy Nginx

```nginx
http {
  upstream backend {
    server api1:3000;
    server api2:3000;
  }

  server {
    listen 80;
    location / {
      proxy_pass http://backend;
      proxy_cache_valid 200 10s;
    }
  }
}
```

→ Load balancing + caching = combo performance.  

---

## Résumé — points clés

- **Mesurer** avant d’optimiser.  
- **Automatiser** les tests et la supervision.  
- **Anticiper** les pannes : retry, timeouts, fallbacks.  
- **Scalabilité horizontale** + **statelessness**.  

---

## Travaux pratiques

🎯 **Objectif :** tester et fiabiliser une app web.  
- Configurer Nginx comme reverse proxy.  
- Ajouter un cache sur les réponses statiques.  
- Lancer un test de charge avec `k6` ou `Gatling`.  
- Observer l’impact sur les métriques.  

---

## Partie suivante

### Partie XII — Nouvelles tendances
- Serverless, Edge computing, Progressive Web Apps.  
- Architectures émergentes et futurs modèles du Web.  
