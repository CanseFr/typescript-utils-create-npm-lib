# 📦 Créer et publier une librairie TypeScript sur npm

> Un guide complet et pratique pour apprendre à créer, configurer, compiler et publier votre propre package npm en **TypeScript**.
> Vous apprendrez aussi à gérer les versions (Semantic Versioning), comprendre les fichiers `.d.ts`, et éviter les erreurs courantes.

---

## 🚀 Objectif

Ce repo a pour but de montrer **comment transformer du code TypeScript réutilisable en une librairie npm publique**.
Vous pourrez ensuite :

* Créer vos propres packages réutilisables.
* Publier vos utilitaires et types pour d’autres projets.
* Comprendre les bases du packaging npm et de TypeScript.

---

## 📑 Sommaire

1. [Prérequis](#-prérequis)
2. [Initialisation du projet](#-1-initialisation-du-projet)
3. [Configurer TypeScript](#-2-configurer-typescript)
4. [Écrire le code](#-3-écrire-le-code)
5. [Configurer `package.json`](#-4-configurer-packagejson)
6. [Compiler la librairie](#-5-compiler-la-librairie)
7. [Publier sur npm](#-6-publier-sur-npm)
8. [Gestion des versions (SemVer)](#-7-gestion-des-versions-semver)
9. [Comprendre les fichiers `.d.ts`](#-8-comprendre-les-fichiers-dts)
10. [Erreurs courantes et solutions](#-9-erreurs-courantes-et-solutions)
11. [Utiliser le package dans un projet](#-10-utiliser-le-package-dans-un-projet)

---

## ✅ Prérequis

* Node.js et npm installés (`node -v && npm -v`)
* Un compte [npm](https://www.npmjs.com/signup)
* Connaissances de base en TypeScript
* Git pour versionner le projet

---

## 🔨 1. Initialisation du projet

```bash
mkdir mon-package-utils
cd mon-package-utils
npm init -y
git init
git add .
git commit -m "chore: init project"
```

---

## ⚙️ 2. Configurer TypeScript

Installer les dépendances :

```bash
npm install --save-dev typescript @types/node
```

Créer un fichier `tsconfig.json` :

```json
{
  "compilerOptions": {
    "target": "ES2019",
    "module": "CommonJS",
    "declaration": true,
    "emitDeclarationOnly": false,
    "outDir": "dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

👉 Explication rapide :

* `declaration: true` → génère les fichiers `.d.ts` pour les types.
* `outDir: "dist"` → place les fichiers compilés dans `dist/`.
* `strict: true` → active les vérifications strictes de TypeScript.

---

## ✍️ 3. Écrire le code

Organisation :

```
src/
  index.ts
  helpers.ts
  types.ts
```

**helpers.ts**

```ts
export function isEmpty(value: unknown): boolean {
  return value === null || value === undefined || value === "";
}
```

**types.ts**

```ts
export interface Identifiable {
  id: string | number;
}
```

**index.ts**

```ts
export * from "./helpers";
export * from "./types";
```

👉 `index.ts` agit comme **point d’entrée unique**, ce qui permet d’importer depuis la racine du package plutôt que fichier par fichier.

---

## 📦 4. Configurer `package.json`

```json
{
  "name": "mon-package-utils",
  "version": "1.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "files": ["dist/**/*"],
  "scripts": {
    "build": "tsc"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/tonpseudo/mon-package-utils.git"
  },
  "keywords": ["typescript", "npm", "utils", "helpers"],
  "author": "Ton Nom <tonmail@example.com>",
  "license": "MIT"
}
```

👉 Points clés :

* `"main"` → point d’entrée JS.
* `"types"` → point d’entrée des définitions TypeScript.
* `"files"` → limite la publication au dossier `dist/`.

---

## ⚡ 5. Compiler la librairie

```bash
npm run build
```

Tu dois obtenir dans `dist/` :

```
dist/
  index.js
  index.d.ts
  helpers.js
  helpers.d.ts
  types.js
  types.d.ts
```

---

## 🌍 6. Publier sur npm

1. Connexion :

   ```bash
   npm login
   ```
2. Publication :

   ```bash
   npm publish --access public
   ```

⚠️ Le nom doit être unique.

* Si déjà pris → changer `"name"` ou utiliser un **scope** (`"@tonpseudo/utils"`).

---

## 🔢 7. Gestion des versions (SemVer)

Les versions suivent la règle `MAJOR.MINOR.PATCH` :

| Type  | Quand l’utiliser ?                       | Exemple           |
| ----- | ---------------------------------------- | ----------------- |
| PATCH | Correction de bug sans changer l’API     | `1.0.0` → `1.0.1` |
| MINOR | Nouvelle fonctionnalité rétro-compatible | `1.0.1` → `1.1.0` |
| MAJOR | Changement cassant (API modifiée)        | `1.1.0` → `2.0.0` |

Commandes utiles :

```bash
npm version patch   # bugfix
npm version minor   # nouvelle fonctionnalité
npm version major   # changement cassant
```

---

## 📖 8. Comprendre les fichiers `.d.ts`

* `.js` → code exécutable
* `.d.ts` → déclarations de types (interfaces, signatures de fonctions)

👉 Exemple :

```ts
export declare function isEmpty(value: unknown): boolean;
```

Ces fichiers :

* Permettent l’autocomplétion dans VS Code.
* Évitent les erreurs de typage côté utilisateur.
* Servent de contrat entre ta lib et ceux qui l’utilisent.

---

## 🐞 9. Erreurs courantes et solutions

* **EPRIVATE** :

  ```
  npm error This package has been marked as private
  ```

  👉 Vérifie que `"private": true` n’est pas présent dans `package.json`.

* **Impossible d’importer des éléments** :
  Vérifie :

    * `tsconfig.json` → `"declaration": true`, `"outDir": "dist"`.
    * `package.json` → `"main"` et `"types"` pointent bien vers `dist/index.js` et `dist/index.d.ts`.

* **Version déjà publiée** :

  ```
  npm error You cannot publish over the previously published versions
  ```

  👉 Utilise `npm version patch | minor | major` avant de republier.

---

## 🛠️ 10. Utiliser le package dans un projet

Installer la dernière version :

```bash
npm install mon-package-utils@latest
```

Exemple d’utilisation :

```ts
import { isEmpty, Identifiable } from "mon-package-utils";

const user: Identifiable = { id: 1 };
console.log(isEmpty("")); // false
```

---

## 📌 Conclusion

Avec ce guide, vous pouvez :

* Créer une librairie TypeScript réutilisable.
* Compiler et générer automatiquement vos types.
* Publier votre package sur npm.
* Gérer vos versions proprement avec SemVer.

👉 Cette démarche est idéale pour factoriser vos helpers, types et outils afin de les réutiliser dans plusieurs projets.
