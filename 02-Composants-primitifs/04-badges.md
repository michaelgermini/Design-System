# Badges : Étiquettes et indicateurs

## 🎯 Objectifs

- ✓ Créer des badges pour statuts et compteurs
- ✓ Implémenter les variants de couleur
- ✓ Gérer les tailles et positions
- ✓ Optimiser la lisibilité

---

## 📐 Anatomie d'un Badge

```
┌─────────────────┐
│  [●] Label      │  ← Icon (opt) + Text
└─────────────────┘
 ↑              ↑
Padding      Border radius
```

---

## 🎨 Variants

### 1. Status Badges

```tsx
<Badge variant="success">Actif</Badge>
<Badge variant="warning">En attente</Badge>
<Badge variant="error">Erreur</Badge>
<Badge variant="info">Info</Badge>
<Badge variant="neutral">Brouillon</Badge>
```

### 2. Count Badges

```tsx
<Badge count={5} />
<Badge count={99} max={99} />  {/* 99+ */}
```

### 3. Dot Badges (Indicateur)

```tsx
<Badge dot variant="success" />
```

---

## 🎨 Styles

```css
.badge {
  display: inline-flex;
  align-items: center;
  gap: var(--spacing-1);
  padding: 2px 8px;
  font-size: 12px;
  font-weight: 500;
  border-radius: var(--radius-full);
  line-height: 1.5;
}

/* Variants */
.badge-success {
  background: var(--color-success-100);
  color: var(--color-success-700);
}

.badge-warning {
  background: var(--color-warning-100);
  color: var(--color-warning-700);
}

.badge-error {
  background: var(--color-error-100);
  color: var(--color-error-700);
}

.badge-info {
  background: var(--color-info-100);
  color: var(--color-info-700);
}

.badge-neutral {
  background: var(--color-gray-100);
  color: var(--color-gray-700);
}
```

---

## 📏 Tailles

```tsx
<Badge size="sm">Small</Badge>  {/* 12px */}
<Badge size="md">Medium</Badge> {/* 14px */}
<Badge size="lg">Large</Badge>  {/* 16px */}
```

---

## 💻 Implémentation

```tsx
interface BadgeProps {
  variant?: 'success' | 'warning' | 'error' | 'info' | 'neutral';
  size?: 'sm' | 'md' | 'lg';
  count?: number;
  max?: number;
  dot?: boolean;
  icon?: React.ReactNode;
  children?: React.ReactNode;
}

export const Badge: React.FC<BadgeProps> = ({
  variant = 'neutral',
  size = 'md',
  count,
  max = 99,
  dot,
  icon,
  children
}) => {
  const displayCount = count && count > max ? `${max}+` : count;
  
  if (dot) {
    return <span className={`badge-dot badge-dot-${variant}`} />;
  }
  
  if (count !== undefined) {
    return (
      <span className={`badge badge-${variant} badge-${size}`}>
        {displayCount}
      </span>
    );
  }
  
  return (
    <span className={`badge badge-${variant} badge-${size}`}>
      {icon && <span className="badge-icon">{icon}</span>}
      {children}
    </span>
  );
};
```

---

## 🎯 Positionnement

```tsx
// Badge sur un bouton
<div className="relative">
  <Button>Messages</Button>
  <Badge count={3} className="absolute -top-2 -right-2" />
</div>
```

```css
.badge-positioned {
  position: absolute;
  top: -8px;
  right: -8px;
  z-index: 1;
}
```

---

## ♿ Accessibilité

```tsx
<Badge
  count={5}
  aria-label="5 messages non lus"
/>

<Badge dot variant="error" aria-label="Statut: Erreur" />
```

---

## 📚 Points clés

1. **Contraste** : Fond clair, texte foncé (4.5:1 mini)
2. **Taille** : 12-16px selon importance
3. **Border radius** : Arrondi complet (9999px)
4. **Padding** : 2-4px vertical, 6-12px horizontal
5. **Compteur** : Max 99+
6. **ARIA** : Label descriptif
7. **Position** : Absolute sur parent relative

---

*Chapitre 02.4 | Badges*

