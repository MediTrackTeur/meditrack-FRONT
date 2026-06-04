# MediTrack-front

Application **Angular 17** (standalone components) de suivi de stock de medicaments.
Ce depot est un **starter** pour une formation CI/CD GitHub Actions : vous allez ecrire
vous-meme les workflows dans `.github/workflows/`.

## Prerequis

- **Node.js 18+** (et npm)
- Un navigateur **Chrome / Chromium** pour executer les tests unitaires (Karma)

## Installation

```bash
npm install
```

## Scripts

| Script             | Description                                              |
| ------------------ | -------------------------------------------------------- |
| `npm start`        | Lance le serveur de dev (`ng serve`) sur http://localhost:4200 |
| `npm run build`    | Build de production dans `dist/meditrack-front`          |
| `npm test`         | Tests unitaires Karma en mode watch (Chrome)             |
| `npm run test:ci`  | Tests unitaires une seule fois en `ChromeHeadlessCI`     |
| `npm run lint`     | Analyse statique ESLint                                  |

## Tests en CI

Le script `test:ci` utilise un custom launcher **ChromeHeadlessCI** defini dans
`karma.conf.js` (flags `--no-sandbox --disable-gpu --headless`). C'est ce launcher
qu'il faut utiliser dans GitHub Actions, ou un Chrome headless est present sur les
runners `ubuntu-latest`.

## A faire par l'apprenant

Les workflows GitHub Actions ne sont **pas** fournis. A vous de les creer dans
`.github/workflows/` :

- **`ci-angular`** : `npm ci`, `npm run lint`, `npm run test:ci`, `npm run build`
  (idealement avec une **matrice** de versions Node). Publier le dossier **`dist/`**
  comme **artifact**.
- **`deploy-pages`** : publier le contenu de `dist/meditrack-front/browser` sur
  **GitHub Pages**.

> L'artifact a publier est le dossier `dist/` produit par `npm run build`.

---

# Rendu TP - Partie 2

# MediTrack Front

Frontend Angular 17 de l'application MediTrack, interfaçant avec l'API pour la gestion de stock et le suivi des prescriptions en officine.

### Configuration Karma

J'ai modifié `karma.conf.js` pour utiliser `ChromeHeadlessCI` à la place de `ChromeHeadless`. La différence est importante : `ChromeHeadlessCI` inclut les flags `--no-sandbox` et `--disable-gpu` qui sont obligatoires sur les runners GitHub qui n'ont pas d'interface graphique. 
Sans ça, Chrome refuse de démarrer en CI.

J'ai aussi passé `restartOnFileChange` à `false` car en CI il n'y a pas de fichiers qui changent — laisser cette option à `true` peut bloquer le runner indéfiniment.

### Workflow CI Angular

J'ai créé `.github/workflows/ci-angular.yml` avec une matrice sur Node 18 et Node 20. Ça me permet de vérifier que le projet fonctionne sur les deux versions LTS actuelles de Node en parallèle.

J'ai ajouté `fail-fast: false` pour que si un job échoue, l'autre continue jusqu'au bout. Sans ça GitHub annule tous les jobs dès le premier échec, ce qui m'empêche de voir l'état complet de la matrice.

Le build de production est uploadé comme artifact avec un nom distinct par version Node (`dist-node-18`, `dist-node-20`). 
Ça me permet de télécharger et vérifier le bundle sans avoir à rebuilder localement.