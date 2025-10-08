# Architecture des Applications Web  
## Partie VI — Architectures côté client  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre les différences entre **rendu côté serveur (SSR)** et **rendu côté client (CSR)**.  
- Identifier les avantages et limites des **Single Page Applications (SPA)**.  
- Maîtriser la communication **client ↔ serveur** (fetch, JSON).  
- Découvrir les **frameworks front modernes** et les approches **hybrides** (SSR, SSG, hydration).  

---

## Du Web statique au Web dynamique

| Période | Modèle | Description | Exemples |
|----------|---------|--------------|-----------|
| 1995–2005 | **MPA (Multi-Page App)** | Chaque clic → nouvelle page complète | PHP, ASP.NET, JSP |
| 2005–2015 | **Ajax / CSR** | Chargement asynchrone, logique côté client | jQuery, Backbone |
| 2015–2020 | **SPA** | Rendu côté client unique, routing JS | React, Vue, Angular |
| 2020–... | **Hybride SSR/SSG** | Pré-rendu + hydration | Next.js, Nuxt, SvelteKit |

---

## Architecture Multi-Page (MPA)

- Chaque requête charge **une page complète** depuis le serveur.  
- Simplicité : SEO, pas de JS obligatoire.  
- Limites : rechargement complet, expérience fragmentée.  

```
Client → GET /page → HTML complet
```

→ Adapté aux sites éditoriaux, documentations, blogs statiques.  

---

## Ajax & `fetch()` : le Web asynchrone

- Chargement partiel des données sans recharger la page.  
- Communication via **API JSON**.  

```js
fetch("/api/users")
  .then(r => r.json())
  .then(data => console.log(data));
```

- Permet l’interactivité sans changer d’URL.  
- Base du modèle **SPA**.  

---

## Single Page Application (SPA)

- Une **unique page HTML**, les transitions sont gérées par JS.  
- Navigation client-side via **router** (`history.pushState`).  
- Données dynamiques via **API REST / GraphQL**.  

```
index.html
 ├── App.js (routing)
 ├── components/
 └── api/
```

→ UX fluide, mais SEO et performance initiale plus délicats.  

---

## Frameworks modernes — panorama

| Framework | Philosophie | Points forts |
|------------|--------------|--------------|
| **React** | Composants + Virtual DOM | Écosystème énorme, JSX, Hooks |
| **Vue** | Réactif, progressif | Simplicité, directives, composition API |
| **Angular** | Framework complet | DI, RxJS, CLI puissant |
| **Svelte** | Compilation au build | Moins de runtime, ultra rapide |
| **SolidJS** | Réactivité fine-grain | Performance, typage fort |

→ React & Vue dominent le marché, mais Svelte/Solid gagnent du terrain.  

---

## Cycle de vie d’une SPA

1. Téléchargement de `index.html` minimal.  
2. Chargement des bundles JS/CSS.  
3. Initialisation du framework.  
4. Hydration : le DOM statique devient interactif.  
5. Routing client-side.  

→ Performance = taille du bundle + hydration time.  

---

## Gestion de l’état (client-side)

- **State local** : Hook (`useState`), `data()` (Vue).  
- **State global** : Redux, Zustand, Pinia, Vuex.  
- **State dérivé** : Recoil, Jotai, signals (Angular 17+).  
- **Synchronisation** : SWR, React Query, Apollo.  

→ But : éviter la “prop drilling” et maintenir la cohérence des données.  

---

## SSR, SSG et ISR — approches hybrides

| Mode | Description | Exemples | Avantages |
|------|--------------|----------|------------|
| **SSR (Server-Side Rendering)** | Rendu HTML à chaque requête | Next.js, Nuxt, Remix | SEO, temps de chargement initial |
| **SSG (Static Site Generation)** | Pré-rendu au build | Next.js, Astro | Ultra rapide, cache CDN |
| **ISR (Incremental Static Regeneration)** | Pages statiques mises à jour à intervalle | Next.js | Équilibre perfs / fraîcheur |

→ Permet de combiner SEO et UX fluide.  

---

## Progressive Web Apps (PWA)

- Fonctionnent **hors ligne** grâce au **Service Worker**.  
- Installation comme une **application native**.  
- Atouts : rapidité, notifications push, offline cache.  

**Fichiers clés :**
- `manifest.json`  
- `service-worker.js`  
- HTTPS obligatoire.  

---

## Exemple de PWA (simplifiée)

```js
self.addEventListener("install", event => {
  event.waitUntil(
    caches.open("v1").then(cache => cache.addAll(["/", "/index.html", "/app.js"]))
  );
});
```

→ Mise en cache des ressources essentielles.  

---

## Performance front-end

- Minimiser le **bundle JS** (code splitting, lazy loading).  
- Optimiser les **images** et **fonts**.  
- Utiliser un **CDN**.  
- Mesurer : Lighthouse, Web Vitals (`LCP`, `FID`, `CLS`).  
- Privilégier les **hydrations partielles** (Astro, Qwik, React Server Components).  

---

## Sécurité côté client

- Éviter le stockage de secrets dans le code.  
- Valider toutes les entrées (XSS, injection HTML).  
- Utiliser `Content-Security-Policy`.  
- Toujours via HTTPS.  

---

## Travaux pratiques

🎯 **Objectif :** construire une mini SPA.  
- Afficher une liste de produits via une API REST.  
- Ajouter un système de favoris stocké en `localStorage`.  
- Utiliser un routeur client (React Router, Vue Router).  
- Bonus : transformer en **PWA** installable.  

---

## Partie suivante

### Partie VII — Les APIs REST
- Principes de conception et bonnes pratiques.  
- Structuration d’une API RESTful.  
- OpenAPI / Swagger.  
