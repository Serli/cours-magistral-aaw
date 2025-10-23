# 🧰 TypeScript — Les bases (Node.js & React)
**Master Informatique – Architecture des Applications Web**

---

## 🎯 Objectif du rappel

- Comprendre **TypeScript** (sur-ensemble typé de JavaScript).  
- Savoir l’utiliser en **Node.js** (API/Express) et **React** (front).  
- Configurer un **tsconfig** propre et exécuter en dev/build.

> TypeScript ajoute un **système de types statiques** vérifié à la compilation (pas à l’exécution).

---

## ⚙️ Installation rapide

```bash
npm i -D typescript
npx tsc --init
```

- Fichier `tsconfig.json` généré.  
- Compilation : `npx tsc` → produit du JS (par défaut dans le dossier courant ou `outDir`).

**Exécution en dev :**
```bash
npm i -D tsx         # ou ts-node/ts-node-dev
npx tsx src/index.ts # exécute directement TS (ESM-friendly)
```

---

## 🧱 Types de base & annotations

```ts
let message: string = "Hello";
let count: number = 42;
let ok: boolean = true;
let ids: number[] = [1, 2, 3];
let tuple: [string, number] = ["age", 25];

type UserId = string | number; // union
let id: UserId = 123;
```

- **Type inference** : souvent pas besoin d’annoter (`const x = 1`).  
- **Union** = valeur d’un type **ou** d’un autre.

---

## 🧩 Objets, interfaces, types

```ts
type User = { id: number; name: string; email?: string }; // ? = optionnel

interface Product {
  id: string;
  price: number;
}
```

- `type` et `interface` sont proches ; **`type`** est plus flexible (unions).  
- `interface` se **merge** et s’**étend** facilement (`extends`).

---

## 🧠 Fonctions & génériques

```ts
function add(a: number, b: number): number { return a + b; }

function first<T>(arr: T[]): T | undefined { return arr[0]; }
const x = first<string>(["a", "b"]); // inférence possible
```

- Type de retour explicite utile pour les API publiques.  
- **Génériques** : réutilisables et précis.

---

## 🔎 Narrowing & guards

```ts
function len(x: string | string[]) {
  if (typeof x === "string") return x.length; // string
  return x.length; // string[]
}

function isNumber(v: unknown): v is number {
  return typeof v === "number";
}
```

- **Narrowing** : affiner le type via `typeof`, `in`, `instanceof`, prédicats (`v is Type`).

---

## 🧰 Enums & littéraux

```ts
enum Role { Admin = "ADMIN", User = "USER" }
type Status = "idle" | "loading" | "error" | "success";
```

- Préférer parfois les **littéraux** (`"idle" | "loading"`) aux `enum` pour la compatibilité ESM.

---

## 📦 tsconfig — Node.js (ESM moderne)

`tsconfig.json` (exemple conseillé) :

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Node",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "dist",
    "sourceMap": true,
    "types": ["node"]
  },
  "include": ["src"]
}
```

> Mettre `"type": "module"` dans **package.json** pour des imports ESM natifs.

---

## 🌐 Node.js : serveur Express en TypeScript

Installation :
```bash
npm i express
npm i -D @types/express tsx
```

`src/server.ts` :
```ts
import express, { Request, Response, NextFunction } from "express";

const app = express();
app.use(express.json());

app.get("/health", (req: Request, res: Response) => {
  res.json({ ok: true });
});

app.post("/users", (req: Request, res: Response) => {
  const body = req.body as { name: string };
  if (!body?.name) return res.status(400).json({ error: "name required" });
  res.status(201).json({ id: 1, ...body });
});

// middleware d'erreur typé
app.use((err: unknown, _req: Request, res: Response, _next: NextFunction) => {
  console.error(err);
  res.status(500).json({ error: "internal_error" });
});

app.listen(process.env.PORT ?? 3000, () => {
  console.log("http://localhost:" + (process.env.PORT ?? 3000));
});
```

Run dev :
```bash
npx tsx src/server.ts
```

---

## 🧾 Typage des schémas (validation)

- TypeScript **ne valide pas à l’exécution**.  
- Utiliser une lib de **validation runtime** : `zod`, `yup`, `ajv`.  

```ts
import { z } from "zod";
const UserBody = z.object({ name: z.string().min(1) });
type UserBody = z.infer<typeof UserBody>; // type TS dérivé

app.post("/users", (req, res) => {
  const parse = UserBody.safeParse(req.body);
  if (!parse.success) return res.status(400).json(parse.error);
  const body: UserBody = parse.data;
  res.status(201).json({ id: 1, ...body });
});
```

---

## 🧪 Types asynchrones

```ts
async function fetchUsers(): Promise<{ id: number; name: string }[]> {
  return [{ id: 1, name: "Alice" }];
}
```

- `Promise<T>` : résultat futur typé.  
- `await` garde le type `T` une fois résolu.

---

## 🔐 Variables d’environnement (typées)

```ts
// src/env.ts
const env = {
  NODE_ENV: process.env.NODE_ENV ?? "development",
  PORT: Number(process.env.PORT ?? 3000),
  API_KEY: process.env.API_KEY ?? ""
};
export default env;
```

- Option : générer des types stricts via `zod` (`z.object({...})`).

---

## 🗂️ Structure projet Node + scripts

```
api/
├─ src/
│  ├─ server.ts
│  ├─ routes/
│  │  └─ users.ts
│  └─ env.ts
├─ tsconfig.json
├─ package.json
└─ .env
```

`package.json` (extrait) :

```json
{
  "type": "module",
  "scripts": {
    "dev": "tsx src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js"
  }
}
```

---

## ⚛️ React + TypeScript (Vite)

Création :
```bash
npm create vite@latest my-app -- --template react-swc-ts
cd my-app && npm i && npm run dev
```

**Props typées :**
```tsx
type BadgeProps = { label: string; color?: string };

export function Badge({ label, color = "#0a66ff" }: BadgeProps) {
  return <span style={{ background: color }}>{label}</span>;
}
```

---

## 🔗 Événements & refs (React)

```tsx
import { useRef, useState, useEffect } from "react";

export default function Form() {
  const [name, setName] = useState<string>("");
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => inputRef.current?.focus(), []);

  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    setName(e.target.value);
  }

  function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
    e.preventDefault();
    console.log("Saved", name);
  }

  return (
    <form>
      <input ref={inputRef} value={name} onChange={handleChange} />
      <button onClick={handleClick}>Save</button>
    </form>
  );
}
```

---

## 📡 Fetch typé (React)

```tsx
type User = { id: number; name: string };

async function loadUsers(): Promise<User[]> {
  const res = await fetch("/api/users");
  if (!res.ok) throw new Error("Network error");
  return res.json() as Promise<User[]>;
}
```

- **Castez** le retour JSON (`as Promise<T>`) si nécessaire.  
- Ou utilisez un **schéma runtime** (`zod`) pour valider/typer.

---

## 🧭 Alias de chemins & DX

`tsconfig.json` :
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": { "@/*": ["src/*"] }
  }
}
```

`vite.config.ts` :
```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "node:path";

export default defineConfig({
  plugins: [react()],
  resolve: { alias: { "@": path.resolve(__dirname, "src") } }
});
```

Usage :
```ts
import Button from "@/components/Button";
```

---

## ✅ Bonnes pratiques TypeScript

- `strict: true` dans le tsconfig.  
- Préférer **types précis** (unions, littéraux) aux `any`.  
- Typage **des retours** des fonctions publiques.  
- Ne pas oublier que TS **n’existe pas au runtime** → valider les entrées.  
- Pour React : typer **props**, **events**, **refs**, **fetch**.

---

## ⚠️ Erreurs fréquentes

- Confondre **types** (compile) et **validation** (runtime).  
- `esModuleInterop` manquant → import par défaut casse.  
- Mauvais `module`/`target` (ESM vs CJS) → imports qui échouent.  
- Utiliser `any` partout → perte de sécurité et d’auto-complétion.  
- Oublier `--types node` ou `"types": ["node"]` pour les APIs Node.

---

## 📚 Pour aller plus loin

- 📘 Docs TypeScript : https://www.typescriptlang.org/docs/  
- 🌐 Handbook : https://www.typescriptlang.org/docs/handbook/intro.html  
- ⚛️ React + TS cheatsheets : https://react-typescript-cheatsheet.netlify.app/  
- 🔧 tsx (exécuter TS direct) : https://github.com/esbuild-kit/tsx  
- ✅ Zod (validation runtime) : https://zod.dev/
