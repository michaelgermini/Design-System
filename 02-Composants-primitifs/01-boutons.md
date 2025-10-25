# Boutons : Le composant fondamental

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Créer un système de boutons complet et accessible
- ✓ Implémenter les variants, tailles et états
- ✓ Gérer les interactions clavier et souris
- ✓ Optimiser les performances et l'accessibilité
- ✓ Documenter vos composants efficacement

---

## 📐 Anatomie d'un bouton

```
┌─────────────────────────────────────────────┐
│ ┌───────────────────────────────────────┐   │ ← Container
│ │  [Icon] Label Text [Icon]            │   │ ← Content
│ └───────────────────────────────────────┘   │
│     ↑       ↑          ↑                    │
│   Leading  Text    Trailing                 │
│    Icon             Icon                    │
│                                             │
│ ← Padding X →                  ← Padding X →│
└─────────────────────────────────────────────┘
  ↑                                           ↑
Height                                    Border/Radius
```

### Éléments constitutifs

1. **Container** : Forme, couleur de fond, bordure
2. **Content** : Texte + icônes optionnelles
3. **Spacing** : Padding horizontal et vertical
4. **States** : Hover, active, focus, disabled, loading

---

## 🎨 Variants de boutons

### 1. Primary (Action principale)

```tsx
<Button variant="primary">
  Enregistrer
</Button>
```

**Caractéristiques** :
- Couleur de fond : `--color-primary`
- Texte : blanc (contraste élevé)
- Poids visuel maximal
- **Règle** : 1 seul par écran

```css
.button-primary {
  background: var(--color-primary-500);
  color: #FFFFFF;
  border: none;
  font-weight: 600;
  
  /* Contraste : minimum 4.5:1 */
}

.button-primary:hover {
  background: var(--color-primary-600);
}

.button-primary:active {
  background: var(--color-primary-700);
}
```

### 2. Secondary (Actions secondaires)

```tsx
<Button variant="secondary">
  Annuler
</Button>
```

```css
.button-secondary {
  background: transparent;
  color: var(--color-primary-500);
  border: 1px solid var(--color-primary-500);
  font-weight: 500;
}

.button-secondary:hover {
  background: var(--color-primary-50);
}
```

### 3. Ghost (Actions tertiaires)

```tsx
<Button variant="ghost">
  Voir plus
</Button>
```

```css
.button-ghost {
  background: transparent;
  color: var(--color-text-secondary);
  border: none;
  font-weight: 500;
}

.button-ghost:hover {
  background: var(--color-state-hover);
  color: var(--color-text-primary);
}
```

### 4. Danger (Actions destructives)

```tsx
<Button variant="danger">
  Supprimer
</Button>
```

```css
.button-danger {
  background: var(--color-error-500);
  color: #FFFFFF;
  border: none;
  font-weight: 600;
}

.button-danger:hover {
  background: var(--color-error-600);
}
```

### Hiérarchie des variants

```
Poids visuel (10 = max attention)

Primary   ████████████ 10  (1 par écran)
Danger    ██████████   8   (confirmations)
Secondary ██████       6   (2-3 par écran)
Ghost     ███          3   (liens, moins important)
```

---

## 📏 Tailles de boutons

### Échelle de tailles

| Taille | Height | Padding X | Font Size | Usage |
|--------|--------|-----------|-----------|-------|
| **xs** | 28px | 12px | 12px | Tags, pills |
| **sm** | 32px | 16px | 14px | Forms compacts, tables |
| **md** | 40px | 20px | 16px | **Standard** (défaut) |
| **lg** | 48px | 24px | 18px | Hero, CTAs importants |
| **xl** | 56px | 32px | 20px | Landing pages |

### Formules de calcul

```
height = base × size_factor

Padding_X = height × 0.5 à 0.6
Padding_Y = (height - line-height) / 2

Exemple (md, 40px) :
Padding_X = 40 × 0.5 = 20px
Padding_Y = (40 - 24) / 2 = 8px
```

### Implémentation

```typescript
// Button.tsx
type ButtonSize = 'xs' | 'sm' | 'md' | 'lg' | 'xl';

const sizeStyles = {
  xs: 'h-7 px-3 text-xs',      // 28px
  sm: 'h-8 px-4 text-sm',      // 32px
  md: 'h-10 px-5 text-base',   // 40px (default)
  lg: 'h-12 px-6 text-lg',     // 48px
  xl: 'h-14 px-8 text-xl'      // 56px
};
```

---

## 🎭 États des boutons

### 1. Default (Repos)

```css
.button {
  /* Style de base */
  cursor: pointer;
  user-select: none;
}
```

### 2. Hover (Survol)

```css
.button:hover {
  /* Luminosité -5 à -10% */
  /* Ou scale légèrement */
  transform: translateY(-1px);
  box-shadow: var(--shadow-sm);
}
```

### 3. Active (Clic)

```css
.button:active {
  /* Luminosité -15% */
  transform: translateY(0);
  box-shadow: none;
}
```

### 4. Focus (Clavier)

```css
.button:focus-visible {
  outline: 2px solid var(--color-primary-500);
  outline-offset: 2px;
}

/* Ou ring */
.button:focus-visible {
  box-shadow: 0 0 0 3px var(--color-primary-200);
}
```

### 5. Disabled (Désactivé)

```css
.button:disabled {
  background: var(--color-gray-200);
  color: var(--color-gray-400);
  cursor: not-allowed;
  opacity: 0.6;
}
```

### 6. Loading (Chargement)

```tsx
<Button loading>
  <Spinner /> Chargement...
</Button>
```

```css
.button-loading {
  cursor: wait;
  position: relative;
}

.button-loading .button-content {
  opacity: 0.5;
}

.button-loading .spinner {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
```

---

## 💻 Implémentation complète

### TypeScript + React

```typescript
// Button.tsx
import React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center gap-2 rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        primary: 'bg-primary-500 text-white hover:bg-primary-600 active:bg-primary-700',
        secondary: 'border border-primary-500 text-primary-500 hover:bg-primary-50',
        ghost: 'text-gray-700 hover:bg-gray-100 hover:text-gray-900',
        danger: 'bg-error-500 text-white hover:bg-error-600'
      },
      size: {
        xs: 'h-7 px-3 text-xs',
        sm: 'h-8 px-4 text-sm',
        md: 'h-10 px-5 text-base',
        lg: 'h-12 px-6 text-lg',
        xl: 'h-14 px-8 text-xl'
      }
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md'
    }
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  loading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
  asChild?: boolean;
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ 
    className, 
    variant, 
    size, 
    loading, 
    disabled,
    leftIcon, 
    rightIcon, 
    children, 
    ...props 
  }, ref) => {
    return (
      <button
        className={buttonVariants({ variant, size, className })}
        disabled={disabled || loading}
        ref={ref}
        {...props}
      >
        {loading && <Spinner className="animate-spin" />}
        {!loading && leftIcon && leftIcon}
        <span>{children}</span>
        {!loading && rightIcon && rightIcon}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

### Composant Spinner

```tsx
// Spinner.tsx
export function Spinner({ className }: { className?: string }) {
  return (
    <svg
      className={className}
      width="16"
      height="16"
      viewBox="0 0 16 16"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <circle
        cx="8"
        cy="8"
        r="7"
        stroke="currentColor"
        strokeWidth="2"
        strokeLinecap="round"
        strokeDasharray="32"
        strokeDashoffset="8"
      />
    </svg>
  );
}
```

---

## ♿ Accessibilité

### 1. Support clavier

```tsx
function Button({ onClick, children, ...props }: ButtonProps) {
  const handleKeyPress = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      onClick?.(e as any);
    }
  };
  
  return (
    <button
      onClick={onClick}
      onKeyPress={handleKeyPress}
      tabIndex={0}
      {...props}
    >
      {children}
    </button>
  );
}
```

### 2. ARIA attributes

```tsx
<Button
  aria-label="Fermer"              // Si icône seule
  aria-disabled={isDisabled}       // État désactivé
  aria-pressed={isPressed}         // Pour toggle buttons
  aria-busy={isLoading}            // Pendant chargement
  aria-describedby="help-text"     // Description additionnelle
>
  <X /> {/* Icône */}
</Button>

<span id="help-text" className="sr-only">
  Ferme la fenêtre modale
</span>
```

### 3. Zone de clic minimale

```
Loi de Fitts : Cibles plus grandes = Plus rapides à atteindre

Taille minimale :
- Desktop : 40×40px (WCAG 2.5.5)
- Mobile : 44×44px (Apple HIG, WCAG AAA)

Implémentation :
min-height: 44px;
min-width: 44px;
```

### 4. Contraste

```
WCAG AA :
- Texte normal : 4.5:1 minimum
- Texte large (18pt+) : 3:1 minimum
- UI composants : 3:1 minimum

Vérification :
✓ Primary (bleu #3B82F6 sur blanc) : 3.4:1 - OK pour bouton
✓ Texte blanc sur Primary : 8.2:1 - Excellent
```

---

## 🎨 Variants spéciaux

### Icon Button (Icône seule)

```tsx
<IconButton aria-label="Paramètres">
  <Settings />
</IconButton>
```

```css
.icon-button {
  width: 40px;
  height: 40px;
  padding: 0;
  display: inline-flex;
  align-items: center;
  justify-center;
}
```

### Button Group

```tsx
<ButtonGroup>
  <Button>Gauche</Button>
  <Button>Centre</Button>
  <Button>Droite</Button>
</ButtonGroup>
```

```css
.button-group {
  display: inline-flex;
}

.button-group > button:not(:first-child) {
  margin-left: -1px;
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
}

.button-group > button:not(:last-child) {
  border-top-right-radius: 0;
  border-bottom-right-radius: 0;
}
```

### Toggle Button

```tsx
<ToggleButton
  pressed={isBold}
  onPressedChange={setIsBold}
  aria-label="Gras"
>
  <Bold />
</ToggleButton>
```

---

## 📊 Matrice de décision

### Quel variant utiliser ?

| Situation | Variant | Exemple |
|-----------|---------|---------|
| Action principale unique | **Primary** | "Enregistrer", "Valider" |
| Action secondaire | **Secondary** | "Annuler", "Retour" |
| Action tertiaire/navigation | **Ghost** | "Voir plus", "En savoir plus" |
| Action destructive | **Danger** | "Supprimer", "Désactiver" |
| Icône seule | **Icon** | Fermer, Paramètres |

### Quelle taille utiliser ?

| Context | Taille | Hauteur |
|---------|--------|---------|
| Tableaux, forms compacts | **sm** | 32px |
| **Standard** (défaut) | **md** | 40px |
| CTAs importants, Hero | **lg** | 48px |
| Landing pages | **xl** | 56px |

---

## ⚠️ Erreurs courantes

❌ **Trop de boutons primaires**
```tsx
<Button variant="primary">Enregistrer</Button>
<Button variant="primary">Publier</Button>  ← Non !
<Button variant="primary">Partager</Button> ← Non !
```

✓ **Hiérarchie claire**
```tsx
<Button variant="primary">Enregistrer</Button>
<Button variant="secondary">Prévisualiser</Button>
<Button variant="ghost">Annuler</Button>
```

❌ **Texte pas assez explicite**
```tsx
<Button>OK</Button>                  ← Trop vague
<Button>Cliquez ici</Button>         ← Mauvais
<Button aria-label="">  <X /> </Button>  ← Manque label
```

✓ **Texte clair et actionnable**
```tsx
<Button>Enregistrer les modifications</Button>
<Button>Télécharger le PDF</Button>
<Button aria-label="Fermer la fenêtre"><X /></Button>
```

---

## 🧪 Tests

### Tests unitaires (Jest + RTL)

```typescript
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  test('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
  
  test('handles click events', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  test('is disabled when loading', () => {
    render(<Button loading>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
  
  test('shows spinner when loading', () => {
    render(<Button loading>Click me</Button>);
    expect(screen.getByRole('button')).toContainHTML('svg');
  });
  
  test('supports keyboard navigation', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    fireEvent.keyPress(screen.getByText('Click me'), { key: 'Enter' });
    expect(handleClick).toHaveBeenCalled();
  });
});
```

---

## 📚 Documentation Storybook

```typescript
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'ghost', 'danger'],
    },
    size: {
      control: 'select',
      options: ['xs', 'sm', 'md', 'lg', 'xl'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Button',
  },
};

export const AllVariants: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="danger">Danger</Button>
    </div>
  ),
};

export const AllSizes: Story = {
  render: () => (
    <div className="flex items-center gap-4">
      <Button size="xs">Extra Small</Button>
      <Button size="sm">Small</Button>
      <Button size="md">Medium</Button>
      <Button size="lg">Large</Button>
      <Button size="xl">Extra Large</Button>
    </div>
  ),
};

export const WithIcons: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button leftIcon={<Plus />}>Ajouter</Button>
      <Button rightIcon={<ArrowRight />}>Suivant</Button>
    </div>
  ),
};

export const Loading: Story = {
  args: {
    loading: true,
    children: 'Chargement...',
  },
};
```

---

## ✓ Checklist Button

```
DESIGN
☐ 3-4 variants maximum (primary, secondary, ghost, danger)
☐ 3-5 tailles cohérentes (xs, sm, md, lg, xl)
☐ Hauteur minimum 40px (44px sur mobile)
☐ Contraste WCAG AA minimum

COMPORTEMENT
☐ États : default, hover, active, focus, disabled, loading
☐ Transition douce entre états (200-300ms)
☐ Focus indicator visible (outline ou ring)
☐ Cursor approprié (pointer, not-allowed, wait)

ACCESSIBILITÉ
☐ Rôle button (natif ou ARIA)
☐ Support clavier (Enter, Space)
☐ aria-label si icône seule
☐ aria-disabled pour état désactivé
☐ aria-busy pendant chargement
☐ Zone de clic minimale (44×44px)

CODE
☐ Props TypeScript typées
☐ Variants via class-variance-authority ou similaire
☐ Forwarded ref pour accès au DOM
☐ Tests unitaires (render, click, keyboard)

DOCUMENTATION
☐ Storybook avec tous les variants
☐ Exemples d'usage
☐ Guidelines de quand utiliser chaque variant
☐ Props API documentée
```

---

## 📊 Synthèse visuelle

```
SYSTÈME DE BOUTONS
════════════════════════════════════════════════════════

VARIANTS                       TAILLES
────────────────────────────  ─────────────────────────
Primary   █████████            xs  ████████  28px
Secondary ▒▒▒▒▒▒▒▒▒            sm  ██████████  32px
Ghost     ░░░░░░░░░            md  ████████████  40px (défaut)
Danger    ▓▓▓▓▓▓▓▓▓            lg  ██████████████  48px
                               xl  ████████████████  56px

ÉTATS                          HIÉRARCHIE
────────────────────────────  ─────────────────────────
Default  →  Normal             1. Primary (1 par écran)
Hover    →  -5 à -10% L        2. Secondary (2-3 max)
Active   →  -15% L             3. Ghost (tertiaire)
Focus    →  Ring visible       4. Danger (confirmation)
Disabled →  Opacity 0.6
Loading  →  Spinner + Wait
```

---

## 📚 Points clés à retenir

1. **1 primary par écran** maximum
2. **Hauteur minimum** : 40px (44px mobile)
3. **Contraste** : 4.5:1 pour texte, 3:1 pour UI
4. **Support clavier** : Enter + Space
5. **Focus visible** : Toujours
6. **États clairs** : hover, active, disabled, loading
7. **aria-label** : Si icône seule
8. **Tests** : Render, click, keyboard

---

## 🎯 Prochaines étapes

Maintenant que les boutons sont maîtrisés, passons aux **Inputs** : champs de texte, textarea, select, etc.

---

*Chapitre 02.1 | Boutons*

