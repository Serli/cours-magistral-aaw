# Architecture des Applications Web  
## Partie XIII — Études de cas réelles  
**Master Informatique**

---

## Objectifs de la partie

- Analyser des **architectures concrètes** d’applications web.  
- Identifier les **choix techniques** et leurs **compromis**.  
- Évaluer la **scalabilité**, la **sécurité** et la **résilience**.  
- Développer un **esprit critique** face aux architectures industrielles.  

---

## Méthodologie d’analyse

1. Comprendre le **contexte métier** et les **contraintes**.  
2. Identifier les **composants clés** : front, back, base de données, services.  
3. Cartographier les **flux de données** et les dépendances.  
4. Évaluer selon :  
   - Performance et disponibilité  
   - Coût et maintenance  
   - Sécurité et conformité  

---

## Cas n°1 — Plateforme e-commerce

**Objectif :** vendre, traiter les paiements, suivre les commandes.  

- Architecture **microservices** : produits, commandes, paiements, stock.  
- **API Gateway** + **authentification JWT**.  
- **Cache** produit (Redis), **CDN** pour les images.  
- **File de messages** (Kafka) pour la commande asynchrone.  
- **Sécurité** : PCI DSS, WAF, secrets manager.  

```text
Client → Gateway → Orders → Payments → Inventory
                   ↓ async
                 Kafka queue
```

→ Scalabilité horizontale et cohérence eventual.  

---

## Cas n°2 — Messagerie temps réel

**Objectif :** communication instantanée entre utilisateurs.  

- **WebSockets** pour la bidirectionnalité.  
- Backend distribué avec **Redis pub/sub** ou **NATS**.  
- Gestion des **rooms** et des **statuts de présence**.  
- **Observabilité** : métriques de connexions actives.  

```text
Clients ↔ Gateway ↔ Chat-Service ↔ Redis pub/sub ↔ Other Instances
```

→ Défis : scalabilité, reconnexion, stockage des messages.  

---

## Cas n°3 — Plateforme vidéo / streaming

**Objectif :** ingestion, transcodage, diffusion mondiale.  

- Upload → File de traitement → CDN.  
- Stockage objet (S3, MinIO).  
- File de traitement (SQS, Kafka).  
- **Transcodage** distribué (FFmpeg workers).  
- CDN multi-région pour diffusion.  

```text
Uploader → Queue → Transcoder → Storage → CDN Edge → Utilisateur
```

→ Architecture asynchrone, orientée flux, hautement scalable.  

---

## Étude comparative

| Critère | E-commerce | Messagerie | Streaming |
|----------|-------------|-------------|------------|
| Type de charge | Lecture/écriture mixte | Temps réel | Traitement média |
| Scalabilité | Horizontale | Élastique | Massivement parallèle |
| Persistance | SQL + cache | In-memory + historique | Objet + métadonnées |
| Sensibilité latence | Moyenne | Élevée | Très élevée |
| Observabilité | Logs, métriques, traces | Connexions actives | Pipeline et files |

---

## Approche critique

- Pourquoi ce découpage ? (fonctionnel, performance, équipe ?)  
- Quels **risques de couplage** ?  
- Quelle stratégie de **résilience** ?  
- Si la charge double : goulots d’étranglement ?  
- Quelle **dette technique** à long terme ?  

→ Toujours relier architecture ↔ besoins métier.  

---

## Synthèse

- Pas d’architecture universelle.  
- Chaque choix implique un **compromis** :  
  **scalabilité – complexité – coût.**  
- Le rôle de l’architecte : **équilibrer** ces trois axes.  

---

## Travaux pratiques

🎯 **Objectif :** analyser une architecture réelle.  
- Choisir une application (publique ou interne).  
- Identifier ses composants et interactions.  
- Proposer des améliorations argumentées.  
- Présentation orale de 10 min / groupe.  

---

## Partie suivante

### Partie XIV — Synthèse & perspectives
- Récapitulatif global du cours.  
- Tendances à venir (WebAssembly, Edge AI, sobriété numérique).  
- Bilan et ouverture.  
