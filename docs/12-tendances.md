# Architecture des Applications Web  
## Partie XII — Nouvelles tendances  
**Master Informatique**

---

## Objectifs de la partie

- Découvrir les **architectures émergentes** du Web moderne.  
- Comprendre les **modèles Serverless et Edge Computing**.  
- Explorer les **Progressive Web Apps (PWA)** et leur impact.  
- Identifier les **limites et cas d’usage** de ces approches.  

---

## Évolution du déploiement Web

- Du **serveur centralisé** au **cloud distribué**.  
- Émergence du **DevOps**, **CI/CD** et **GitOps**.  
- Objectif : automatiser, scaler, rapprocher le code de l’utilisateur.  

```text
Dev → CI/CD → Cloud → Edge → Utilisateur final
```

→ Moins de latence, plus de fiabilité.  

---

## Serverless — principes

- Exécution **à la demande**, sans serveur dédié.  
- Facturation à l’usage (par exécution ou durée).  
- Auto-scaling instantané.  

**Exemples :**
- AWS Lambda  
- Google Cloud Functions  
- Azure Functions  
- Deno Deploy  

---

## Exemple — fonction Serverless

```js
export async function handler(event) {
  return {
    statusCode: 200,
    body: JSON.stringify({ message: "Hello from the cloud!" })
  };
}
```

→ Déployée sans infrastructure, facturée par appel.  

---

## Avantages et limites du Serverless

| Avantages | Limites |
|------------|----------|
| Pas d’infra à gérer | Démarrage lent (cold start) |
| Scalabilité automatique | Débogage et monitoring limités |
| Coût à l’usage | Vendor lock-in |
| Résilience native | Pas adapté aux workloads persistants |

---

## Edge Computing

- Déplacement du calcul **au plus proche de l’utilisateur**.  
- Exécution dans des **PoPs** (points de présence) distribués.  

**Exemples :**
- Cloudflare Workers  
- Vercel Edge Functions  
- Netlify Edge  
- Fastly Compute@Edge  

→ Idéal pour le cache, la personnalisation, l’auth.  

---

## Jamstack revisité

- **J**avaScript, **A**PIs, **M**arkup.  
- Sites statiques + API + CDN.  
- Frameworks modernes : **Next.js**, **Astro**, **Remix**, **SvelteKit**.  

```text
Build → Deploy → CDN → Edge → Revalidate
```

→ Performance et SEO excellents grâce au pré‑rendu.  

---

## Progressive Web Apps (PWA)

- Application web **installable**, **hors ligne**, **rapide**.  
- Requiert :  
  - HTTPS  
  - `manifest.json`  
  - `service-worker.js`  

```json
{
  "name": "Mon App PWA",
  "start_url": "/",
  "display": "standalone",
  "icons": [{ "src": "/icon.png", "sizes": "512x512" }]
}
```

---

## Exemple — Service Worker

```js
self.addEventListener("install", e => {
  e.waitUntil(
    caches.open("v1").then(cache => cache.addAll(["/", "/index.html", "/app.js"]))
  );
});

self.addEventListener("fetch", e => {
  e.respondWith(caches.match(e.request).then(r => r || fetch(e.request)));
});
```

→ Mise en cache locale, fonctionnement offline.  

---

## Tendances à venir

- **WebAssembly (WASM)** : exécution native dans le navigateur.  
- **Edge AI** : inférence locale sans cloud.  
- **Durable Objects** (Cloudflare) : état distribué cohérent.  
- **Green IT** : réduction de l’empreinte carbone du cloud.  

---

## Travaux pratiques

🎯 **Objectif :** explorer les tendances modernes.  
- Déployer une fonction serverless (AWS, Cloudflare).  
- Transformer une app en **PWA installable**.  
- Comparer la latence entre **Edge** et **serveur centralisé**.  

---

## Partie suivante

### Partie XIII — Études de cas réelles
- Analyse d’architectures industrielles.  
- Études : e-commerce, messagerie temps réel, plateforme vidéo.  
