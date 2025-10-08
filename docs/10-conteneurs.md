# Architecture des Applications Web  
## Partie X — Conteneurisation & orchestration  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre la **virtualisation légère** avec Docker.  
- Savoir construire, exécuter et partager des **conteneurs**.  
- Découvrir **Docker Compose** et **Kubernetes**.  
- Comprendre les notions de **pods**, **services** et **scaling**.  

---

## Virtualisation vs Conteneurisation

| Caractéristique | Machine virtuelle | Conteneur |
|-----------------|-------------------|------------|
| Isolation | OS complet (hyperviseur) | Processus isolé |
| Performance | Lourde (démarrage lent) | Légère (rapide) |
| Portabilité | Moyenne | Excellente |
| Exemple | VMware, VirtualBox | Docker, Podman |

→ Les conteneurs partagent le **kernel** de l’hôte, mais restent isolés.  

---

## Docker — concepts clés

- **Image** : modèle immuable d’un conteneur.  
- **Conteneur** : instance en exécution d’une image.  
- **Dockerfile** : recette de construction d’image.  
- **Registry** : dépôt d’images (`Docker Hub`, `GHCR`, `ECR`).  

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY . .
RUN npm ci
CMD ["node", "server.js"]
```

---

## Commandes Docker essentielles

```bash
docker build -t myapp .
docker run -p 8080:8080 myapp
docker ps
docker logs <container_id>
docker exec -it <container_id> sh
```

→ Chaque conteneur est **éphémère** et **stateless**.  

---

## Réseaux et volumes

- Réseau par défaut : `bridge`.  
- Communication inter-conteneurs via noms de service (`mydb:5432`).  
- **Volumes** : stockage persistant (`/var/lib/docker/volumes`).  

```bash
docker volume create mydata
docker run -v mydata:/data myapp
```

---

## Docker Compose

- Fichier YAML pour décrire **plusieurs services**.  

```yaml
version: "3"
services:
  api:
    build: .
    ports: ["3000:3000"]
    depends_on: [db]
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: example
```

→ Lance tout d’un coup : `docker compose up -d`.  

---

## Orchestration : pourquoi ?

- Plusieurs conteneurs → besoin de **gestion centralisée**.  
- Objectifs :  
  - **Scaling automatique**  
  - **Redémarrage** et **auto‑healing**  
  - **Réseautage** et **découverte de services**  
  - **Déploiement progressif**  

→ Kubernetes est devenu le **standard de facto**.  

---

## Kubernetes — concepts de base

| Élément | Rôle |
|----------|------|
| **Pod** | Unité de déploiement (un ou plusieurs conteneurs) |
| **ReplicaSet** | Assure un nombre constant de pods |
| **Deployment** | Gère les mises à jour |
| **Service** | Expose les pods (ClusterIP, NodePort, LoadBalancer) |
| **Ingress** | Route le trafic HTTP/HTTPS |
| **ConfigMap / Secret** | Configuration et secrets |

---

## Exemple : déploiement Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: api }
spec:
  replicas: 2
  selector: { matchLabels: { app: api } }
  template:
    metadata: { labels: { app: api } }
    spec:
      containers:
        - name: api
          image: myapp:latest
          ports: [{ containerPort: 3000 }]
```

```bash
kubectl apply -f api-deployment.yaml
```

---

## Scaling & mise à jour

- **Horizontal Pod Autoscaler (HPA)** : ajuste les réplicas selon la charge CPU/RAM.  
- **Rolling update** : remplacement progressif des pods.  
- **Rollback** possible.  

```bash
kubectl scale deployment api --replicas=5
kubectl rollout status deployment api
```

---

## Monitoring & observabilité

- **Logs** : `kubectl logs`, Fluentd, Loki.  
- **Metrics** : Prometheus, Grafana.  
- **Tracing** : OpenTelemetry, Jaeger.  

→ Toujours exporter des **metrics standardisées** (`/metrics`, Prometheus).  

---

## Sécurité & bonnes pratiques

- Exécuter en **non‑root**.  
- Restreindre les **ressources** (`requests`, `limits`).  
- Scanner les images (Trivy, Grype).  
- Signer les images (Cosign, Sigstore).  
- Stocker les secrets dans **Kubernetes Secrets** ou **Vault**.  

---

## Service Mesh

- Couche d’infrastructure pour :  
  - Routage avancé (canary, mirroring)  
  - Sécurité (mTLS)  
  - Observabilité centralisée  

→ Exemples : **Istio**, **Linkerd**.  

---

## Travaux pratiques

🎯 **Objectif :** conteneuriser et orchestrer.  
- Créer un `Dockerfile` pour une app Node.js ou Python.  
- Lancer via `docker compose`.  
- Déployer sur **minikube** ou **kind**.  
- Observer logs et scaling automatique.  

---

## Partie suivante

### Partie XI — Performance, scalabilité & résilience
- Load balancing, CDN, patterns de résilience.  
- Observabilité, tests de charge, optimisation.  
