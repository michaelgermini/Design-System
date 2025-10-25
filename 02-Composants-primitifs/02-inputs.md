# Inputs : Champs de saisie et formulaires

## 🎯 Objectifs

- ✓ Créer des inputs accessibles et ergonomiques
- ✓ Gérer la validation et les états d'erreur
- ✓ Implémenter tous les types d'inputs (text, select, textarea, etc.)
- ✓ Optimiser l'expérience mobile

---

## 📐 Anatomie d'un Input

```
┌─────────────────────────────────────────────┐
│ Label *                           [i]       │ ← Label + Required + Help
├─────────────────────────────────────────────┤
│ │ [Icon]  Placeholder text...         │    │ ← Input field
│ └──────────────────────────────────────┘    │
│ ℹ️ Message d'aide ou d'erreur               │ ← Helper/Error text
└─────────────────────────────────────────────┘
```

---

## 🎨 Types d'Inputs

### 1. Text Input (Basique)

```tsx
<Input
  label="Email"
  type="email"
  placeholder="vous@exemple.com"
  required
/>
```

### 2. Textarea

```tsx
<Textarea
  label="Message"
  rows={4}
  maxLength={500}
/>
```

### 3. Select

```tsx
<Select label="Pays">
  <option value="fr">France</option>
  <option value="be">Belgique</option>
</Select>
```

### 4. Checkbox & Radio

```tsx
<Checkbox label="J'accepte les conditions" />
<Radio name="plan" value="basic" label="Plan Basic" />
```

---

## 📏 Tailles

| Taille | Height | Font | Usage |
|--------|--------|------|-------|
| sm | 32px | 14px | Forms compacts |
| md | 40px | 16px | **Standard** |
| lg | 48px | 18px | Hero, landing |

---

## 🎭 États

```
Default → Focus → Valid/Error → Disabled
```

### CSS Implementation

```css
.input {
  height: 40px;
  padding: 0 12px;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  transition: all 150ms;
}

.input:hover {
  border-color: var(--color-border-strong);
}

.input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px var(--color-primary-100);
}

.input.error {
  border-color: var(--color-error-500);
}

.input:disabled {
  background: var(--color-gray-100);
  cursor: not-allowed;
  opacity: 0.6;
}
```

---

## ♿ Accessibilité

### Labels obligatoires

```tsx
// ✓ Bon
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ❌ Mauvais (placeholder seul)
<input type="email" placeholder="Email" />
```

### Messages d'erreur

```tsx
<Input
  id="email"
  aria-invalid={hasError}
  aria-describedby="email-error"
/>
<span id="email-error" role="alert">
  {errorMessage}
</span>
```

---

## ✓ Validation

### Patterns de validation

```typescript
const validators = {
  email: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
  phone: /^(\+33|0)[1-9](\d{2}){4}$/,
  password: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/,
};

function validateField(value: string, type: string) {
  return validators[type]?.test(value) ?? true;
}
```

---

## 💻 Implémentation complète

```tsx
interface InputProps {
  label: string;
  type?: 'text' | 'email' | 'password' | 'number';
  error?: string;
  helperText?: string;
  required?: boolean;
  disabled?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}

export const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, helperText, required, leftIcon, rightIcon, ...props }, ref) => {
    const id = useId();
    const errorId = `${id}-error`;
    const helperId = `${id}-helper`;
    
    return (
      <div className="input-wrapper">
        <label htmlFor={id}>
          {label}
          {required && <span aria-label="requis">*</span>}
        </label>
        
        <div className="input-container">
          {leftIcon && <span className="input-icon-left">{leftIcon}</span>}
          
          <input
            id={id}
            ref={ref}
            className={error ? 'input error' : 'input'}
            aria-invalid={!!error}
            aria-describedby={error ? errorId : helperText ? helperId : undefined}
            aria-required={required}
            {...props}
          />
          
          {rightIcon && <span className="input-icon-right">{rightIcon}</span>}
        </div>
        
        {error && (
          <span id={errorId} role="alert" className="error-message">
            {error}
          </span>
        )}
        
        {helperText && !error && (
          <span id={helperId} className="helper-text">
            {helperText}
          </span>
        )}
      </div>
    );
  }
);
```

---

## 📱 Optimisation Mobile

```tsx
// Keyboard approprié
<input type="email" inputMode="email" />
<input type="tel" inputMode="tel" />
<input type="number" inputMode="numeric" />
<input type="url" inputMode="url" />

// Autocomplete
<input
  type="email"
  autoComplete="email"
/>
<input
  type="tel"
  autoComplete="tel"
/>
```

---

## 📚 Points clés

1. **Label obligatoire** (pas de placeholder seul)
2. **Hauteur minimum** : 44px sur mobile
3. **Focus visible** : Ring ou outline
4. **Validation claire** : Messages explicites
5. **Support clavier** : Tab, Enter, Escape
6. **ARIA** : invalid, describedby, required
7. **Mobile** : inputMode, autoComplete

---

*Chapitre 02.2 | Inputs*

