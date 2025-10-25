# Automatisation et Tokens

## 🎯 Objectifs

- ✓ Automatiser la génération de tokens
- ✓ Synchroniser design et code
- ✓ Mettre en place un workflow CI/CD
- ✓ Optimiser la collaboration

---

## 🔄 Style Dictionary

### Installation

```bash
npm install style-dictionary
```

### Configuration

```js
// build-tokens.js
const StyleDictionary = require('style-dictionary');

StyleDictionary.extend({
  source: ['tokens/**/*.json'],
  platforms: {
    css: {
      transformGroup: 'css',
      buildPath: 'build/css/',
      files: [{
        destination: 'variables.css',
        format: 'css/variables'
      }]
    },
    js: {
      transformGroup: 'js',
      buildPath: 'build/js/',
      files: [{
        destination: 'tokens.js',
        format: 'javascript/es6'
      }]
    },
    ios: {
      transformGroup: 'ios',
      buildPath: 'build/ios/',
      files: [{
        destination: 'tokens.swift',
        format: 'ios-swift/class.swift'
      }]
    }
  }
}).buildAllPlatforms();
```

### Tokens JSON

```json
{
  "color": {
    "primary": {
      "500": { "value": "#3B82F6" }
    }
  },
  "spacing": {
    "4": { "value": "16px" }
  }
}
```

### Build

```bash
node build-tokens.js
```

Génère:
- `build/css/variables.css`
- `build/js/tokens.js`
- `build/ios/tokens.swift`

---

## 🎨 Figma to Code

### Figma Tokens Plugin

1. Installer "Figma Tokens"
2. Exporter les tokens en JSON
3. Synchroniser avec GitHub

### Automation avec GitHub Actions

```yaml
# .github/workflows/sync-tokens.yml
name: Sync Design Tokens

on:
  push:
    paths:
      - 'tokens/**/*.json'

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install
      
      - name: Build tokens
        run: npm run build:tokens
      
      - name: Commit changes
        run: |
          git config --global user.name 'Token Bot'
          git config --global user.email 'bot@example.com'
          git add build/
          git commit -m 'chore: update tokens'
          git push
```

---

## 🚀 CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      
      - name: Install
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Type check
        run: npm run type-check
      
      - name: Test
        run: npm test
      
      - name: Build Storybook
        run: npm run build-storybook
      
      - name: Deploy Storybook
        if: github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./storybook-static
```

---

## 📦 Versioning automatique

```bash
# Semantic Release
npm install --save-dev semantic-release
```

```json
// package.json
{
  "scripts": {
    "release": "semantic-release"
  },
  "release": {
    "branches": ["main"],
    "plugins": [
      "@semantic-release/commit-analyzer",
      "@semantic-release/release-notes-generator",
      "@semantic-release/changelog",
      "@semantic-release/npm",
      "@semantic-release/github"
    ]
  }
}
```

Commit messages:
```
feat: add new button variant
fix: correct spacing in card component
BREAKING CHANGE: remove deprecated prop
```

---

## 🔄 Figma API Integration

```ts
// sync-figma.ts
import fetch from 'node-fetch';

const FIGMA_TOKEN = process.env.FIGMA_TOKEN;
const FILE_KEY = 'your-file-key';

async function fetchFigmaStyles() {
  const response = await fetch(
    `https://api.figma.com/v1/files/${FILE_KEY}/styles`,
    {
      headers: {
        'X-Figma-Token': FIGMA_TOKEN
      }
    }
  );
  
  const data = await response.json();
  return data.meta.styles;
}

async function syncTokens() {
  const styles = await fetchFigmaStyles();
  
  // Transform to tokens format
  const tokens = transformStylesToTokens(styles);
  
  // Write to file
  fs.writeFileSync(
    'tokens/colors.json',
    JSON.stringify(tokens, null, 2)
  );
  
  console.log('✅ Tokens synchronized');
}

syncTokens();
```

---

## 🤖 Automated Visual Regression

```bash
npm install --save-dev chromatic
```

```yaml
# .github/workflows/chromatic.yml
name: Chromatic

on: push

jobs:
  chromatic:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
      
      - run: npm ci
      
      - uses: chromaui/action@v1
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
```

---

## 📚 Points clés

1. **Style Dictionary** : Multi-platform tokens
2. **Figma Tokens** : Export automatique
3. **GitHub Actions** : CI/CD
4. **Semantic Release** : Versioning auto
5. **Chromatic** : Visual regression
6. **Figma API** : Sync programmatique
7. **Monorepo** : Turborepo/Nx
8. **Changesets** : Changelog automatique

---

*Chapitre 05.5 | Automatisation et Tokens*

