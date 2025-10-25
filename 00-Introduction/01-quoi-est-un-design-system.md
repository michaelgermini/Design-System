# Qu'est-ce qu'un Design System ?

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Définir précisément ce qu'est un design system
- ✓ Distinguer un design system d'une bibliothèque de composants
- ✓ Identifier les composantes essentielles d'un design system
- ✓ Comprendre les différents niveaux de maturité
- ✓ Évaluer si votre projet a besoin d'un design system

---

## 📖 Définition formelle

### L'équation fondamentale

Un design system peut être représenté mathématiquement comme :

```
DS = {T, C, P, D, G}

où :
T = Design Tokens (variables de design)
C = Components (composants réutilisables)
P = Patterns (modèles d'usage)
D = Documentation (guides et exemples)
G = Governance (règles et processus)
```

### Définition littéraire

> **Un design system est un ensemble cohérent et évolutif de standards, de composants et d'outils qui permettent de concevoir et développer des produits numériques de manière systématique et harmonieuse.**

Plus simplement : c'est le **langage visuel et technique partagé** par toute une équipe produit.

---

## 🧩 Les composantes d'un Design System

### 1. Design Tokens (T)

Les **tokens** sont les atomes du design system : les valeurs les plus fondamentales.

```javascript
// Exemple de tokens
const tokens = {
  colors: {
    primary: {
      50: '#EEF2FF',
      500: '#3B82F6',  // Couleur principale
      900: '#1E3A8A'
    }
  },
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px'
  },
  typography: {
    fontFamily: {
      sans: 'Inter, system-ui, sans-serif',
      mono: 'JetBrains Mono, monospace'
    },
    fontSize: {
      sm: '14px',
      base: '16px',
      lg: '18px',
      xl: '20px'
    }
  },
  radius: {
    sm: '4px',
    md: '8px',
    lg: '12px',
    full: '9999px'
  },
  shadows: {
    sm: '0 1px 2px rgba(0,0,0,0.05)',
    md: '0 4px 6px rgba(0,0,0,0.1)',
    lg: '0 10px 15px rgba(0,0,0,0.1)'
  }
};
```

**Formule de cohérence des tokens** :

```
Cohérence_tokens = (Tokens_utilisés / Tokens_définis) × 100

Objectif : > 80%
```

Si vous définissez 50 couleurs mais n'en utilisez que 10, votre cohérence est faible (20%).

### 2. Composants (C)

Les composants sont les **molécules et organismes** construits à partir des tokens.

#### Hiérarchie des composants

```
Niveau 0 : Tokens (variables)
    ↓
Niveau 1 : Primitives (Button, Input, Text)
    ↓
Niveau 2 : Composés (Card, Modal, Dropdown)
    ↓
Niveau 3 : Patterns (Form, Navigation, Auth)
    ↓
Niveau 4 : Templates (Dashboard, Settings, Landing)
```

#### Exemple de composant primitif

```typescript
// Button.tsx
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'ghost';
  size: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  disabled = false,
  children
}) => {
  // Utilise les tokens pour le style
  const styles = {
    primary: 'bg-primary-500 text-white hover:bg-primary-600',
    secondary: 'bg-secondary-500 text-white hover:bg-secondary-600',
    ghost: 'bg-transparent text-gray-700 hover:bg-gray-100'
  };
  
  const sizes = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg'
  };
  
  return (
    <button
      className={`${styles[variant]} ${sizes[size]} rounded-md transition-colors`}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### 3. Patterns (P)

Les patterns sont des **solutions réutilisables** à des problèmes récurrents.

| Pattern | Problème résolu | Exemple |
|---------|----------------|---------|
| **Form validation** | Feedback utilisateur cohérent | Message d'erreur sous le champ |
| **Empty state** | Gérer l'absence de données | Illustration + CTA |
| **Loading** | Indiquer un chargement | Skeleton screens |
| **Error handling** | Afficher les erreurs | Toast notifications |
| **Navigation** | Se déplacer dans l'app | Sidebar + breadcrumbs |

### 4. Documentation (D)

La documentation est le **mode d'emploi** du design system.

**Équation de la documentation** :

```
Qualité_doc = (Exemples_code + Captures_écran + Guidelines) / Ambiguïtés

Idéal : Qualité_doc > 10
```

#### Structure d'une bonne documentation

```markdown
## Composant : Button

### Quand l'utiliser
✓ Pour déclencher une action
✓ Pour soumettre un formulaire
✓ Pour naviguer (avec parcimonie)

### Quand ne PAS l'utiliser
❌ Pour naviguer (préférer Link)
❌ Pour des actions destructives (préférer Dialog de confirmation)

### Variants

1. **Primary** : Action principale (1 seul par écran)
2. **Secondary** : Actions secondaires
3. **Ghost** : Actions tertiaires, moins importantes

### Accessibilité
- Contraste minimum : 4.5:1 (WCAG AA)
- Taille minimum : 44×44px (zone tactile)
- Support clavier : Enter et Espace
- ARIA : role="button" si <div>

### Exemples
[Code examples here]
```

### 5. Gouvernance (G)

La gouvernance définit **qui fait quoi et comment**.

```
Matrice RACI d'un Design System

Tâche                    | Designer | Dev | Product | Contributeur
-------------------------|----------|-----|---------|-------------
Créer nouveau composant  | R        | R   | C       | I
Valider accessibilité    | C        | R   | I       | I
Documenter               | A        | R   | I       | I
Approuver release        | I        | A   | R       | I
Proposer amélioration    | C        | C   | C       | R

R = Responsible (réalise)
A = Accountable (approuve)
C = Consulted (consulté)
I = Informed (informé)
```

---

## 🔍 Design System vs. Autres concepts

### Tableau comparatif

| Concept | Scope | Technique | Design | Process | Gouvernance |
|---------|-------|-----------|--------|---------|-------------|
| **Design System** | 🟢 Large | ✓ | ✓ | ✓ | ✓ |
| **UI Kit** | 🟡 Moyen | ✗ | ✓ | ✗ | ✗ |
| **Component Library** | 🟡 Moyen | ✓ | ✗ | ✗ | ✗ |
| **Style Guide** | 🔴 Petit | ✗ | ✓ | ✗ | ✗ |
| **Pattern Library** | 🟡 Moyen | ~ | ✓ | ✓ | ✗ |

### Équation d'inclusion

```
Style_Guide ⊂ UI_Kit ⊂ Component_Library ⊂ Design_System

où ⊂ signifie "est un sous-ensemble de"
```

Un design system **englobe** tous les autres concepts.

---

## 📊 Niveaux de maturité

### Modèle à 5 niveaux

```
Niveau 5 : Systémique     ████████████████████  100%
            ↑ Documentation auto-générée
            ↑ Tokens universels multi-plateformes
            ↑ Gouvernance formalisée

Niveau 4 : Productif      ████████████████      80%
            ↑ Composants documentés
            ↑ Tests automatisés
            ↑ CI/CD en place

Niveau 3 : Définitif      ████████████          60%
            ↑ Bibliothèque de composants
            ↑ Tokens définis
            ↑ Documentation basique

Niveau 2 : Répétitif      ████████              40%
            ↑ Composants copiés-collés
            ↑ Variables CSS/SCSS
            ↑ Style guide PDF

Niveau 1 : Chaotique      ████                  20%
            ↑ Chaque page est unique
            ↑ Styles inline
            ↑ Pas de documentation
```

### Formule de maturité

```
Score_maturité = (
  20 × a +  // a = tokens définis (0-1)
  30 × b +  // b = composants réutilisables (0-1)
  20 × c +  // c = documentation complète (0-1)
  15 × d +  // d = tests automatisés (0-1)
  15 × e    // e = gouvernance établie (0-1)
)

où chaque variable vaut entre 0 (absent) et 1 (complet)

Score max = 100 points
```

**Exemple de calcul** :
- Tokens définis à 80% → a = 0.8
- Composants réutilisables à 60% → b = 0.6
- Documentation à 40% → c = 0.4
- Tests à 20% → d = 0.2
- Gouvernance à 0% → e = 0

```
Score = 20×0.8 + 30×0.6 + 20×0.4 + 15×0.2 + 15×0
Score = 16 + 18 + 8 + 3 + 0 = 45/100
→ Niveau 3 : Définitif
```

---

## 💡 Pourquoi "System" ?

Le terme **"system"** n'est pas anodin. Il implique :

### 1. Interconnexion

```
Tokens ←→ Composants ←→ Patterns
   ↕          ↕           ↕
Documentation ←→ Gouvernance
```

Modifier un token impacte tous les composants → **effet systémique**.

### 2. Règles et contraintes

```
IF button.variant === 'primary' 
THEN max_per_page = 1

IF spacing.value 
THEN spacing.value ∈ {4, 8, 12, 16, 24, 32, 48, 64}px
```

### 3. Évolution contrôlée

```
Version n → Version n+1

Avec :
- Changelog
- Migration guide
- Deprecation warnings
- Backward compatibility (si possible)
```

---

## ⚠️ Ce qu'un Design System N'EST PAS

❌ **Un projet avec une fin** → C'est un produit continu

❌ **Seulement du design** → C'est aussi technique et organisationnel

❌ **Une contrainte créative** → C'est une accélération créative

❌ **Un luxe de grande entreprise** → C'est rentable dès 2-3 développeurs

❌ **Un outil de développeur** → C'est un outil de collaboration

❌ **Une mode passagère** → C'est une évolution naturelle du développement web

---

## ✓ Bonnes pratiques

### 1. Commencer petit

```
MVP d'un Design System :
├── 5-10 couleurs (tokens)
├── 3-5 composants primitifs (Button, Input, Text)
├── 1 page de documentation
└── 1 règle de contribution
```

### 2. Penser réutilisabilité

**Règle du 3** : Un composant ne devient "système" qu'après sa **3ème utilisation** dans des contextes différents.

### 3. Documenter en continu

```
Ratio idéal :
Temps de code : Temps de documentation = 2:1
```

Pour 2h de code, prévoyez 1h de documentation.

### 4. Impliquer toute l'équipe

```
Responsabilité partagée :
- Designers : Définissent les standards visuels
- Devs : Implémentent et optimisent
- Product : Priorisent les composants
- QA : Testent l'accessibilité
```

---

## 📊 Synthèse visuelle

```
┌─────────────────────────────────────────────────────────┐
│                    DESIGN SYSTEM                         │
│                                                          │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐ │
│  │   TOKENS    │  │  COMPOSANTS  │  │    PATTERNS    │ │
│  │             │  │              │  │                │ │
│  │ • Couleurs  │→ │ • Primitifs  │→ │ • Forms        │ │
│  │ • Espacem.  │  │ • Composés   │  │ • Navigation   │ │
│  │ • Typo      │  │ • Templates  │  │ • Feedback     │ │
│  └─────────────┘  └──────────────┘  └────────────────┘ │
│         ↓                 ↓                   ↓          │
│  ┌──────────────────────────────────────────────────┐   │
│  │            DOCUMENTATION + OUTILS                │   │
│  └──────────────────────────────────────────────────┘   │
│         ↓                                                │
│  ┌──────────────────────────────────────────────────┐   │
│  │           GOUVERNANCE + PROCESSUS                │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## ✍️ Exercice d'évaluation

### Quiz : Avez-vous besoin d'un design system ?

Répondez par OUI ou NON :

1. ☐ Votre équipe compte 2+ designers ou 3+ développeurs front-end
2. ☐ Vous avez plusieurs produits/applications à maintenir
3. ☐ Vous constatez des incohérences visuelles entre les pages
4. ☐ L'onboarding d'un nouveau dev prend plus de 2 semaines
5. ☐ Vous copiez-collez régulièrement du code de composants
6. ☐ Les développeurs demandent souvent "quelle couleur utiliser ?"
7. ☐ Les bugs visuels représentent >10% de vos tickets
8. ☐ Vous refaites souvent le même composant différemment

**Résultat** :
- **0-2 OUI** : Peut-être trop tôt, mais gardez l'idée en tête
- **3-5 OUI** : Un design system serait bénéfique
- **6-8 OUI** : Un design system est **urgent** !

---

## 📚 Points clés à retenir

1. Un design system = **Tokens + Composants + Patterns + Documentation + Gouvernance**
2. Ce n'est pas un projet, c'est un **produit évolutif**
3. Il existe différents **niveaux de maturité** (de chaotique à systémique)
4. Commencer **petit** et itérer est la meilleure approche
5. La **documentation** est aussi importante que le code

---

## 🎯 Prochaines étapes

Dans le prochain chapitre, nous explorerons **pourquoi investir dans un design system**, avec des métriques concrètes et des études de cas réels.

**Question de réflexion** : *À quel niveau de maturité se situe votre équipe actuellement ?*

---

*Chapitre 01 | Qu'est-ce qu'un Design System ?*

