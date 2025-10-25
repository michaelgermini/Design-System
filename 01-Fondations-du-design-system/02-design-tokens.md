# Design Tokens : L'ADN de votre Design System

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Comprendre ce que sont les design tokens et leur rôle
- ✓ Créer une architecture de tokens scalable
- ✓ Implémenter des tokens en JSON, CSS et JavaScript
- ✓ Gérer les tokens pour plusieurs plateformes
- ✓ Mettre en place un système de transformation automatique

---

## 📖 Qu'est-ce qu'un Design Token ?

### Définition

> **Un design token est une variable nommée qui stocke une valeur de design atomique et sémantique.**

```
Token = Nom + Valeur + Contexte

Exemple :
color-primary-500 = #3B82F6 (utilisé pour les CTAs principaux)
```

### Analogie

```
Design Tokens ≈ Atomes
Composants   ≈ Molécules
Patterns     ≈ Organismes
Templates    ≈ Écosystèmes
```

Les tokens sont les **briques élémentaires** indivisibles de votre design system.

---

## 🏗️ Architecture des Tokens

### Hiérarchie à 3 niveaux

```
NIVEAU 1 : Core Tokens (valeurs brutes)
────────────────────────────────────────
color-blue-500: #3B82F6
spacing-4: 16px
font-size-base: 16px
         ↓

NIVEAU 2 : Semantic Tokens (rôle fonctionnel)
────────────────────────────────────────
color-primary: {color-blue-500}
spacing-component-padding: {spacing-4}
typography-body: {font-size-base}
         ↓

NIVEAU 3 : Component Tokens (contexte spécifique)
────────────────────────────────────────
button-primary-background: {color-primary}
button-padding-x: {spacing-component-padding}
button-font-size: {typography-body}
```

### Exemple concret

```javascript
// tokens.js
export const tokens = {
  // NIVEAU 1 : Core tokens
  core: {
    colors: {
      blue: {
        50: '#EEF2FF',
        500: '#3B82F6',
        900: '#1E3A8A'
      },
      gray: {
        50: '#F9FAFB',
        500: '#6B7280',
        900: '#111827'
      }
    },
    spacing: {
      1: '4px',
      2: '8px',
      4: '16px',
      8: '32px'
    },
    fontSize: {
      sm: '14px',
      base: '16px',
      lg: '18px'
    }
  },
  
  // NIVEAU 2 : Semantic tokens
  semantic: {
    colors: {
      primary: '{core.colors.blue.500}',
      text: {
        primary: '{core.colors.gray.900}',
        secondary: '{core.colors.gray.500}'
      },
      background: {
        primary: '#FFFFFF',
        secondary: '{core.colors.gray.50}'
      }
    },
    spacing: {
      xs: '{core.spacing.1}',
      sm: '{core.spacing.2}',
      md: '{core.spacing.4}',
      lg: '{core.spacing.8}'
    }
  },
  
  // NIVEAU 3 : Component tokens
  components: {
    button: {
      primary: {
        bg: '{semantic.colors.primary}',
        color: '#FFFFFF',
        padding: '{semantic.spacing.md} {semantic.spacing.lg}',
        fontSize: '{core.fontSize.base}'
      }
    }
  }
};
```

---

## 🎨 Catégories de Tokens

### 1. Color Tokens

```javascript
const colorTokens = {
  // Palette primaire
  primary: {
    50: '#EEF2FF',
    100: '#E0E7FF',
    200: '#C7D2FE',
    300: '#A5B4FC',
    400: '#818CF8',
    500: '#6366F1',  // Base
    600: '#4F46E5',
    700: '#4338CA',
    800: '#3730A3',
    900: '#312E81',
    950: '#1E1B4B'
  },
  
  // Couleurs sémantiques
  success: '#10B981',
  warning: '#F59E0B',
  error: '#EF4444',
  info: '#3B82F6',
  
  // Neutrals
  gray: { /* 50-950 */ },
  
  // État
  states: {
    hover: 'rgba(0, 0, 0, 0.05)',
    active: 'rgba(0, 0, 0, 0.1)',
    disabled: 'rgba(0, 0, 0, 0.3)',
    focus: '#3B82F6'
  }
};
```

#### Formule de génération de palette

```
Pour générer des variations d'une couleur de base :

Lightness(n) = L_base × (1 + (n - 500) × factor)

où :
L_base = Luminosité de la couleur base (500)
n = Niveau (50, 100, ..., 900, 950)
factor = 0.08 (8% par niveau)

Exemple :
Si primary-500 a L = 50%
→ primary-400 : L = 50 × (1 + 0.08) = 54%
→ primary-600 : L = 50 × (1 - 0.08) = 46%
```

### 2. Spacing Tokens

```javascript
const spacingTokens = {
  // Échelle en base 4
  0: '0',
  1: '4px',
  2: '8px',
  3: '12px',
  4: '16px',
  5: '20px',
  6: '24px',
  8: '32px',
  10: '40px',
  12: '48px',
  16: '64px',
  20: '80px',
  24: '96px',
  32: '128px',
  40: '160px',
  48: '192px',
  
  // Semantic
  component: {
    padding: {
      xs: '8px',
      sm: '12px',
      md: '16px',
      lg: '24px'
    },
    gap: {
      xs: '4px',
      sm: '8px',
      md: '16px',
      lg: '24px'
    }
  }
};
```

#### Formule d'espacement harmonique

```
S(n) = base × (ratio^level ou linear)

Base 4 (linéaire) :
S(n) = 4 × n
S(1) = 4px, S(2) = 8px, S(4) = 16px

Base 4 (exponentielle après 8) :
S(n≤8) = 4 × n
S(n>8) = 8 × 1.5^(n-8)
```

### 3. Typography Tokens

```javascript
const typographyTokens = {
  fontFamily: {
    sans: '"Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif',
    serif: '"Merriweather", Georgia, serif',
    mono: '"JetBrains Mono", "Fira Code", monospace'
  },
  
  fontSize: {
    xs: '12px',
    sm: '14px',
    base: '16px',
    lg: '18px',
    xl: '20px',
    '2xl': '24px',
    '3xl': '30px',
    '4xl': '36px',
    '5xl': '48px',
    '6xl': '60px',
    '7xl': '72px'
  },
  
  fontWeight: {
    thin: 100,
    extralight: 200,
    light: 300,
    normal: 400,
    medium: 500,
    semibold: 600,
    bold: 700,
    extrabold: 800,
    black: 900
  },
  
  lineHeight: {
    none: 1,
    tight: 1.25,
    snug: 1.375,
    normal: 1.5,
    relaxed: 1.625,
    loose: 2
  },
  
  letterSpacing: {
    tighter: '-0.05em',
    tight: '-0.025em',
    normal: '0',
    wide: '0.025em',
    wider: '0.05em',
    widest: '0.1em'
  }
};
```

### 4. Radius Tokens

```javascript
const radiusTokens = {
  none: '0',
  sm: '4px',
  md: '6px',
  lg: '8px',
  xl: '12px',
  '2xl': '16px',
  '3xl': '24px',
  full: '9999px'
};
```

### 5. Shadow Tokens

```javascript
const shadowTokens = {
  xs: '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
  sm: '0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06)',
  md: '0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)',
  lg: '0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)',
  xl: '0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)',
  '2xl': '0 25px 50px -12px rgba(0, 0, 0, 0.25)',
  inner: 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.06)',
  none: '0 0 #0000'
};
```

### 6. Animation Tokens

```javascript
const animationTokens = {
  duration: {
    instant: '0ms',
    fast: '150ms',
    normal: '250ms',
    slow: '350ms',
    slower: '500ms'
  },
  
  easing: {
    linear: 'linear',
    in: 'cubic-bezier(0.4, 0, 1, 1)',
    out: 'cubic-bezier(0, 0, 0.2, 1)',
    inOut: 'cubic-bezier(0.4, 0, 0.2, 1)',
    bounce: 'cubic-bezier(0.68, -0.55, 0.265, 1.55)'
  }
};
```

---

## 💻 Formats d'implémentation

### 1. Format JSON (source unique)

```json
{
  "color": {
    "primary": {
      "500": {
        "value": "#3B82F6",
        "type": "color",
        "description": "Couleur primaire principale"
      }
    }
  },
  "spacing": {
    "4": {
      "value": "16px",
      "type": "dimension"
    }
  },
  "typography": {
    "fontSize": {
      "base": {
        "value": "16px",
        "type": "dimension"
      }
    }
  }
}
```

### 2. CSS Variables

```css
:root {
  /* Colors */
  --color-primary-50: #EEF2FF;
  --color-primary-500: #3B82F6;
  --color-primary-900: #1E3A8A;
  
  /* Spacing */
  --spacing-1: 4px;
  --spacing-2: 8px;
  --spacing-4: 16px;
  
  /* Typography */
  --font-family-sans: "Inter", sans-serif;
  --font-size-base: 16px;
  --line-height-normal: 1.5;
  
  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  
  /* Radius */
  --radius-sm: 4px;
  --radius-md: 6px;
  --radius-full: 9999px;
}

/* Semantic tokens */
:root {
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-500);
  --color-background: #FFFFFF;
  --color-background-secondary: var(--color-gray-50);
}

/* Component tokens */
:root {
  --button-primary-bg: var(--color-primary-500);
  --button-primary-color: #FFFFFF;
  --button-padding-x: var(--spacing-4);
  --button-padding-y: var(--spacing-2);
}
```

### 3. JavaScript/TypeScript

```typescript
// tokens.ts
export const tokens = {
  color: {
    primary: {
      50: '#EEF2FF',
      500: '#3B82F6',
      900: '#1E3A8A'
    }
  },
  spacing: {
    1: '4px',
    2: '8px',
    4: '16px'
  }
} as const;

// Types auto-générés
export type ColorToken = keyof typeof tokens.color;
export type SpacingToken = keyof typeof tokens.spacing;

// Helper pour accès type-safe
export function getToken<T extends keyof typeof tokens>(
  category: T,
  path: string
): string {
  // Implementation
}

// Usage
const primaryColor = getToken('color', 'primary.500');
```

### 4. SCSS Variables

```scss
// _tokens.scss

// Colors
$color-primary-50: #EEF2FF;
$color-primary-500: #3B82F6;
$color-primary-900: #1E3A8A;

// Spacing
$spacing-1: 4px;
$spacing-2: 8px;
$spacing-4: 16px;

// Maps pour itération
$colors: (
  'primary-50': $color-primary-50,
  'primary-500': $color-primary-500,
  'primary-900': $color-primary-900
);

$spacings: (
  '1': $spacing-1,
  '2': $spacing-2,
  '4': $spacing-4
);

// Fonction helper
@function token($category, $name) {
  @if $category == 'color' {
    @return map-get($colors, $name);
  }
  @if $category == 'spacing' {
    @return map-get($spacings, $name);
  }
}
```

---

## 🔄 Transformation de tokens (Style Dictionary)

### Installation et configuration

```bash
npm install style-dictionary --save-dev
```

```javascript
// build-tokens.js
const StyleDictionary = require('style-dictionary');

StyleDictionary.extend({
  source: ['tokens/**/*.json'],
  platforms: {
    css: {
      transformGroup: 'css',
      buildPath: 'build/css/',
      files: [{
        destination: 'tokens.css',
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
    },
    android: {
      transformGroup: 'android',
      buildPath: 'build/android/',
      files: [{
        destination: 'tokens.xml',
        format: 'android/resources'
      }]
    }
  }
}).buildAllPlatforms();
```

### Structure des fichiers source

```
tokens/
├── color.json
├── spacing.json
├── typography.json
└── semantic/
    ├── colors.json
    └── components.json
```

```json
// tokens/color.json
{
  "color": {
    "primary": {
      "50": { "value": "#EEF2FF" },
      "500": { "value": "#3B82F6" },
      "900": { "value": "#1E3A8A" }
    }
  }
}
```

### Résultat de transformation

```css
/* build/css/tokens.css */
:root {
  --color-primary-50: #EEF2FF;
  --color-primary-500: #3B82F6;
  --color-primary-900: #1E3A8A;
}
```

```javascript
// build/js/tokens.js
export const colorPrimary50 = '#EEF2FF';
export const colorPrimary500 = '#3B82F6';
export const colorPrimary900 = '#1E3A8A';
```

```swift
// build/ios/tokens.swift
public class Tokens {
    public static let colorPrimary50 = UIColor(hex: "#EEF2FF")
    public static let colorPrimary500 = UIColor(hex: "#3B82F6")
    public static let colorPrimary900 = UIColor(hex: "#1E3A8A")
}
```

---

## 🎯 Conventions de nommage

### Anatomie d'un nom de token

```
[category]-[concept]-[variant]-[state]

Exemples :
color-primary-500
color-text-primary
spacing-component-padding-md
button-primary-background-hover
```

### Règles de nommage

| Règle | ❌ Mauvais | ✓ Bon |
|-------|-----------|-------|
| **Kebab-case** | `colorPrimary500` | `color-primary-500` |
| **Descriptif** | `blue` | `color-primary-500` |
| **Hiérarchique** | `primary-color-500` | `color-primary-500` |
| **Sémantique** | `color-333` | `color-text-primary` |
| **Pas de valeur** | `color-16px` | `spacing-4` |

### Formule de qualité du nommage

```
Q = (S + C + H) / 3

où :
S = Score sémantique (0-10)
C = Score de cohérence (0-10)
H = Score hiérarchique (0-10)

Objectif : Q > 8
```

---

## 🌓 Tokens pour le theming

### Mode sombre avec tokens

```css
:root {
  /* Light mode (default) */
  --color-background: #FFFFFF;
  --color-text: #111827;
  --color-border: #E5E7EB;
}

[data-theme="dark"] {
  /* Dark mode */
  --color-background: #111827;
  --color-text: #F9FAFB;
  --color-border: #374151;
}

/* Usage (identique peu importe le theme) */
body {
  background: var(--color-background);
  color: var(--color-text);
}
```

### Tokens dynamiques en JavaScript

```typescript
// theme-tokens.ts
export const lightTheme = {
  background: '#FFFFFF',
  text: '#111827',
  primary: '#3B82F6'
};

export const darkTheme = {
  background: '#111827',
  text: '#F9FAFB',
  primary: '#60A5FA'
};

// theme-provider.tsx
export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const tokens = theme === 'light' ? lightTheme : darkTheme;
  
  useEffect(() => {
    Object.entries(tokens).forEach(([key, value]) => {
      document.documentElement.style.setProperty(`--${key}`, value);
    });
  }, [theme, tokens]);
  
  return <>{children}</>;
}
```

---

## ⚠️ Erreurs courantes

### 1. Trop de tokens

❌ **Mauvais** :
```javascript
// 50 nuances de bleu...
color-blue-1: '#F0F9FF'
color-blue-2: '#E1F3FF'
color-blue-3: '#D2EDFF'
// ... 47 autres ...
```

✓ **Bon** :
```javascript
// Palette standard
color-primary-50 à color-primary-950 (10-11 valeurs)
```

### 2. Tokens trop spécifiques

❌ **Mauvais** :
```javascript
button-login-background-hover-active-desktop: '#1E40AF'
```

✓ **Bon** :
```javascript
color-primary-700: '#1E40AF'
// Utilisé par : button-primary-background-hover
```

### 3. Pas de semantic layer

❌ **Mauvais** (usage direct) :
```css
.button {
  background: var(--color-blue-500);
}
```

✓ **Bon** (via semantic token) :
```css
:root {
  --button-primary-bg: var(--color-blue-500);
}

.button {
  background: var(--button-primary-bg);
}
```

---

## ✓ Bonnes pratiques

### 1. Hiérarchie claire

```
Core → Semantic → Component
(valeurs brutes → rôle → contexte)
```

### 2. Référencement

```javascript
// Token référence un autre token
{
  "color-primary": { "value": "{color.blue.500}" },
  "button-bg": { "value": "{color-primary}" }
}
```

### 3. Documentation inline

```json
{
  "color-primary-500": {
    "value": "#3B82F6",
    "description": "Couleur primaire principale - Actions importantes",
    "usage": ["buttons", "links", "icons"],
    "wcag": {
      "onWhite": "AAA",
      "onBlack": "AA"
    }
  }
}
```

### 4. Versioning des tokens

```json
{
  "$schema": "https://design-tokens.org/schema.json",
  "$version": "2.0.0",
  "tokens": { /* ... */ }
}
```

---

## 📊 Métriques de tokens

### Nombre optimal de tokens

```
Formule indicative :
N_tokens = 100 + (50 × N_themes) + (20 × N_platforms)

où :
N_themes = Nombre de thèmes (light, dark, etc.)
N_platforms = Nombre de plateformes (web, iOS, Android)

Exemple (1 theme, 1 platform) :
N = 100 + 50×1 + 20×1 = 170 tokens
```

### Taux d'utilisation

```
Taux_utilisation = (Tokens_utilisés / Tokens_définis) × 100

Objectif : > 80%

Si < 60% : Trop de tokens inutiles
```

---

## 📊 Synthèse visuelle

```
ARCHITECTURE DES DESIGN TOKENS
════════════════════════════════════════════════════════

┌─────────────────────────────────────────────────────┐
│ NIVEAU 1 : CORE TOKENS (valeurs atomiques)         │
│ color-blue-500: #3B82F6                             │
│ spacing-4: 16px                                     │
└──────────────────┬──────────────────────────────────┘
                   │ référence
┌──────────────────▼──────────────────────────────────┐
│ NIVEAU 2 : SEMANTIC TOKENS (rôle)                  │
│ color-primary: {color-blue-500}                     │
│ spacing-md: {spacing-4}                             │
└──────────────────┬──────────────────────────────────┘
                   │ référence
┌──────────────────▼──────────────────────────────────┐
│ NIVEAU 3 : COMPONENT TOKENS (contexte)             │
│ button-primary-bg: {color-primary}                  │
│ button-padding: {spacing-md}                        │
└─────────────────────────────────────────────────────┘

TRANSFORMATION MULTI-PLATEFORME
═══════════════════════════════

tokens.json ──┬──→ CSS Variables
              ├──→ JavaScript/TS
              ├──→ SCSS
              ├──→ Swift (iOS)
              ├──→ Kotlin (Android)
              └──→ JSON (documentation)
```

---

## ✍️ Exercice pratique

Créez vos premiers tokens :

```json
{
  "color": {
    "primary": {
      "50": { "value": "___________" },
      "500": { "value": "___________" },
      "900": { "value": "___________" }
    }
  },
  "spacing": {
    "1": { "value": "___________" },
    "2": { "value": "___________" },
    "4": { "value": "___________" }
  },
  "semantic": {
    "button": {
      "primary": {
        "background": { "value": "{color.primary.___}" }
      }
    }
  }
}
```

---

## 📚 Points clés à retenir

1. **3 niveaux** : Core → Semantic → Component
2. **Single source of truth** : tokens.json
3. **Transformation automatique** : Style Dictionary
4. **Nommage cohérent** : category-concept-variant
5. **Référencement** : tokens se référencent entre eux
6. **80%+ d'utilisation** : pas de tokens inutiles
7. **Documentation** : chaque token documenté

---

## 🎯 Prochaines étapes

Maintenant que vous maîtrisez les tokens, nous allons les appliquer aux **couleurs, typographie et espacement** de manière détaillée.

---

*Chapitre 01.2 | Design Tokens*

