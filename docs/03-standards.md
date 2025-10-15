
# Architecture des Applications Web  
## Partie III — Les standards du Web  
**Master Informatique**

---

## Objectifs de la partie

- Comprendre le rôle fondamental de **HTML**, **CSS** et **JavaScript** dans le Web.  
- Manipuler la structure du **DOM** et les **interactions client**.  
- Identifier le cycle de vie d’une page web.  
- Poser les bases du développement front-end moderne.  

---

## Le socle du Web : 3 langages complémentaires

| Langage | Rôle principal | Exemple |
|----------|----------------|----------|
| **HTML** | Structure et sémantique du contenu | `<article><h1>Titre</h1></article>` |
| **CSS** | Présentation et mise en forme | `h1 { color: crimson; }` |
| **JavaScript** | Dynamisme et interaction | `document.querySelector('h1').textContent = 'Bonjour !'` |

→ Une page web moderne combine ces trois couches de manière cohérente.  

---

## HTML — Structure et sémantique

- HyperText Markup Language.  
- Structure hiérarchique en balises : `<html>`, `<head>`, `<body>`, etc.  
- Éléments sémantiques : `<header>`, `<main>`, `<article>`, `<section>`, `<footer>`.  
- Accessibilité : attributs `alt`, `aria-*`, titres structurés `<h1>–<h6>`.  
- Validation : [validator.w3.org](https://validator.w3.org/)

---

### Exemple minimal

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Exemple HTML</title>
</head>
<body>
  <header>
    <h1>Bonjour Web !</h1>
  </header>
  <main>
    <p>Ceci est une page HTML valide et sémantique.</p>
  </main>
</body>
</html>
```

---

## CSS — Mise en forme et présentation

- Cascading Style Sheets.  
- Trois modes d’application :  
  1. **Inline** → `style="color: red"`  
  2. **Interne** → `<style>h1 { color: red; }</style>`  
  3. **Externe** → `<link rel="stylesheet" href="style.css">`  
- Sélecteurs, classes, identifiants, pseudo-classes.  
- Box model, Flexbox, Grid.  

---

### Exemple CSS simple

```css
body {
  font-family: system-ui, sans-serif;
  background: #fafafa;
  color: #222;
}

h1 {
  color: darkslateblue;
  text-align: center;
}
```

→ Un seul fichier CSS peut styliser des centaines de pages.  

---

## Responsive design

- Objectif : adapter la mise en page à la taille d’écran.  
- Outil clé : **media queries**.  

```css
@media (max-width: 600px) {
  main {
    flex-direction: column;
  }
}
```

- Utiliser des unités relatives (`em`, `rem`, `%`, `vh`, `vw`).  
- Frameworks utiles : Bootstrap, Tailwind CSS, Bulma.  

---

## JavaScript — Dynamisme et logique côté client

- Langage exécuté **dans le navigateur**.  
- Manipule le **DOM (Document Object Model)**.  
- Permet d’ajouter de l’interactivité : menus, formulaires, animations, API.  
- Depuis ES6 : syntaxe moderne (modules, `let/const`, `arrow functions`).  

---

### Exemple : manipulation du DOM

```html
<h1 id="titre">Bonjour !</h1>
<button id="btn">Changer le titre</button>

<script>
  const titre = document.getElementById("titre");
  const bouton = document.getElementById("btn");

  bouton.addEventListener("click", () => {
    titre.textContent = "Bienvenue dans le Web dynamique 🚀";
  });
</script>
```

→ Interaction directe entre utilisateur et page.  

---

## Le DOM (Document Object Model)

- Représentation **arborescente** de la page HTML.  
- Chaque balise devient un **nœud** manipulable via JS.  
- API du navigateur : `document.querySelector()`, `innerHTML`, `classList`, etc.  
- Événements : `click`, `input`, `load`, `scroll`, etc.  

---

## Cycle de vie d’une page web

1. **Requête HTTP** → téléchargement du HTML.  
2. **Parsing** → construction du DOM.  
3. **Téléchargement** des ressources CSS / JS.  
4. **Application du CSS** → création du CSSOM.  
5. **Exécution du JS**.  
6. **Rendu** dans le navigateur.  

→ Mesurable via l’onglet *Performance* des DevTools.  

---

## Les standards du Web et le W3C

- **W3C (World Wide Web Consortium)** : définit les spécifications HTML, CSS, DOM, etc.  
- **WHATWG** (Working Group) : maintient les standards “vivants” (HTML Living Standard).  
- Objectif : interopérabilité entre navigateurs.  
- Références :  
  - [https://html.spec.whatwg.org/](https://html.spec.whatwg.org/)  
  - [https://www.w3.org/](https://www.w3.org/)  

---

## 🌍 Can I use — compatibilité des standards du Web

**Objectif :** savoir si une technologie web (HTML, CSS, JS, API)  
est **prise en charge par les navigateurs**.

🔗 [https://caniuse.com](https://caniuse.com)

---

### 🧭 Exemple : `CSS grid`

```
https://caniuse.com/css-grid
```

| Navigateur | Support | Version minimale |
|-------------|----------|------------------|
| Chrome | ✅ | 57+ |
| Firefox | ✅ | 52+ |
| Safari | ✅ | 10.1+ |
| Edge | ✅ | 16+ |
| IE | ❌ | — |

➡️ Permet de vérifier avant d’utiliser une **nouvelle propriété CSS ou API JS**.  
➡️ Indique aussi les **préfixes** (`-webkit-`, `-moz-`, etc.) si nécessaires.

---

## Bonnes pratiques front-end

- Utiliser une **structure HTML sémantique**.  
- Séparer contenu (HTML), style (CSS) et comportement (JS).  
- Tester le rendu sur plusieurs navigateurs / appareils.  
- Vérifier la performance (Lighthouse, WebPageTest).  
- Respecter l’accessibilité (WCAG 2.1).  

---

## Travaux pratiques

🎯 **Objectif :** créer une page interactive simple en HTML, CSS et JS pur.  
- Afficher une carte d’utilisateur avec nom, avatar et bouton “Like”.  
- Stocker le compteur dans `localStorage`.  
- Soigner la mise en page responsive.  

---

## Partie suivante

### Partie IV — Gestion de l’état et communication client-serveur
- Cookies, sessions, tokens.  
- Mécanismes d’authentification (JWT, OAuth).  
- Stockage client et synchronisation avec le serveur.  
