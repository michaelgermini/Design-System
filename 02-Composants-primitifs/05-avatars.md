# Avatars : Images de profil

## 🎯 Objectifs

- ✓ Créer des avatars adaptables et accessibles
- ✓ Gérer les fallbacks (initiales, icône)
- ✓ Implémenter les tailles et variants
- ✓ Optimiser les performances d'image

---

## 📐 Anatomie d'un Avatar

```
┌─────────────┐
│             │
│   [Image]   │  ← Image ou Initiales ou Icône
│             │
└─────────────┘
    ↑     ↑
  Border  Round
```

---

## 🎨 Types d'Avatars

### 1. Avatar avec image

```tsx
<Avatar src="/user.jpg" alt="John Doe" />
```

### 2. Avatar avec initiales

```tsx
<Avatar name="John Doe" />  {/* Affiche "JD" */}
```

### 3. Avatar avec icône (fallback)

```tsx
<Avatar>
  <User />
</Avatar>
```

---

## 📏 Tailles

```tsx
<Avatar size="xs" />  {/* 24px */}
<Avatar size="sm" />  {/* 32px */}
<Avatar size="md" />  {/* 40px */}
<Avatar size="lg" />  {/* 48px */}
<Avatar size="xl" />  {/* 64px */}
<Avatar size="2xl" /> {/* 96px */}
```

---

## 💻 Implémentation

```tsx
interface AvatarProps {
  src?: string;
  alt?: string;
  name?: string;
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl' | '2xl';
  shape?: 'circle' | 'square';
  children?: React.ReactNode;
}

export const Avatar: React.FC<AvatarProps> = ({
  src,
  alt,
  name,
  size = 'md',
  shape = 'circle',
  children
}) => {
  const [imageError, setImageError] = useState(false);
  
  const getInitials = (name: string) => {
    return name
      .split(' ')
      .map(n => n[0])
      .join('')
      .toUpperCase()
      .slice(0, 2);
  };
  
  const sizeClasses = {
    xs: 'w-6 h-6 text-xs',
    sm: 'w-8 h-8 text-sm',
    md: 'w-10 h-10 text-base',
    lg: 'w-12 h-12 text-lg',
    xl: 'w-16 h-16 text-xl',
    '2xl': 'w-24 h-24 text-2xl'
  };
  
  return (
    <div
      className={cn(
        'avatar',
        sizeClasses[size],
        shape === 'circle' ? 'rounded-full' : 'rounded-md'
      )}
    >
      {src && !imageError ? (
        <img
          src={src}
          alt={alt || name}
          onError={() => setImageError(true)}
        />
      ) : name ? (
        <span className="avatar-initials">
          {getInitials(name)}
        </span>
      ) : (
        <span className="avatar-icon">
          {children || <User />}
        </span>
      )}
    </div>
  );
};
```

---

## 🎨 Styles

```css
.avatar {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background: var(--color-gray-200);
  color: var(--color-gray-700);
  font-weight: 500;
}

.avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-initials {
  user-select: none;
}

.avatar-icon {
  width: 60%;
  height: 60%;
}
```

---

## 🖼️ Avatar Group

```tsx
<AvatarGroup max={3}>
  <Avatar src="/user1.jpg" />
  <Avatar src="/user2.jpg" />
  <Avatar src="/user3.jpg" />
  <Avatar src="/user4.jpg" />
</AvatarGroup>
```

```tsx
export const AvatarGroup: React.FC<{
  children: React.ReactNode;
  max?: number;
}> = ({ children, max = 5 }) => {
  const avatars = React.Children.toArray(children);
  const visibleAvatars = avatars.slice(0, max);
  const hiddenCount = avatars.length - max;
  
  return (
    <div className="avatar-group">
      {visibleAvatars}
      {hiddenCount > 0 && (
        <Avatar>+{hiddenCount}</Avatar>
      )}
    </div>
  );
};
```

```css
.avatar-group {
  display: inline-flex;
  align-items: center;
}

.avatar-group > * {
  margin-left: -8px;
  border: 2px solid var(--color-bg-primary);
}

.avatar-group > *:first-child {
  margin-left: 0;
}
```

---

## 🎯 Status Indicator

```tsx
<Avatar src="/user.jpg" status="online" />
```

```tsx
<div className="relative">
  <Avatar src="/user.jpg" />
  <span className={`avatar-status avatar-status-${status}`} />
</div>
```

```css
.avatar-status {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 25%;
  height: 25%;
  border-radius: 50%;
  border: 2px solid var(--color-bg-primary);
}

.avatar-status-online {
  background: var(--color-success-500);
}

.avatar-status-offline {
  background: var(--color-gray-400);
}

.avatar-status-busy {
  background: var(--color-error-500);
}
```

---

## ♿ Accessibilité

```tsx
<Avatar
  src="/user.jpg"
  alt="Photo de profil de John Doe"
/>

<Avatar
  name="John Doe"
  aria-label="Avatar de John Doe"
/>
```

---

## ⚡ Optimisation

```tsx
// Lazy loading
<Avatar
  src="/user.jpg"
  loading="lazy"
/>

// Srcset pour différentes tailles
<Avatar
  src="/user-400.jpg"
  srcSet="/user-400.jpg 400w, /user-800.jpg 800w"
  sizes="(max-width: 768px) 40px, 64px"
/>
```

---

## 📚 Points clés

1. **Fallback** : Initiales > Icône > Placeholder
2. **Tailles** : 24-96px selon contexte
3. **Shape** : Circle (défaut) ou Square
4. **Border** : 2-3px sur fond coloré
5. **Alt text** : Nom de la personne
6. **Status** : Dot en bas à droite (25% taille)
7. **Group** : Overlap -8px, border blanc
8. **Loading** : Lazy par défaut

---

*Chapitre 02.5 | Avatars*

