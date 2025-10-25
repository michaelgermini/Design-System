# Couleurs, Typographie et Espacement

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Créer une palette de couleurs harmonieuse et accessible
- ✓ Définir une échelle typographique cohérente
- ✓ Établir un système d'espacement mathématique
- ✓ Appliquer ces fondations dans vos composants
- ✓ Tester et valider l'accessibilité

---

## 🎨 PARTIE 1 : LES COULEURS

### Théorie des couleurs

#### Modèle HSL : Le plus adapté aux design systems

```
HSL = (Hue, Saturation, Lightness)

H (Teinte) : 0-360° sur le cercle chromatique
  0° = Rouge
  120° = Vert
  240° = Bleu
  
S (Saturation) : 0-100%
  0% = Gris (désaturé)
  100% = Couleur pure
  
L (Luminosité) : 0-100%
  0% = Noir
  50% = Couleur pure
  100% = Blanc
```

#### Conversion HSL → RGB

```
Formules de conversion :

Si L < 0.5 :
  C = 2 × L × S
Sinon :
  C = (2 - 2×L) × S

X = C × (1 - |(H/60°) mod 2 - 1|)
m = L - C/2

Selon la zone de H :
  0° ≤ H < 60°   : RGB' = (C, X, 0)
  60° ≤ H < 120° : RGB' = (X, C, 0)
  120° ≤ H < 180°: RGB' = (0, C, X)
  180° ≤ H < 240°: RGB' = (0, X, C)
  240° ≤ H < 300°: RGB' = (X, 0, C)
  300° ≤ H < 360°: RGB' = (C, 0, X)

RGB_final = (R'+m, G'+m, B'+m) × 255
```

### Création d'une palette complète

#### Algorithme de génération

```javascript
function generatePalette(baseHue, baseSat, baseLightness) {
  const palette = {};
  
  const lightnessSteps = {
    50: 95,
    100: 90,
    200: 80,
    300: 70,
    400: 60,
    500: baseLightness,  // Base (ex: 50)
    600: 45,
    700: 38,
    800: 30,
    900: 22,
    950: 15
  };
  
  for (const [step, lightness] of Object.entries(lightnessSteps)) {
    // Ajuster légèrement la saturation pour les extrêmes
    const adjustedSat = step <= 200 
      ? baseSat * 0.8  // Moins saturé pour les clairs
      : step >= 800
      ? baseSat * 0.9  // Moins saturé pour les foncés
      : baseSat;
    
    palette[step] = `hsl(${baseHue}, ${adjustedSat}%, ${lightness}%)`;
  }
  
  return palette;
}

// Exemple : Générer une palette de bleu
const bluePalette = generatePalette(217, 91, 50);
// Résultat :
// {
//   50: 'hsl(217, 73%, 95%)',
//   500: 'hsl(217, 91%, 50%)',  // #3B82F6
//   900: 'hsl(217, 82%, 22%)',
//   ...
// }
```

#### Palette complète recommandée

```css
:root {
  /* Primary (Bleu) */
  --color-primary-50: #EEF2FF;
  --color-primary-100: #E0E7FF;
  --color-primary-200: #C7D2FE;
  --color-primary-300: #A5B4FC;
  --color-primary-400: #818CF8;
  --color-primary-500: #6366F1;  /* Base */
  --color-primary-600: #4F46E5;
  --color-primary-700: #4338CA;
  --color-primary-800: #3730A3;
  --color-primary-900: #312E81;
  --color-primary-950: #1E1B4B;
  
  /* Gray (Neutre) */
  --color-gray-50: #F9FAFB;
  --color-gray-100: #F3F4F6;
  --color-gray-200: #E5E7EB;
  --color-gray-300: #D1D5DB;
  --color-gray-400: #9CA3AF;
  --color-gray-500: #6B7280;
  --color-gray-600: #4B5563;
  --color-gray-700: #374151;
  --color-gray-800: #1F2937;
  --color-gray-900: #111827;
  --color-gray-950: #030712;
  
  /* Success (Vert) */
  --color-success-50: #F0FDF4;
  --color-success-500: #10B981;
  --color-success-900: #064E3B;
  
  /* Warning (Orange) */
  --color-warning-50: #FFFBEB;
  --color-warning-500: #F59E0B;
  --color-warning-900: #78350F;
  
  /* Error (Rouge) */
  --color-error-50: #FEF2F2;
  --color-error-500: #EF4444;
  --color-error-900: #7F1D1D;
  
  /* Info (Cyan) */
  --color-info-50: #ECFEFF;
  --color-info-500: #06B6D4;
  --color-info-900: #164E63;
}
```

### Couleurs sémantiques

```css
:root {
  /* Text */
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-600);
  --color-text-tertiary: var(--color-gray-400);
  --color-text-disabled: var(--color-gray-300);
  --color-text-on-primary: #FFFFFF;
  
  /* Background */
  --color-bg-primary: #FFFFFF;
  --color-bg-secondary: var(--color-gray-50);
  --color-bg-tertiary: var(--color-gray-100);
  --color-bg-overlay: rgba(0, 0, 0, 0.5);
  
  /* Border */
  --color-border-primary: var(--color-gray-200);
  --color-border-secondary: var(--color-gray-300);
  --color-border-focus: var(--color-primary-500);
  
  /* States */
  --color-state-hover: rgba(0, 0, 0, 0.04);
  --color-state-active: rgba(0, 0, 0, 0.08);
  --color-state-selected: var(--color-primary-50);
  --color-state-disabled-bg: var(--color-gray-100);
}
```

### Harmonies de couleurs

#### Complémentaires

```
Couleur 1 : H
Couleur 2 : (H + 180) mod 360

Exemple :
Bleu (H=217°) + Orange (H=37°)
```

#### Triadiques

```
Couleur 1 : H
Couleur 2 : (H + 120) mod 360
Couleur 3 : (H + 240) mod 360

Exemple :
Rouge (0°) + Vert (120°) + Bleu (240°)
```

#### Analogues

```
Couleur 1 : H - 30
Couleur 2 : H
Couleur 3 : H + 30

Exemple :
Bleu-violet (187°) + Bleu (217°) + Bleu-cyan (247°)
```

---

## 📝 PARTIE 2 : LA TYPOGRAPHIE

### Échelle typographique

#### Méthode du ratio modulaire

```
Formule : size(n) = base × ratio^n

Ratios musicaux :
- 1.067 (Seconde mineure)
- 1.125 (Seconde majeure)
- 1.200 (Tierce mineure)
- 1.250 (Tierce majeure)
- 1.333 (Quarte parfaite)
- 1.414 (Triton, √2)
- 1.500 (Quinte parfaite)
- 1.618 (Nombre d'or, φ)
```

#### Exemple avec ratio 1.25 (Tierce majeure)

```javascript
const base = 16; // px
const ratio = 1.25;

const scale = {
  xs: Math.round(base / ratio²) = 10px,
  sm: Math.round(base / ratio) = 13px,
  base: 16px,
  lg: Math.round(base × ratio) = 20px,
  xl: Math.round(base × ratio²) = 25px,
  '2xl': Math.round(base × ratio³) = 31px,
  '3xl': Math.round(base × ratio⁴) = 39px,
  '4xl': Math.round(base × ratio⁵) = 49px,
  '5xl': Math.round(base × ratio⁶) = 61px,
  '6xl': Math.round(base × ratio⁷) = 76px
};
```

#### Échelle recommandée (ratio 1.25)

```css
:root {
  --font-size-xs: 0.625rem;    /* 10px */
  --font-size-sm: 0.875rem;    /* 14px */
  --font-size-base: 1rem;      /* 16px */
  --font-size-lg: 1.125rem;    /* 18px */
  --font-size-xl: 1.25rem;     /* 20px */
  --font-size-2xl: 1.5rem;     /* 24px */
  --font-size-3xl: 1.875rem;   /* 30px */
  --font-size-4xl: 2.25rem;    /* 36px */
  --font-size-5xl: 3rem;       /* 48px */
  --font-size-6xl: 3.75rem;    /* 60px */
  --font-size-7xl: 4.5rem;     /* 72px */
  --font-size-8xl: 6rem;       /* 96px */
  --font-size-9xl: 8rem;       /* 128px */
}
```

### Poids de police (Font Weight)

```css
:root {
  --font-weight-thin: 100;
  --font-weight-extralight: 200;
  --font-weight-light: 300;
  --font-weight-normal: 400;      /* Corps de texte */
  --font-weight-medium: 500;
  --font-weight-semibold: 600;    /* Sous-titres */
  --font-weight-bold: 700;        /* Titres */
  --font-weight-extrabold: 800;
  --font-weight-black: 900;
}
```

💡 **Règle d'usage** :
- Texte courant : 400 (normal)
- Emphase : 500-600 (medium/semibold)
- Titres : 700+ (bold)

### Interlignage (Line Height)

#### Formule de calcul

```
line-height = font-size × ratio

Ratios recommandés :
- Display (72px+) : 1.0 - 1.1
- Headings (24-60px) : 1.1 - 1.3
- Body (14-20px) : 1.5 - 1.7
- Small (12px-) : 1.6 - 2.0
```

```css
:root {
  --line-height-none: 1;
  --line-height-tight: 1.25;
  --line-height-snug: 1.375;
  --line-height-normal: 1.5;      /* Par défaut pour body */
  --line-height-relaxed: 1.625;
  --line-height-loose: 2;
}
```

### Longueur de ligne optimale

#### Formule de Tschichold

```
L_optimal = font-size × (60 ± 15) caractères

Pour font-size = 16px :
L_min = 16px × 45 car = 720px ≈ 45rem
L_optimal = 16px × 60 car = 960px = 60rem
L_max = 16px × 75 car = 1200px = 75rem
```

```css
.prose {
  max-width: 65ch; /* 65 caractères */
  /* ou */
  max-width: 60rem; /* 960px à 16px base */
}
```

### Familles de polices

```css
:root {
  /* Sans-serif (UI, corps de texte) */
  --font-family-sans: 
    "Inter", 
    -apple-system, 
    BlinkMacSystemFont, 
    "Segoe UI", 
    "Roboto", 
    "Helvetica Neue", 
    Arial, 
    sans-serif,
    "Apple Color Emoji",
    "Segoe UI Emoji";
  
  /* Serif (contenu éditorial) */
  --font-family-serif: 
    "Merriweather", 
    Georgia, 
    Cambria, 
    "Times New Roman", 
    Times, 
    serif;
  
  /* Monospace (code) */
  --font-family-mono: 
    "JetBrains Mono", 
    "Fira Code", 
    "SF Mono", 
    Monaco, 
    "Cascadia Code", 
    "Consolas", 
    monospace;
}
```

### Styles typographiques composés

```css
/* Heading 1 */
.text-h1 {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-5xl);    /* 48px */
  font-weight: var(--font-weight-bold);
  line-height: var(--line-height-tight);
  letter-spacing: -0.025em;
}

/* Heading 2 */
.text-h2 {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-4xl);    /* 36px */
  font-weight: var(--font-weight-bold);
  line-height: var(--line-height-tight);
  letter-spacing: -0.02em;
}

/* Body */
.text-body {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-base);   /* 16px */
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-normal);
  letter-spacing: 0;
}

/* Small */
.text-small {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-sm);     /* 14px */
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-relaxed);
  letter-spacing: 0.01em;
}

/* Caption */
.text-caption {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-xs);     /* 10px */
  font-weight: var(--font-weight-medium);
  line-height: var(--line-height-relaxed);
  letter-spacing: 0.05em;
  text-transform: uppercase;
}
```

---

## 📏 PARTIE 3 : L'ESPACEMENT

### Système d'espacement en base 4

#### Échelle linéaire

```
S(n) = 4px × n

S(0) = 0px
S(1) = 4px
S(2) = 8px
S(3) = 12px
S(4) = 16px
S(5) = 20px
S(6) = 24px
S(8) = 32px
S(10) = 40px
S(12) = 48px
S(16) = 64px
S(20) = 80px
S(24) = 96px
S(32) = 128px
```

```css
:root {
  --spacing-0: 0;
  --spacing-1: 0.25rem;  /* 4px */
  --spacing-2: 0.5rem;   /* 8px */
  --spacing-3: 0.75rem;  /* 12px */
  --spacing-4: 1rem;     /* 16px */
  --spacing-5: 1.25rem;  /* 20px */
  --spacing-6: 1.5rem;   /* 24px */
  --spacing-8: 2rem;     /* 32px */
  --spacing-10: 2.5rem;  /* 40px */
  --spacing-12: 3rem;    /* 48px */
  --spacing-16: 4rem;    /* 64px */
  --spacing-20: 5rem;    /* 80px */
  --spacing-24: 6rem;    /* 96px */
  --spacing-32: 8rem;    /* 128px */
  --spacing-40: 10rem;   /* 160px */
  --spacing-48: 12rem;   /* 192px */
  --spacing-56: 14rem;   /* 224px */
  --spacing-64: 16rem;   /* 256px */
}
```

### Échelle en base 8

```
S(n) = 8px × n

S(0) = 0
S(1) = 8px
S(2) = 16px
S(3) = 24px
S(4) = 32px
S(5) = 40px
S(6) = 48px
S(8) = 64px
S(10) = 80px
```

💡 **Choix entre base 4 et base 8** :
- **Base 4** : Plus granulaire, meilleur pour mobile
- **Base 8** : Plus simple, suffit souvent

### Espacement sémantique

```css
:root {
  /* Padding de composants */
  --spacing-component-xs: var(--spacing-1);  /* 4px */
  --spacing-component-sm: var(--spacing-2);  /* 8px */
  --spacing-component-md: var(--spacing-4);  /* 16px */
  --spacing-component-lg: var(--spacing-6);  /* 24px */
  --spacing-component-xl: var(--spacing-8);  /* 32px */
  
  /* Gap entre éléments */
  --spacing-gap-xs: var(--spacing-2);   /* 8px */
  --spacing-gap-sm: var(--spacing-4);   /* 16px */
  --spacing-gap-md: var(--spacing-6);   /* 24px */
  --spacing-gap-lg: var(--spacing-8);   /* 32px */
  --spacing-gap-xl: var(--spacing-12);  /* 48px */
  
  /* Sections */
  --spacing-section-sm: var(--spacing-16);  /* 64px */
  --spacing-section-md: var(--spacing-24);  /* 96px */
  --spacing-section-lg: var(--spacing-32);  /* 128px */
}
```

### Loi de proximité appliquée

```
Règle : Distance entre éléments ∝ Relation sémantique

distance(A, B) < distance(B, C)
→ A et B sont perçus comme plus liés

Exemple :
Label ←4px→ Input ←24px→ Autre champ
```

```css
/* Groupe : label + input */
.form-field {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-1);  /* 4px - très proches */
}

/* Entre champs différents */
.form {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-6);  /* 24px - séparés */
}

/* Entre sections */
.sections {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-16);  /* 64px - bien séparés */
}
```

### Formule de densité

```
Densité = Contenu_utile / Espace_total

D_comfortable = 0.3 - 0.5 (30-50% de contenu, reste en white space)
D_dense = 0.5 - 0.7 (mode compact)
D_sparse = 0.2 - 0.3 (mode aéré)
```

---

## ✓ Application pratique : Card component

```css
.card {
  /* Couleurs */
  background: var(--color-bg-primary);
  border: 1px solid var(--color-border-primary);
  color: var(--color-text-primary);
  
  /* Espacement */
  padding: var(--spacing-6);  /* 24px */
  gap: var(--spacing-4);      /* 16px entre éléments */
  
  /* Typographie */
  font-family: var(--font-family-sans);
  
  /* Autres */
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
}

.card__title {
  /* Typographie */
  font-size: var(--font-size-xl);
  font-weight: var(--font-weight-semibold);
  line-height: var(--line-height-tight);
  
  /* Couleur */
  color: var(--color-text-primary);
  
  /* Espacement */
  margin-bottom: var(--spacing-2);  /* 8px */
}

.card__body {
  /* Typographie */
  font-size: var(--font-size-base);
  font-weight: var(--font-weight-normal);
  line-height: var(--line-height-normal);
  
  /* Couleur */
  color: var(--color-text-secondary);
}
```

---

## 📊 Tableau récapitulatif

### Couleurs

| Palette | Nombre | Usage principal |
|---------|--------|-----------------|
| Primary | 11 (50-950) | Actions, liens, focus |
| Gray | 11 (50-950) | Texte, borders, backgrounds |
| Success | 3+ | Confirmations, succès |
| Warning | 3+ | Avertissements |
| Error | 3+ | Erreurs, suppressions |
| Info | 3+ | Informations |

### Typographie

| Propriété | Valeurs | Usage |
|-----------|---------|-------|
| Font size | 10-128px (13 tailles) | Hiérarchie textuelle |
| Font weight | 100-900 (9 poids) | Emphase |
| Line height | 1.0-2.0 | Lisibilité |
| Font family | 2-3 families | UI, Editorial, Code |

### Espacement

| Échelle | Valeurs | Usage |
|---------|---------|-------|
| Base 4 | 0-256px (20+ valeurs) | Padding, margin, gap |
| Sémantique | xs-xl | Composants |
| Sections | sm-lg | Layout majeur |

---

## ⚠️ Erreurs courantes

❌ **Trop de couleurs** : 100 nuances de gris
✓ **Bon** : 10-11 par palette

❌ **Tailles aléatoires** : 13px, 17px, 23px...
✓ **Bon** : Échelle modulaire

❌ **Espacements arbitraires** : 13px, 27px...
✓ **Bon** : Multiples de 4 ou 8

❌ **Line-height en px** : `line-height: 24px`
✓ **Bon** : `line-height: 1.5` (unitless, s'adapte)

---

## 📊 Synthèse visuelle

```
FONDATIONS DU DESIGN SYSTEM
═══════════════════════════════════════════════════════

COULEURS                 TYPOGRAPHIE              ESPACEMENT
────────────────         ───────────────          ──────────────
50  ▓▓▓▓▓▓▓▓            9xl █████████            0   (0)
100 ▓▓▓▓▓▓▓             8xl ████████             1   ████
200 ▓▓▓▓▓▓              7xl ███████              2   ████████
300 ▓▓▓▓▓               6xl ██████               3   ████████████
400 ▓▓▓▓                5xl █████                4   ████████████████
500 ▓▓▓ ← Base          4xl ████                 6   ████████████████████████
600 ▓▓                  3xl ███                  8   ████████████████████████████████
700 ▓                   2xl ██                   12  ████████████████████████████████████████████████
800 ▓                   xl  █                    16  ████████████████████████████████████████████████████████████████
900 ▓                   lg  █                    
950 ▓                   base █ ← Base
                        sm  █
11 valeurs              xs  █

                        13 valeurs               15+ valeurs
```

---

## ✍️ Exercice : Audit de votre projet

### Checklist couleurs

```
☐ Palette primaire : 10-11 teintes
☐ Palette grise : 10-11 teintes
☐ Couleurs sémantiques (success, warning, error)
☐ Couleurs de texte (3 niveaux mini)
☐ Couleurs de background (2-3 niveaux)
☐ Contraste WCAG AA minimum (voir chapitre suivant)
```

### Checklist typographie

```
☐ 1-2 font families définies
☐ Échelle de font-size (8-13 tailles)
☐ Font weights (3-5 poids utilisés)
☐ Line heights (3-5 valeurs)
☐ Letter spacing défini pour display/headings
☐ Longueur de ligne : 45-75 caractères
```

### Checklist espacement

```
☐ Base définie (4px ou 8px)
☐ Échelle cohérente (multiples de la base)
☐ 15-20 valeurs maximum
☐ Tokens sémantiques (component, gap, section)
☐ Pas de valeurs arbitraires en dehors de l'échelle
```

---

## 📚 Points clés à retenir

1. **Couleurs** : Palette de 10-11 teintes, mode HSL
2. **Typographie** : Échelle modulaire (ratio 1.2-1.6)
3. **Espacement** : Base 4 ou 8, échelle harmonique
4. **Sémantique** : Tokens core → semantic → component
5. **Cohérence** : Respecter l'échelle, pas de valeurs arbitraires
6. **Lisibilité** : Line-height 1.5, max 75 caractères/ligne
7. **Accessibilité** : Contraste mini 4.5:1 (prochain chapitre)

---

## 🎯 Prochaines étapes

Nous allons maintenant approfondir **l'accessibilité et les contrastes** pour nous assurer que nos fondations sont inclusives.

---

*Chapitre 01.3 | Couleurs, Typographie et Espacement*

