# ⚡ JavaScript — Les bases  
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

Revoir les **fondamentaux du langage JavaScript (ES6+)**, utilisé côté navigateur pour :  
- manipuler le **DOM**,  
- gérer les **événements**,  
- interagir avec des **API** (HTTP / fetch).

---

## 🕰️ Brève histoire

- Créé en **1995** par **Brendan Eich** (Netscape).  
- Standardisé sous le nom **ECMAScript** (ES).  
- ES5 (2009) → compatibilité large.  
- ES6 (2015) → `let`, `const`, classes, arrow functions, modules.  
- Aujourd’hui : un langage moderne, modulaire et multi‑plateforme.

---

## 🚀 Introduction

- JavaScript s’exécute **dans le navigateur**.  
- Peut **modifier le DOM**, **réagir aux interactions** et **appeler des APIs**.  

```html
<script>
  console.log("Bonjour le Web !");
</script>
```

> Chaque navigateur contient un **moteur JS** (V8, SpiderMonkey, etc.).

---

## 🔤 Variables et constantes

```js
let nom = "Alice";   // variable (modif possible)
const age = 25;      // constante (non modifiable)
var ancien = "à éviter";
```

⚠️ **Attention fréquente :**
- `var` crée des variables à **portée fonctionnelle** (source d’erreurs).  
- Utiliser `let` ou `const` par défaut.

---

## 🔢 Types de base

- `string` → `"Bonjour"`  
- `number` → `42`, `3.14`  
- `boolean` → `true` / `false`  
- `null`, `undefined`  
- `object`, `array`, `function`

```js
typeof "abc"      // "string"
typeof 42         // "number"
typeof undefined  // "undefined"
```

⚠️ `null` est de type `"object"` pour des raisons historiques.

---

## ⚖️ Opérateurs & comparaisons

```js
1 + 2 * 3       // 7
10 % 3          // 1
"Hello" + " !"  // concaténation

5 == "5"        // true (conversion implicite)
5 === "5"       // false (comparaison stricte)
```

⚠️ **Toujours préférer `===` et `!==`** pour éviter les conversions implicites.

---

## 🔁 Conditions et boucles

```js
if (age >= 18) {
  console.log("Majeur");
} else {
  console.log("Mineur");
}

for (let i = 0; i < 3; i++) {
  console.log(i);
}
```

Autres boucles utiles :
- `while`, `do…while`  
- `for…of` (tableaux), `for…in` (objets)

---

## 🧩 Fonctions

```js
function addition(a, b) {
  return a + b;
}

// Fonction fléchée (ES6)
const mult = (a, b) => a * b;

console.log(addition(2, 3)); // 5
console.log(mult(2, 3));     // 6
```

- Les fonctions peuvent être **déclarées** ou **anonymes**.  
- Elles peuvent retourner n’importe quel type.

⚠️ Les fonctions fléchées n’ont pas leur propre `this`.

---

## 🧠 Portée et hoisting

```js
let x = 10;
function test() {
  let y = 20;
  console.log(x); // accessible
  console.log(y); // ok
}
console.log(y); // ❌ erreur (portée locale)
```

⚠️ **Hoisting :** les déclarations `var` et fonctions sont lues avant exécution.

---

## 📦 Tableaux

```js
const fruits = ["pomme", "banane", "poire"];
fruits.push("orange");

for (const f of fruits) {
  console.log(f.toUpperCase());
}
```

Méthodes courantes :  
- `push`, `pop`, `shift`, `unshift`, `splice`  
- `map`, `filter`, `find`, `forEach`, `reduce`

---

## 🧱 Objets

```js
const user = {
  name: "Alice",
  age: 25,
  greet() { console.log("Bonjour " + this.name); }
};

console.log(user.name); // Alice
user.greet();
```

- Un **objet** regroupe des propriétés (clé/valeur).  
- Les fonctions dans un objet sont appelées **méthodes**.

---

## 🧩 Déstructuration (ES6+)

```js
const user = { name: "Bob", age: 30 };
const { name, age } = user;

const [a, b] = [10, 20];
```

> Simplifie la récupération de données dans les objets et tableaux.

---

## 🌐 Le DOM — Document Object Model

- Chaque page HTML → un **arbre d’objets** manipulable par JS.

```js
document.title = "Nouveau titre";
const btn = document.querySelector("#go");
btn.textContent = "Clique ici";
```

⚠️ `querySelector()` et `querySelectorAll()` sont les plus modernes et flexibles.

---

## 🖱️ Événements

```js
const btn = document.querySelector("button");
btn.addEventListener("click", () => {
  alert("Clic détecté !");
});
```

- `addEventListener(type, callback)` → méthode moderne.  
- Événements fréquents : `click`, `input`, `submit`, `keydown`, `scroll`.  
- `event.preventDefault()` pour bloquer le comportement par défaut.

---

## 🌍 Requêtes réseau — `fetch()`

```js
fetch("https://api.example.com/users")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

- `fetch()` renvoie une **Promise**.  
- `.then()` enchaîne les traitements, `.catch()` gère les erreurs.

Version moderne (async/await) :

```js
const res = await fetch("/api/data");
const data = await res.json();
```

---

## 🧠 Asynchronisme simplifié

JS est **monothread**, mais utilise une **boucle d’événements**.  
> → Quand une opération longue est lancée (ex. `fetch`), elle est déléguée à l’API navigateur.  
> → Le code principal continue, et le résultat revient plus tard (Promise résolue).

---

## 🧑‍💻 Bonnes pratiques

- Toujours utiliser `const` par défaut, `let` si nécessaire.  
- Éviter les variables globales.  
- Indenter clairement (`prettier`, `eslint`).  
- Écrire des fonctions **pures** quand c’est possible.  
- Nommer les variables clairement (`camelCase`).

---

## ⚠️ Erreurs fréquentes

- Utiliser `==` au lieu de `===`.  
- Oublier `await` devant `fetch`.  
- Manipuler le DOM avant `DOMContentLoaded`.  
- Oublier de convertir les types (`parseInt`, `JSON.parse`, etc.).  
- Ne pas gérer les erreurs réseau (`.catch`).

---

## 🧰 Outils modernes

- **Console navigateur** (`Ctrl+Shift+I`) → onglet *Console*.  
- **Sources** → points d’arrêt, pas à pas.  
- **Linter / Formatter** : ESLint, Prettier.  
- **Transpilation** : Babel (pour compatibilité).

---

## 🧠 À retenir

- JS = logique + interactivité dans le navigateur.  
- Syntaxe moderne : `let`, `const`, arrow functions, destructuring.  
- DOM + événements = cœur de l’interactivité.  
- `fetch` et promesses → communication avec APIs.  
- Toujours tester et valider dans la console.

---

## 📚 Pour aller plus loin

- 🌐 [MDN – Apprendre JavaScript](https://developer.mozilla.org/fr/docs/Learn/JavaScript)  
- 📘 [You Don’t Know JS (Kyle Simpson)](https://github.com/getify/You-Dont-Know-JS)  
- 🎓 [JavaScript.info](https://javascript.info/)  
- 🧠 [MDN – Guide sur le DOM](https://developer.mozilla.org/fr/docs/Web/API/Document_Object_Model)
