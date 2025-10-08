---
title: "Architecture des Applications Web — Partie I : Introduction générale"
theme: white
transition: slide
highlightTheme: github
---

# Architecture des Applications Web  
## Partie I — Introduction générale  
**Master Informatique**

---

## Objectifs de la séance

- Comprendre ce qu’est une application web  
- Identifier les enjeux d’architecture (performance, scalabilité, sécurité)  
- Situer les grandes familles d’architectures dans l’évolution du Web  

---

## De quoi parle-t-on ?

**Site web**  
→ Ensemble de pages statiques ou dynamiques diffusant de l’information.  

**Application web**  
→ Système interactif exploitant le navigateur comme interface applicative.  

**Exemples :** Gmail, Figma, Notion, Trello, Spotify Web.  

---

## Pourquoi une architecture ?

- Assurer la **cohérence** entre les couches (client, serveur, données)  
- Garantir la **scalabilité** (support de la charge, montée en puissance)  
- Maintenir la **performance** et la **sécurité**  
- Faciliter l’**évolutivité** et la **maintenance**  

---

## L’évolution des architectures Web

| Période | Modèle dominant | Exemple | Caractéristiques |
|----------|-----------------|----------|------------------|
| 1995–2000 | CGI / LAMP | PHP / Apache | Pages dynamiques simples |
| 2000–2010 | MVC côté serveur | Rails, Django | Séparation logique, réutilisabilité |
| 2010–2020 | SPA + API REST | React, Angular, Node | Découplage front/back |
| 2020–... | Microservices, Serverless | Kubernetes, AWS Lambda | Distribué, élastique |

---

## De la page au système distribué

Une application web moderne = **écosystème de services** :  

- Front-end dynamique (SPA, PWA)  
- API REST ou GraphQL  
- Services : authentification, stockage, cache, analytics  
- Infrastructure cloud (CDN, load balancing, containers)

---

## Panorama des architectures

### Monolithique
- Application unique et centralisée  
- Simple à développer  
- Difficile à faire évoluer

---

### N-tiers
- Séparation logique : présentation, logique métier, données  
- Architecture dominante dans les années 2000  
- Favorise la modularité

---

### Microservices
- Modules indépendants communiquant via API  
- Déploiement et scalabilité fine  
- Plus complexe à orchestrer

---

### Serverless
- Exécution à la demande (FaaS)  
- Aucune gestion d’infrastructure  
- Facturation à l’usage  
- Démarrage rapide, mais moins de contrôle

---

## L’architecture, une affaire de compromis

> “There is no silver bullet.”

Chaque modèle implique des choix :  
- **Complexité** vs **flexibilité**  
- **Coût** vs **performance**  
- **Vitesse de développement** vs **maintenabilité**

---

## Objectif global du cours

Apprendre à **comprendre, comparer et concevoir**  
des architectures d’applications web **efficaces, scalables et sécurisées**.  

---

## Prochaine séance

### Semaine 2 : Le protocole HTTP en profondeur  
- Structure des requêtes et réponses  
- Méthodes, statuts, en-têtes  
- Notion de statelessness  
