# Cards : Conteneurs de contenu

## 🎯 Objectifs

- ✓ Créer des cards flexibles et modulaires
- ✓ Implémenter les variants et compositions
- ✓ Gérer les interactions et états
- ✓ Optimiser la lisibilité et hiérarchie

---

## 📐 Anatomie d'une Card

```
┌─────────────────────────────────────────────┐
│ ┌─────────────────────────────────────────┐ │
│ │         Image / Media (optionnel)       │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │  Header                           [•••] │ │ ← Header + Actions
│ ├─────────────────────────────────────────┤ │
│ │  Title                                  │ │ ← Title
│ │  Description du contenu de la card...   │ │ ← Description
│ │                                         │ │
│ │  • Élément de liste                    │ │ ← Content
│ │  • Autre élément                       │ │
│ ├─────────────────────────────────────────┤ │
│ │  [Button]              [Button]        │ │ ← Footer / Actions
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

---

## 🎨 Variants de Cards

### 1. Default Card

```tsx
<Card>
  <CardHeader>
    <CardTitle>Titre</CardTitle>
  </CardHeader>
  <CardContent>
    Contenu de la card
  </CardContent>
  <CardFooter>
    <Button>Action</Button>
  </CardFooter>
</Card>
```

### 2. Elevated Card (avec ombre)

```css
.card-elevated {
  box-shadow: var(--shadow-md);
}

.card-elevated:hover {
  box-shadow: var(--shadow-lg);
  transform: translateY(-2px);
}
```

### 3. Outlined Card (avec bordure)

```css
.card-outlined {
  border: 1px solid var(--color-border);
  box-shadow: none;
}
```

### 4. Interactive Card (cliquable)

```tsx
<Card asLink href="/detail">
  {/* Contenu */}
</Card>
```

```css
.card-interactive {
  cursor: pointer;
  transition: all 200ms;
}

.card-interactive:hover {
  box-shadow: var(--shadow-lg);
  border-color: var(--color-primary);
}
```

---

## 📏 Spacing & Padding

```css
.card {
  padding: var(--spacing-6); /* 24px */
  gap: var(--spacing-4);     /* 16px entre sections */
  border-radius: var(--radius-lg);
}

.card-header {
  padding-bottom: var(--spacing-4);
}

.card-footer {
  padding-top: var(--spacing-4);
  border-top: 1px solid var(--color-border);
}
```

---

## 💻 Implémentation

```tsx
// Card.tsx
interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  variant?: 'default' | 'elevated' | 'outlined';
  interactive?: boolean;
  asLink?: boolean;
  href?: string;
}

export const Card = React.forwardRef<HTMLDivElement, CardProps>(
  ({ variant = 'default', interactive, asLink, href, className, children, ...props }, ref) => {
    const Component = asLink ? 'a' : 'div';
    
    return (
      <Component
        ref={ref}
        href={asLink ? href : undefined}
        className={cn(
          'card',
          `card-${variant}`,
          interactive && 'card-interactive',
          className
        )}
        {...props}
      >
        {children}
      </Component>
    );
  }
);

export const CardHeader = ({ children }: { children: React.ReactNode }) => (
  <div className="card-header">{children}</div>
);

export const CardTitle = ({ children }: { children: React.ReactNode }) => (
  <h3 className="card-title">{children}</h3>
);

export const CardContent = ({ children }: { children: React.ReactNode }) => (
  <div className="card-content">{children}</div>
);

export const CardFooter = ({ children }: { children: React.ReactNode }) => (
  <div className="card-footer">{children}</div>
);
```

---

## 🎨 CSS Styles

```css
.card {
  background: var(--color-bg-primary);
  border-radius: var(--radius-lg);
  padding: var(--spacing-6);
  transition: all 200ms ease;
}

.card-elevated {
  box-shadow: var(--shadow-sm);
}

.card-elevated:hover {
  box-shadow: var(--shadow-md);
}

.card-outlined {
  border: 1px solid var(--color-border);
}

.card-interactive {
  cursor: pointer;
}

.card-interactive:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.card-title {
  font-size: var(--font-size-lg);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-primary);
  margin: 0;
}

.card-content {
  color: var(--color-text-secondary);
  line-height: var(--line-height-normal);
}

.card-footer {
  display: flex;
  gap: var(--spacing-3);
  padding-top: var(--spacing-4);
  border-top: 1px solid var(--color-border);
}
```

---

## 🖼️ Cards avec images

```tsx
<Card>
  <CardImage src="/image.jpg" alt="Description" />
  <CardHeader>
    <CardTitle>Titre</CardTitle>
  </CardHeader>
  <CardContent>
    Description
  </CardContent>
</Card>
```

```css
.card-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: var(--radius-lg) var(--radius-lg) 0 0;
  margin: calc(var(--spacing-6) * -1);
  margin-bottom: var(--spacing-4);
}
```

---

## 📊 Grid de Cards

```tsx
<div className="cards-grid">
  <Card>...</Card>
  <Card>...</Card>
  <Card>...</Card>
</div>
```

```css
.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: var(--spacing-6);
}
```

---

## ♿ Accessibilité

```tsx
// Card interactive
<Card
  role="article"
  tabIndex={0}
  onClick={handleClick}
  onKeyPress={(e) => e.key === 'Enter' && handleClick()}
>
  {/* Contenu */}
</Card>

// Card link
<Card
  asLink
  href="/detail"
  aria-label="Voir les détails de l'article"
>
  {/* Contenu */}
</Card>
```

---

## 📚 Points clés

1. **Padding cohérent** : 24px standard
2. **Border radius** : 8-12px
3. **Ombre subtile** : var(--shadow-sm)
4. **Hover state** : Transform + shadow
5. **Spacing interne** : 16px gap
6. **Interactive** : Focus visible
7. **Composition** : Header, Content, Footer modulaires

---

*Chapitre 02.3 | Cards*

