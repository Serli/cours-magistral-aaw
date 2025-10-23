# 🧱 HTML — Les bases  
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

Revoir les fondamentaux du langage **HTML5**, base de toute page web.

À l’issue de cette courte révision, vous saurez :  
- Structurer une page avec les balises essentielles.  
- Différencier contenu et présentation.  
- Comprendre la sémantique HTML moderne.  
- Utiliser les attributs de base et bonnes pratiques.  

---

## 🌐 Qu’est‑ce que HTML ?

**HTML (HyperText Markup Language)** décrit la **structure et le sens** du contenu d’une page web.

- Le navigateur interprète le code pour afficher du texte, des images, des liens, etc.  
- HTML ne gère **ni le style** (CSS) **ni le comportement** (JavaScript).

> 🎓 HTML = structure  
> CSS = apparence  
> JS = interaction

---

## 🧱 Structure minimale d’une page HTML5

```html
<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Ma page</title>
</head>
<body>
  Contenu visible ici
</body>
</html>
```

**À retenir :**
- `<!doctype html>` → active le mode standard.  
- `lang` → utile pour l’accessibilité et les moteurs de recherche.  
- `<head>` contient les métadonnées, `<body>` le contenu affiché.

---

## 🔤 Les balises de texte

```html
<h1>Titre principal</h1>
<h2>Sous-titre</h2>
<p>Un paragraphe avec <strong>mise en valeur</strong> et <em>emphase</em>.</p>
```

- `<h1>` à `<h6>` : titres hiérarchiques (un seul `<h1>` par page).  
- `<p>` = paragraphe.  
- `<strong>` = importance, `<em>` = accentuation.  
- `<br>` = saut de ligne (éviter pour la mise en page).

---

## 🔗 Liens & médias

```html
<a href="https://developer.mozilla.org/" target="_blank" rel="noopener">
  Ressource MDN
</a>

<img src="logo.png" alt="Logo du site" width="120">
```

- `<a>` crée un lien hypertexte.  
- `target="_blank"` ouvre un nouvel onglet.  
- `rel="noopener"` protège contre les failles de sécurité.  
- `alt` = description texte de l’image.

---

## 📋 Listes et tableaux

```html
<ul>
  <li>HTML</li>
  <li>CSS</li>
  <li>JavaScript</li>
</ul>

<table>
  <thead><tr><th>Nom</th><th>Note</th></tr></thead>
  <tbody><tr><td>Alice</td><td>17</td></tr></tbody>
</table>
```

**À retenir :**
- `<ul>` = liste non ordonnée, `<ol>` = ordonnée.  
- `<table>` = données tabulaires, pas pour la mise en page.  
- Utiliser `<th>` pour les en-têtes de colonnes ou de lignes.

---

## 🧩 Balises sémantiques modernes

```html
<header>En-tête</header>
<nav>Navigation</nav>
<main>Contenu principal</main>
<article>Article indépendant</article>
<section>Section thématique</section>
<aside>Contenu secondaire</aside>
<footer>Pied de page</footer>
```

> Ces balises structurent le document pour les lecteurs, robots et outils d’accessibilité.

**Avantages :**
- Meilleur **SEO** (moteurs de recherche).  
- Lecture facilitée par les lecteurs d’écran.  
- Structure plus claire pour les développeurs.

---

## 📄 Les attributs HTML

- `id` : identifiant unique d’un élément.  
- `class` : groupe d’éléments partageant un style ou comportement.  
- `title` : info-bulle facultative.  
- `lang` : langue de l’élément.  
- `data-*` : attributs personnalisés pour le JS.

```html
<p id="intro" class="highlight">Bonjour le monde</p>
```

---

## 📨 Les formulaires — aperçu

```html
<form action="/login" method="post">
  <label for="user">Utilisateur</label>
  <input id="user" name="user" required>
  <button type="submit">Connexion</button>
</form>
```

**À retenir :**
- `<form>` = conteneur d’entrée utilisateur.  
- `label` lié à l’input via `for`.  
- `required` = validation côté navigateur.

---

## ♿ Accessibilité — bonnes pratiques

- Toujours fournir un **texte alternatif** (`alt`) pour les images.  
- Relier les **labels** aux champs de formulaire.  
- Respecter la hiérarchie de titres (`h1` → `h2` → `h3`).  
- Utiliser `lang="fr"` pour indiquer la langue du document.  
- Vérifier les pages avec un validateur W3C.

---

## 🔍 Validation et vérification

- Outil en ligne : [https://validator.w3.org/](https://validator.w3.org/)  
- DevTools (Chrome/Firefox) → Inspecter → *Elements*.  
- Vérifiez :  
  - Structure correcte du DOM.  
  - Absence de balises orphelines.  
  - Propreté des attributs et indentation.

---

## 🧠 À retenir

- HTML = structure sémantique, pas présentation.  
- Toujours soigner la **hiérarchie des titres** et la **sémantique**.  
- Utiliser des **balises modernes** (`header`, `main`, `footer`…).  
- Vérifier la validité et l’accessibilité.  
- Garder un code **lisible, propre et indenté**.

---

## 📚 Pour aller plus loin

- 🌐 [MDN – Guide HTML](https://developer.mozilla.org/fr/docs/Web/HTML)  
- ✅ [W3C Validator](https://validator.w3.org/)  
- 📘 [HTML Living Standard – WHATWG](https://html.spec.whatwg.org/)  
- 🎨 [WebAIM – Accessibility Principles](https://webaim.org/intro/)  

---

## 🧩 Suite

Prochain rappel : **CSS — Les bases du style et de la mise en forme** 🎨
