# 🎨 CSS — Les bases  
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

Revoir les **fondamentaux CSS** pour styliser une page HTML :  
- Syntaxe, sélecteurs, **cascade & héritage**  
- **Box model** (margin, border, padding, content)  
- Mise en page : **flexbox** & **grid** (aperçu)  
- **Responsive design** (media queries)  
- Bonnes pratiques & outils

---

## 🎛️ Rôle et syntaxe du CSS

- **CSS** décrit l’**apparence** (style) d’un document HTML.  
- Trois façons d’appliquer du CSS :  
  1. **Externe** : `<link rel="stylesheet" href="styles.css">` ✅  
  2. Interne : `<style> … </style>`  
  3. En‑ligne : `<h1 style="color:red">` ❌ à éviter

```css
/* styles.css */
h1 { color: #0a66ff; font-weight: 600; }
```

---

## 🧭 Sélecteurs de base

```css
/* Par balise */
p { line-height: 1.6; }

/* Par classe */
.note { background: #fff8c5; padding: 8px; }

/* Par id (à limiter) */
#header { border-bottom: 1px solid #e5e5e5; }
```

- Préférer **classes** pour réutiliser les styles.  
- Les **id** sont uniques → usage parcimonieux.

---

## 🎯 Sélecteurs avancés (aperçu)

```css
/* Descendant / enfant direct */
article p { margin: 0; }
nav > a { padding: 6px 10px; }

/* Adjacent / frère général */
h2 + p { margin-top: 0; }
h2 ~ p { color: #444; }

/* Attribut */
a[target="_blank"] { text-decoration: underline dotted; }
```

---

## ⚖️ Cascade, spécificité et héritage

- **Cascade** : dernier style appliqué (à priorité égale) gagne.  
- **Spécificité** (simplifié) : `!important` > id > classe/attr/pseudo > balise.  
- **Héritage** : certaines propriétés (ex. `color`, `font`) se propagent aux enfants.

```css
/* Attention à !important — à éviter sauf cas isolés */
.warning { color: #b00020 !important; }
```

---

## 🔢 Unités & tailles

- **Absolues** : `px`  
- **Relatives** : `em`, `rem`, `%`, `vw`, `vh`  
  - `1rem` = taille de la **racine** (`html`)  
  - `em` dépend du **parent**

```css
html { font-size: 16px; } /* 1rem = 16px */
h1 { font-size: 1.75rem; }
.container { width: 90%; max-width: 1100px; }
```

---

## 🎨 Couleurs et variables CSS

```css
:root {
  --brand: #0a66ff;
  --text: #222;
}
h1 { color: var(--brand); }
p  { color: var(--text); }
```

- Formats couleur : `#rrggbb`, `rgb()`, `hsl()`  
- **Variables CSS** (custom properties) : réutilisables et surcharges faciles.

---

## 📦 Box model (très important)

Chaque élément a : **content → padding → border → margin**

```css
.card {
  width: 300px;
  padding: 12px;
  border: 1px solid #ddd;
  margin: 12px;
}
/* Inclure padding+border dans la largeur déclarée */
* { box-sizing: border-box; }
```

> `box-sizing: border-box` rend les mises en page plus prévisibles ✅

---

## 🧱 Display & flux

- `display: block` : prend toute la largeur (`div`, `p`, `h1`…)  
- `display: inline` : s’écoule dans la ligne (`a`, `span`)  
- `display: inline-block` : inline + dimensions  
- `display: none` : retiré du flux (⚠️ accessibilité)

```css
.badge { display: inline-block; padding: 2px 6px; }
```

---

## 🧰 Positionnement (aperçu)

- `position: static` (par défaut)  
- `relative` (décalage local), `absolute` (sort du flux, référence ancêtre positionné),  
- `fixed` (par rapport au viewport), `sticky` (collant selon scroll).

```css
.sticky-footer { position: sticky; bottom: 0; }
```

---

## 🧩 Flexbox — mise en page 1D

Idéal pour alignements & distributions sur **un axe**.

```css
.toolbar {
  display: flex;
  gap: 10px;
  align-items: center;   /* axe croisé */
  justify-content: space-between; /* axe principal */
}
```

- Axes : principal (row/column), croisé (perpendiculaire).  
- Outils clés : `gap`, `flex: 1`, `justify-content`, `align-items`.

---

## 🧮 Grid — mise en page 2D (aperçu)

Grilles bidimensionnelles (lignes/colonnes).

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 20px;
}
```

- Puissant pour des **layouts** complexes.  
- Fonctionne très bien avec des grilles **responsives**.

---

## 📱 Responsive design & media queries

```css
/* Mobile-first */
.card { padding: 12px; }

@media (min-width: 768px) {
  .card { padding: 16px; }
}

@media (min-width: 1200px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

- Penser **mobile‑first**, augmenter progressivement.  
- Tester sur plusieurs tailles d’écran.

---

## 🧑‍💻 Outils modernes

- **DevTools** (Chrome/Firefox) : inspecteur, onglet **Layout** (flex/grid), **Computed**.  
- **Autoprefixer / PostCSS** (via toolchain) pour compat compatibilité.  
- **Can I use** : vérifier le support navigateur.  
- **Design tokens** : variables partagées (couleurs, espacements, fontes).

---

## ✅ Bonnes pratiques

- Préférer les classes, nommage clair (`.btn-primary`, `.card`).  
- Limiter la profondeur des sélecteurs.  
- Éviter `!important` et les id pour le style.  
- Isoler les composants (BEM, utility-first, etc.).  
- Toujours activer `box-sizing: border-box` globalement.

---

## 🔍 Débogage rapide

- Ajouter un contour temporaire : `* { outline: 1px dashed #ddd; }`  
- Inspecter l’arbre et les règles **cascadées** dans DevTools.  
- Vérifier les **calculs** (`Computed`) et la **spécificité** des sélecteurs.

---

## 🧠 À retenir

- CSS pilote l’apparence → **cascade, spécificité, héritage**.  
- **Box model** bien compris = 80% des soucis évités.  
- **Flexbox** (1D) & **Grid** (2D) pour les layouts modernes.  
- **Responsive** : media queries, mobile‑first.  
- Outils : DevTools, Autoprefixer, Can I use.

---

## 📚 Pour aller plus loin

- 🌐 [MDN – Apprendre le CSS](https://developer.mozilla.org/fr/docs/Learn/CSS)  
- 📘 [Guide Flexbox complet (MDN)](https://developer.mozilla.org/fr/docs/Web/CSS/CSS_flexible_box_layout)  
- 🧮 [Guide CSS Grid (MDN)](https://developer.mozilla.org/fr/docs/Web/CSS/CSS_grid_layout)  
- ✅ [Can I use](https://caniuse.com/) – compatibilité navigateurs
