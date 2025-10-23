# 🧪 HTML, JS et CSS : premiers pas
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif

- Créer une page interactive simple en HTML, CSS et JS pur.
- Afficher une carte d’utilisateur avec nom, avatar et bouton “Like”.
- Stocker le compteur de likes dans `localStorage`.
- Soigner la mise en page responsive.

---

## 📝 Énoncé

- **But** : Construire une carte utilisateur avec un bouton *Like* et un compteur **persistant**.

- **Contraintes** : HTML/CSS/JS **vanilla** uniquement. Structure propre et responsive. Accessibilité minimale.

- **Livrables** : code source (`index.html`, `style.css`, `app.js`) + mini README pour lancer en local.

- **Étapes suggérées** :
  1. Structure HTML de la carte.
  2. Styles de base (centrage, typographie, bouton).
  3. Interactivité JS (mise à jour du compteur au clic).
  4. Persistance via `localStorage`.
  5. Responsive et accessibilité.

> Vous pouvez vous inspirer des snippets montrés dans la suite des slides, mais éviter les *copier/coller* sans comprendre 😉

---

## 🧰 Mise en place du projet

1. Créez un dossier `tp1-like-card/`.
2. Fichiers :
   - `index.html`
   - `style.css`
   - `app.js`
3. Liez les fichiers dans `index.html` :

```html
<!doctype html>
<html lang="fr">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>TP – Carte utilisateur</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <main id="app"></main>
    <script src="app.js" defer></script>
  </body>
</html>
```

---

## 🧱 Étape 1 – Structure HTML de base

- Objectif : afficher une **carte** avec avatar, nom, compteur et bouton.
- Placez une structure minimale dans `<main id="app">`.

```html
<article class="card" aria-live="polite">
  <img class="avatar" src="https://i.pravatar.cc/160?img=12" alt="Avatar de l’utilisateur" />
  <h2 class="name">Jane Doe</h2>
  <p class="likes" data-key="likes">❤️ <span class="count">0</span></p>
  <button class="like-btn" type="button" aria-pressed="false">Like</button>
</article>
```

> Vous pouvez générer un avatar temporaire via `https://pravatar.cc/` (ou utilisez un fichier local).

---

## 🎨 Étape 2 – Styles de base (CSS)

- Objectif : rendre la carte **propre** et **centrée**.
- Exemple (à adapter) :

```css
:root {
  --bg: #0f172a; /* slate-900 */
  --card: #111827; /* gray-900 */
  --text: #e5e7eb; /* gray-200 */
  --accent: #f59e0b; /* amber-500 */
}
* { box-sizing: border-box; }
body { margin: 0; font-family: system-ui, sans-serif; background: var(--bg); color: var(--text); }
main { min-height: 100dvh; display: grid; place-items: center; padding: 2rem; }
.card { width: min(90vw, 420px); background: var(--card); border-radius: 16px; padding: 1.25rem; box-shadow: 0 10px 30px rgba(0,0,0,.3); text-align: center; }
.avatar { width: 120px; aspect-ratio: 1; border-radius: 50%; object-fit: cover; display: block; margin: 0 auto 1rem; }
.like-btn { font: inherit; padding: .7rem 1.1rem; border-radius: 999px; border: none; cursor: pointer; }
.like-btn:focus-visible { outline: 3px solid var(--accent); outline-offset: 3px; }
```

> **Responsive** : utilisez des unités relatives (`rem`, `vw`) et l’attribut `aspect-ratio`.

---

## 🧩 Étape 3 – Interactivité (JS) : sélection & écouteurs

- Objectif : réagir au clic sur le bouton.
- Pseudo‑code :

```
compteur = 0
sélectionner le bouton et l’élément .count
quand on clique sur le bouton :
  compteur += 1
  mettre à jour le texte de .count
```

- Exemple minimal :

```js
const countEl = document.querySelector('.count');
const btn = document.querySelector('.like-btn');
let count = 0; // provisoire : sera remplacé par localStorage

btn.addEventListener('click', () => {
  count += 1;
  countEl.textContent = String(count);
});
```

---

## 🧠 Notion : `localStorage` en 2 minutes

- **Clé/valeur** de type **string** stockée dans le navigateur.
- Persiste **entre les rechargements** (même onglet, même origine).
- API :
  - `localStorage.setItem('clé', 'valeur')`
  - `localStorage.getItem('clé')` → `string | null`
  - `localStorage.removeItem('clé')`
  - `localStorage.clear()` (⚠️ tout enlever)
- Pour des objets : `JSON.stringify` / `JSON.parse`.

```js
// Valeur numérique avec valeur par défaut
const KEY = 'likes:v1';
const initial = Number(localStorage.getItem(KEY)) || 0;

function saveLikes(n) {
  localStorage.setItem(KEY, String(n));
}
```

---

## 💾 Étape 4 – Persistance du compteur

- Objectif : **restaurer** le compteur au chargement et **sauvegarder** à chaque clic.

```js
const KEY = 'likes:v1';
const countEl = document.querySelector('.count');
const btn = document.querySelector('.like-btn');

let count = Number(localStorage.getItem(KEY)) || 0;
countEl.textContent = String(count);

btn.addEventListener('click', () => {
  count += 1;
  countEl.textContent = String(count);
  localStorage.setItem(KEY, String(count));
});
```

> Astuce : **namespacer** les clés (`likes:v1`) pour gérer d’éventuelles évolutions.

---

## 📱 Étape 5 – Responsive & petits écrans

- **Mobile‑first** : concevez pour ~360–400px puis élargissez.
- Ajustez la taille des polices/éléments.
- Exemple de media query :

```css
@media (min-width: 640px) {
  .card { padding: 1.75rem; }
  .avatar { width: 140px; }
}
```

> Bonus : testez `prefers-reduced-motion` pour réduire les animations.

---

## ♿ Étape 6 – Accessibilité minimale

- Texte alternatif d’images (`alt`).
- **Contrastes** suffisants (vérifiez dans DevTools > Accessibility).
- Focus visible (`:focus-visible`).
- Si le bouton devient un toggle, gérez `aria-pressed`.

```js
btn.addEventListener('click', () => {
  // ...mise à jour du compteur
  const pressed = btn.getAttribute('aria-pressed') === 'true';
  btn.setAttribute('aria-pressed', String(!pressed));
});
```

---

## 🧱 Étape 7 – Organisation du code

- Isolez la logique dans des **fonctions** : `loadLikes()`, `saveLikes(n)`, `render(n)`.
- Évitez les variables globales non nécessaires.
- Commentez les parties non triviales.

```js
function loadLikes(key) { return Number(localStorage.getItem(key)) || 0; }
function saveLikes(key, n) { localStorage.setItem(key, String(n)); }
function render(el, n) { el.textContent = String(n); }
```

---

## 🧪 Étape 8 – Tests manuels & debug

- Utilisez la **console** DevTools (`Console`, `Application > Storage > Local Storage`).
- Vérifiez le comportement après **rechargement**.
- Faites un **hard refresh** (vider le cache) si besoin.
- Passez un coup de **Lighthouse** (onglet *Audits* ou *Lighthouse*).

---

## ✨ Extensions (facultatives)

- Bouton **Reset** pour remettre le compteur à 0.
- Animation légère sur le bouton (ex : `transform: scale(1.05)` au clic).
- Changement d’icône après un certain nombre de likes.
- **Thème clair/sombre** via `prefers-color-scheme` + toggle utilisateur.

---

## 📚 Ressources utiles

- MDN – [Manipulation du DOM](https://developer.mozilla.org/fr/docs/Web/API/Document_Object_Model)
- MDN – [`localStorage`](https://developer.mozilla.org/fr/docs/Web/API/Window/localStorage)
- Web.dev – [Responsive Design Basics](https://web.dev/learn/design/)
- A11Y – [Checklist WCAG rapide](https://www.w3.org/WAI/standards-guidelines/wcag/)

---
