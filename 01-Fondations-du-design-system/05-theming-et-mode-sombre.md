# Theming et Mode Sombre

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Implémenter un système de theming avec tokens
- ✓ Créer un mode sombre accessible
- ✓ Gérer les préférences utilisateur
- ✓ Optimiser les performances du theme switching
- ✓ Supporter plusieurs thèmes (whitelabel)

---

## 🌓 Pourquoi un mode sombre ?

### Statistiques d'usage

```
82% des utilisateurs utilisent le mode sombre
(étude Android/iOS 2024)

Raisons principales :
1. Confort visuel en faible luminosité (65%)
2. Économie de batterie (OLED) (45%)
3. Esthétique (40%)
4. Réduction fatigue oculaire (35%)
```

### Économie d'énergie (écrans OLED)

```
Formule de consommation :

P = k × L × A

où :
P = Puissance consommée
k = Constante de l'écran
L = Luminosité moyenne (0-1)
A = Surface affichée

Mode clair (L ≈ 0.8) : P = k × 0.8 × A
Mode sombre (L ≈ 0.2) : P = k × 0.2 × A

Économie : 75% sur écrans OLED !
```

---

## 🎨 Architecture de theming

### 1. Tokens par thème

```css
/* Base tokens (indépendants du thème) */
:root {
  /* Couleurs brutes */
  --color-blue-500: #3B82F6;
  --color-gray-50: #F9FAFB;
  --color-gray-900: #111827;
  /* ... */
}

/* Light theme (default) */
:root,
[data-theme="light"] {
  --color-background: #FFFFFF;
  --color-background-secondary: var(--color-gray-50);
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-600);
  --color-border: var(--color-gray-200);
  --color-primary: var(--color-blue-500);
}

/* Dark theme */
[data-theme="dark"] {
  --color-background: #0F172A;
  --color-background-secondary: #1E293B;
  --color-text-primary: #F1F5F9;
  --color-text-secondary: #CBD5E1;
  --color-border: #334155;
  --color-primary: #60A5FA;  /* Bleu plus clair pour contraste */
}

/* Usage dans les composants (identique pour tous les thèmes) */
.card {
  background: var(--color-background);
  color: var(--color-text-primary);
  border: 1px solid var(--color-border);
}
```

### 2. Structure des tokens de theming

```typescript
// theme-tokens.ts
export const lightTheme = {
  // Backgrounds
  bg: {
    primary: '#FFFFFF',
    secondary: '#F9FAFB',
    tertiary: '#F3F4F6',
    inverse: '#111827',
    overlay: 'rgba(0, 0, 0, 0.5)'
  },
  
  // Text
  text: {
    primary: '#111827',
    secondary: '#6B7280',
    tertiary: '#9CA3AF',
    inverse: '#FFFFFF',
    disabled: '#D1D5DB'
  },
  
  // Borders
  border: {
    default: '#E5E7EB',
    strong: '#D1D5DB',
    subtle: '#F3F4F6',
    inverse: '#374151'
  },
  
  // Accent colors
  primary: {
    main: '#3B82F6',
    hover: '#2563EB',
    active: '#1D4ED8',
    subtle: '#DBEAFE'
  },
  
  // States
  success: {
    main: '#10B981',
    subtle: '#D1FAE5'
  },
  error: {
    main: '#EF4444',
    subtle: '#FEE2E2'
  },
  warning: {
    main: '#F59E0B',
    subtle: '#FEF3C7'
  },
  info: {
    main: '#3B82F6',
    subtle: '#DBEAFE'
  }
};

export const darkTheme = {
  // Backgrounds
  bg: {
    primary: '#0F172A',     // slate-900
    secondary: '#1E293B',   // slate-800
    tertiary: '#334155',    // slate-700
    inverse: '#F8FAFC',     // slate-50
    overlay: 'rgba(0, 0, 0, 0.75)'
  },
  
  // Text
  text: {
    primary: '#F1F5F9',     // slate-100
    secondary: '#CBD5E1',   // slate-300
    tertiary: '#94A3B8',    // slate-400
    inverse: '#0F172A',
    disabled: '#64748B'     // slate-500
  },
  
  // Borders
  border: {
    default: '#334155',     // slate-700
    strong: '#475569',      // slate-600
    subtle: '#1E293B',      // slate-800
    inverse: '#CBD5E1'
  },
  
  // Accent colors (plus clairs pour contraste)
  primary: {
    main: '#60A5FA',        // blue-400
    hover: '#3B82F6',       // blue-500
    active: '#2563EB',      // blue-600
    subtle: '#1E3A8A'       // blue-900
  },
  
  // States (plus clairs)
  success: {
    main: '#34D399',        // emerald-400
    subtle: '#064E3B'       // emerald-900
  },
  error: {
    main: '#F87171',        // red-400
    subtle: '#7F1D1D'       // red-900
  },
  warning: {
    main: '#FBBF24',        // amber-400
    subtle: '#78350F'       // amber-900
  },
  info: {
    main: '#60A5FA',        // blue-400
    subtle: '#1E3A8A'       // blue-900
  }
};
```

---

## 🔧 Implémentation

### 1. React Context + CSS Variables

```tsx
// ThemeContext.tsx
import React, { createContext, useContext, useEffect, useState } from 'react';

type Theme = 'light' | 'dark' | 'system';

interface ThemeContextType {
  theme: Theme;
  setTheme: (theme: Theme) => void;
  resolvedTheme: 'light' | 'dark';
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<Theme>('system');
  const [resolvedTheme, setResolvedTheme] = useState<'light' | 'dark'>('light');
  
  useEffect(() => {
    // Charger la préférence sauvegardée
    const saved = localStorage.getItem('theme') as Theme | null;
    if (saved) {
      setTheme(saved);
    }
  }, []);
  
  useEffect(() => {
    // Résoudre 'system' en 'light' ou 'dark'
    if (theme === 'system') {
      const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
      setResolvedTheme(mediaQuery.matches ? 'dark' : 'light');
      
      const handler = (e: MediaQueryListEvent) => {
        setResolvedTheme(e.matches ? 'dark' : 'light');
      };
      
      mediaQuery.addEventListener('change', handler);
      return () => mediaQuery.removeEventListener('change', handler);
    } else {
      setResolvedTheme(theme);
    }
  }, [theme]);
  
  useEffect(() => {
    // Appliquer le thème au DOM
    document.documentElement.setAttribute('data-theme', resolvedTheme);
    
    // Sauvegarder la préférence
    localStorage.setItem('theme', theme);
  }, [theme, resolvedTheme]);
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme, resolvedTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}
```

### 2. Composant ThemeToggle

```tsx
// ThemeToggle.tsx
import { useTheme } from './ThemeContext';
import { Sun, Moon, Monitor } from 'lucide-react';

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();
  
  const themes = [
    { value: 'light', icon: Sun, label: 'Clair' },
    { value: 'dark', icon: Moon, label: 'Sombre' },
    { value: 'system', icon: Monitor, label: 'Système' }
  ] as const;
  
  return (
    <div role="radiogroup" aria-label="Choix du thème">
      {themes.map(({ value, icon: Icon, label }) => (
        <button
          key={value}
          role="radio"
          aria-checked={theme === value}
          onClick={() => setTheme(value)}
          className={theme === value ? 'active' : ''}
        >
          <Icon />
          <span>{label}</span>
        </button>
      ))}
    </div>
  );
}
```

### 3. Éviter le flash de thème incorrect

```tsx
// _document.tsx (Next.js) ou index.html
<script
  dangerouslySetInnerHTML={{
    __html: `
      (function() {
        function getTheme() {
          const stored = localStorage.getItem('theme');
          if (stored && stored !== 'system') return stored;
          
          return window.matchMedia('(prefers-color-scheme: dark)').matches
            ? 'dark'
            : 'light';
        }
        
        document.documentElement.setAttribute('data-theme', getTheme());
      })();
    `
  }}
/>
```

---

## 🎨 Règles de conversion Light → Dark

### 1. Inversion intelligente

❌ **Mauvais** : Inverser toutes les couleurs
```
Light: background #FFFFFF, text #000000
Dark:  background #000000, text #FFFFFF  ← Trop de contraste, fatiguant
```

✓ **Bon** : Réduire le contraste
```
Light: background #FFFFFF, text #111827
Dark:  background #0F172A, text #F1F5F9  ← Contraste confortable
```

### 2. Formule de conversion

```
Conversion automatique (approximative) :

dark_lightness = 100 - (light_lightness × factor)

où factor ≈ 0.8 - 0.9

Exemple :
Light gray-100 (L = 90%) → Dark: 100 - (90 × 0.85) = 23.5%
Light gray-900 (L = 15%) → Dark: 100 - (15 × 0.85) = 87%
```

### 3. Ajustements des couleurs

```css
/* Couleurs primaires : légèrement plus claires en dark */
:root {
  --primary-light: #3B82F6;  /* L = 60% */
}

[data-theme="dark"] {
  --primary-dark: #60A5FA;   /* L = 70%, +10% pour contraste */
}
```

#### Tableau de conversion recommandé

| Élément | Light | Dark | Règle |
|---------|-------|------|-------|
| **BG principal** | 100% (blanc) | 10% (presque noir) | Inversion |
| **BG secondaire** | 98% | 15% | Inversion |
| **Texte principal** | 10% | 95% | Inversion |
| **Texte secondaire** | 40% | 75% | Inversion + contraste |
| **Borders** | 85% | 25% | Inversion |
| **Primary color** | 55% | 65% | +10% luminosité |
| **Success** | 45% | 55% | +10% luminosité |
| **Error** | 50% | 60% | +10% luminosité |

### 4. Ombres en mode sombre

```css
/* Light mode */
:root {
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

/* Dark mode : Ombres plus subtiles ou plus marquées */
[data-theme="dark"] {
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.2);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.6);
  
  /* Ou : glow subtil pour élévation */
  --shadow-glow: 0 0 20px rgba(255, 255, 255, 0.05);
}
```

---

## 🖼️ Images et illustrations

### 1. Filtres CSS pour images

```css
/* Mode sombre : réduire l'intensité des images */
[data-theme="dark"] img {
  filter: brightness(0.8) contrast(1.1);
}

/* Icônes : inverser si noir sur blanc */
[data-theme="dark"] .icon-monochrome {
  filter: invert(1);
}
```

### 2. Illustrations adaptatives

```tsx
// Illustration.tsx
import { useTheme } from './ThemeContext';

export function Illustration() {
  const { resolvedTheme } = useTheme();
  
  return (
    <picture>
      <source
        srcSet="/illustration-dark.svg"
        media="(prefers-color-scheme: dark)"
      />
      <img
        src={resolvedTheme === 'dark' 
          ? '/illustration-dark.svg' 
          : '/illustration-light.svg'
        }
        alt="Illustration"
      />
    </picture>
  );
}
```

### 3. SVG avec tokens CSS

```html
<svg viewBox="0 0 100 100">
  <circle
    cx="50"
    cy="50"
    r="40"
    fill="var(--color-primary)"
    stroke="var(--color-border)"
  />
  <text
    x="50"
    y="55"
    fill="var(--color-text-primary)"
    text-anchor="middle"
  >
    Icon
  </text>
</svg>
```

---

## ⚡ Optimisation des performances

### 1. Transition en douceur

```css
/* Transition globale (attention : peut être lent) */
* {
  transition: 
    background-color 200ms ease,
    color 200ms ease,
    border-color 200ms ease;
}

/* Meilleure approche : transition sélective */
.card,
.button,
.input {
  transition: 
    background-color 200ms ease,
    color 200ms ease;
}

/* Désactiver les transitions pendant le switch */
.theme-switching * {
  transition: none !important;
}
```

```tsx
// Désactiver transitions temporairement
function switchTheme(newTheme: string) {
  document.documentElement.classList.add('theme-switching');
  document.documentElement.setAttribute('data-theme', newTheme);
  
  setTimeout(() => {
    document.documentElement.classList.remove('theme-switching');
  }, 0);
}
```

### 2. Lazy loading des thèmes

```tsx
// Charger le CSS du thème à la demande
async function loadTheme(theme: 'light' | 'dark') {
  const link = document.createElement('link');
  link.rel = 'stylesheet';
  link.href = `/themes/${theme}.css`;
  
  return new Promise((resolve) => {
    link.onload = resolve;
    document.head.appendChild(link);
  });
}
```

### 3. Memoization des composants

```tsx
// Éviter les re-renders inutiles
const ThemedComponent = React.memo(function ThemedComponent() {
  const { resolvedTheme } = useTheme();
  
  // ...
});
```

---

## 🏢 Multi-theming (Whitelabel)

### Architecture pour plusieurs marques

```typescript
// brand-themes.ts
export const brandThemes = {
  'brand-a': {
    primary: '#3B82F6',
    secondary: '#10B981',
    logo: '/logos/brand-a.svg',
    font: 'Inter'
  },
  'brand-b': {
    primary: '#EF4444',
    secondary: '#F59E0B',
    logo: '/logos/brand-b.svg',
    font: 'Roboto'
  },
  'brand-c': {
    primary: '#8B5CF6',
    secondary: '#EC4899',
    logo: '/logos/brand-c.svg',
    font: 'Poppins'
  }
};

export type BrandName = keyof typeof brandThemes;
```

### Application dynamique

```tsx
// BrandProvider.tsx
function BrandProvider({ brand, children }: { brand: BrandName; children: React.ReactNode }) {
  const theme = brandThemes[brand];
  
  useEffect(() => {
    // Injecter les tokens CSS
    Object.entries(theme).forEach(([key, value]) => {
      document.documentElement.style.setProperty(`--brand-${key}`, value);
    });
    
    // Charger la font
    const link = document.createElement('link');
    link.href = `https://fonts.googleapis.com/css2?family=${theme.font}&display=swap`;
    link.rel = 'stylesheet';
    document.head.appendChild(link);
  }, [brand, theme]);
  
  return <>{children}</>;
}

// Usage
<BrandProvider brand="brand-a">
  <App />
</BrandProvider>
```

---

## ✓ Checklist theming

```
TOKENS
☐ Tous les tokens sémantiques définis (bg, text, border)
☐ Pas de couleurs hard-codées dans les composants
☐ Tokens référencent des core tokens

CONTRASTE
☐ Mode sombre respecte WCAG AA (4.5:1 mini)
☐ Texte principal : luminosité 90%+ (dark) / 10%- (light)
☐ Pas de blanc pur (#FFF) ou noir pur (#000) en dark

TRANSITION
☐ Transition en douceur (200-300ms)
☐ Pas de flash au chargement
☐ Préférence système détectée

IMAGES
☐ Illustrations adaptées au thème
☐ Filtres CSS sur photos si nécessaire
☐ SVG utilisent les tokens CSS

PERFORMANCE
☐ CSS variables (pas de re-render React)
☐ Composants mémorisés
☐ Transitions désactivées pendant le switch

ACCESSIBILITÉ
☐ Indication claire du thème actif
☐ Bouton toggle accessible au clavier
☐ ARIA attributes (role="radiogroup")
☐ Respect de prefers-color-scheme
```

---

## 📊 Synthèse visuelle

```
ARCHITECTURE DE THEMING
════════════════════════════════════════════════════════

CORE TOKENS (invariants)
────────────────────────
color-blue-500: #3B82F6
color-gray-50: #F9FAFB
         ↓
SEMANTIC TOKENS (par thème)
────────────────────────────
Light:                    Dark:
--color-bg: #FFFFFF       --color-bg: #0F172A
--color-text: #111827     --color-text: #F1F5F9
--color-primary: #3B82F6  --color-primary: #60A5FA
         ↓
COMPOSANTS (identiques)
───────────────────────
.card {
  background: var(--color-bg);
  color: var(--color-text);
}

RÈGLE DE CONVERSION
═══════════════════
Light L%  →  Dark: 100 - (L × 0.85)
90%       →  23.5%
50%       →  57.5%
10%       →  91.5%
```

---

## 💡 Exemples complets

### Exemple 1 : Card adaptative

```tsx
// Card.tsx
export function Card({ children }: { children: React.ReactNode }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

// card.css
.card {
  /* Utilise les tokens - fonctionne pour tous les thèmes */
  background: var(--color-bg-secondary);
  color: var(--color-text-primary);
  border: 1px solid var(--color-border-default);
  border-radius: var(--radius-lg);
  padding: var(--spacing-6);
  box-shadow: var(--shadow-sm);
  
  /* Transition douce */
  transition: 
    background-color 200ms ease,
    border-color 200ms ease,
    box-shadow 200ms ease;
}

.card:hover {
  box-shadow: var(--shadow-md);
}
```

### Exemple 2 : Graphique adaptatif

```tsx
// Chart.tsx
import { useTheme } from './ThemeContext';

export function Chart({ data }: { data: number[] }) {
  const { resolvedTheme } = useTheme();
  
  const chartColors = resolvedTheme === 'dark'
    ? {
        grid: '#334155',
        text: '#CBD5E1',
        line: '#60A5FA'
      }
    : {
        grid: '#E5E7EB',
        text: '#4B5563',
        line: '#3B82F6'
      };
  
  return (
    <LineChart
      data={data}
      gridColor={chartColors.grid}
      textColor={chartColors.text}
      lineColor={chartColors.line}
    />
  );
}
```

---

## 📚 Points clés à retenir

1. **CSS Variables** : Base du theming moderne
2. **Tokens sémantiques** : bg, text, border (pas de couleurs directes)
3. **Contraste réduit** en dark : pas de blanc pur
4. **Conversion** : Luminosité inversée avec facteur 0.8-0.9
5. **Préférence système** : `prefers-color-scheme`
6. **Pas de flash** : Script inline avant le render
7. **Accessibilité** : WCAG AA en light ET dark
8. **Performance** : CSS variables > Re-render React

---

## 🎯 Prochaines étapes

Nos fondations sont maintenant complètes ! Passons aux **composants primitifs** : boutons, inputs, cards, etc.

---

*Chapitre 01.5 | Theming et Mode Sombre*

