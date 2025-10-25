# Pratiques Réutilisables pour les Composants

## 🎯 Objectifs

- ✓ Maîtriser les patterns de composition
- ✓ Optimiser la réutilisabilité et la maintenabilité
- ✓ Gérer les props et le polymorphisme
- ✓ Implémenter les patterns avancés

---

## 🧩 Composition vs Héritage

### ❌ Héritage (à éviter)

```tsx
class BaseButton extends React.Component { }
class PrimaryButton extends BaseButton { }
class SecondaryButton extends BaseButton { }
```

### ✓ Composition (recommandé)

```tsx
<Button variant="primary">
<Button variant="secondary">
```

**Principe** : Préférer la composition à l'héritage

---

## 🎨 Pattern : Compound Components

### Exemple : Card

```tsx
// ✓ Flexible et composable
<Card>
  <CardHeader>
    <CardTitle>Titre</CardTitle>
  </CardHeader>
  <CardContent>
    Contenu
  </CardContent>
  <CardFooter>
    <Button>Action</Button>
  </CardFooter>
</Card>
```

### Implémentation

```tsx
const CardContext = React.createContext<CardContextType | null>(null);

export const Card = ({ children }: { children: React.ReactNode }) => {
  const value = { /* shared state */ };
  
  return (
    <CardContext.Provider value={value}>
      <div className="card">{children}</div>
    </CardContext.Provider>
  );
};

Card.Header = CardHeader;
Card.Title = CardTitle;
Card.Content = CardContent;
Card.Footer = CardFooter;
```

---

## 🔧 Pattern : Polymorphic Components

### Composant "as" prop

```tsx
<Button as="a" href="/link">Lien</Button>
<Button as="div">Div</Button>
<Button>Bouton</Button> {/* défaut */}
```

### Implémentation TypeScript

```tsx
type AsProp<C extends React.ElementType> = {
  as?: C;
};

type PropsToOmit<C extends React.ElementType, P> = keyof (AsProp<C> & P);

type PolymorphicComponentProp<
  C extends React.ElementType,
  Props = {}
> = React.PropsWithChildren<Props & AsProp<C>> &
  Omit<React.ComponentPropsWithoutRef<C>, PropsToOmit<C, Props>>;

type ButtonProps<C extends React.ElementType> = PolymorphicComponentProp<
  C,
  {
    variant?: 'primary' | 'secondary';
  }
>;

export const Button = <C extends React.ElementType = 'button'>({
  as,
  variant = 'primary',
  children,
  ...props
}: ButtonProps<C>) => {
  const Component = as || 'button';
  
  return (
    <Component className={`button-${variant}`} {...props}>
      {children}
    </Component>
  );
};
```

---

## 🎯 Pattern : Render Props

```tsx
<DataFetcher url="/api/users">
  {({ data, loading, error }) => {
    if (loading) return <Spinner />;
    if (error) return <Error />;
    return <UserList users={data} />;
  }}
</DataFetcher>
```

### Implémentation

```tsx
interface DataFetcherProps<T> {
  url: string;
  children: (state: {
    data: T | null;
    loading: boolean;
    error: Error | null;
  }) => React.ReactNode;
}

export function DataFetcher<T>({ url, children }: DataFetcherProps<T>) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);
  
  return <>{children({ data, loading, error })}</>;
}
```

---

## 🔌 Pattern : Slot Props

```tsx
<Dialog
  trigger={<Button>Ouvrir</Button>}
  content={<div>Contenu</div>}
  footer={<Button>Fermer</Button>}
/>
```

---

## ⚡ Pattern : Controlled vs Uncontrolled

### Controlled (recommandé)

```tsx
const [value, setValue] = useState('');

<Input
  value={value}
  onChange={(e) => setValue(e.target.value)}
/>
```

### Uncontrolled

```tsx
const inputRef = useRef<HTMLInputElement>(null);

<Input ref={inputRef} defaultValue="initial" />

// Accès à la valeur
const value = inputRef.current?.value;
```

### Hybrid (meilleure pratique)

```tsx
interface InputProps {
  value?: string;           // Controlled
  defaultValue?: string;    // Uncontrolled
  onChange?: (value: string) => void;
}

export const Input = ({ value, defaultValue, onChange }: InputProps) => {
  const [internalValue, setInternalValue] = useState(defaultValue ?? '');
  
  const isControlled = value !== undefined;
  const currentValue = isControlled ? value : internalValue;
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const newValue = e.target.value;
    
    if (!isControlled) {
      setInternalValue(newValue);
    }
    
    onChange?.(newValue);
  };
  
  return (
    <input
      value={currentValue}
      onChange={handleChange}
    />
  );
};
```

---

## 🎨 Pattern : Variants avec CVA

```tsx
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-white',
        secondary: 'bg-secondary text-white',
        ghost: 'bg-transparent'
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-12 px-6 text-lg'
      },
      disabled: {
        true: 'opacity-50 pointer-events-none'
      }
    },
    compoundVariants: [
      {
        variant: 'primary',
        disabled: true,
        class: 'bg-gray-300'
      }
    ],
    defaultVariants: {
      variant: 'primary',
      size: 'md'
    }
  }
);

type ButtonProps = VariantProps<typeof buttonVariants> & {
  children: React.ReactNode;
};

export const Button = ({ variant, size, disabled, children }: ButtonProps) => {
  return (
    <button className={buttonVariants({ variant, size, disabled })}>
      {children}
    </button>
  );
};
```

---

## 🧪 Pattern : Testable Components

```tsx
// Composant séparant logique et UI
export const useCounter = (initialValue = 0) => {
  const [count, setCount] = useState(initialValue);
  
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(initialValue);
  
  return { count, increment, decrement, reset };
};

export const Counter = ({ initialValue }: { initialValue?: number }) => {
  const { count, increment, decrement, reset } = useCounter(initialValue);
  
  return (
    <div>
      <span>{count}</span>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
};

// Tests
describe('useCounter', () => {
  test('increments count', () => {
    const { result } = renderHook(() => useCounter(0));
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

---

## 🔄 Pattern : Forwarded Refs

```tsx
export const Input = React.forwardRef<
  HTMLInputElement,
  InputProps
>(({ className, ...props }, ref) => {
  return (
    <input
      ref={ref}
      className={cn('input', className)}
      {...props}
    />
  );
});

Input.displayName = 'Input';

// Usage
const inputRef = useRef<HTMLInputElement>(null);

<Input ref={inputRef} />

inputRef.current?.focus();
```

---

## 🎯 Pattern : Default Props

```tsx
// Méthode 1 : Valeur par défaut dans destructuring
export const Button = ({
  variant = 'primary',
  size = 'md',
  children
}: ButtonProps) => {
  // ...
};

// Méthode 2 : defaultProps (legacy)
Button.defaultProps = {
  variant: 'primary',
  size: 'md'
};
```

---

## 🔧 Utilitaires : cn() helper

```tsx
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Usage
<button
  className={cn(
    'base-class',
    variant === 'primary' && 'primary-class',
    disabled && 'disabled-class',
    className // props externes
  )}
/>
```

---

## 📚 Checklist de réutilisabilité

```
COMPOSITION
☐ Préférer la composition à l'héritage
☐ Compound components pour flexibilité
☐ Slots props pour customisation

FLEXIBILITÉ
☐ Props polymorphes (as prop)
☐ Variants avec CVA
☐ Ref forwarding
☐ Controlled + Uncontrolled support

TYPES
☐ Props TypeScript strictement typées
☐ Generics pour composants data
☐ Omit/Pick pour réutiliser types

TESTS
☐ Logique séparée de l'UI (hooks)
☐ Props data-testid
☐ Tests unitaires hooks
☐ Tests intégration composants

DOCUMENTATION
☐ JSDoc sur props
☐ Exemples Storybook
☐ README avec usage
☐ Props table auto-générée
```

---

## 📊 Patterns Comparison

| Pattern | Flexibilité | Complexité | Usage |
|---------|-------------|------------|-------|
| **Props** | ⭐⭐ | ⭐ | Simple config |
| **Compound** | ⭐⭐⭐⭐ | ⭐⭐⭐ | Composition complexe |
| **Render Props** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Logique réutilisable |
| **Polymorphic** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Element variable |
| **Slots** | ⭐⭐⭐ | ⭐⭐ | Zones customisables |

---

## 📚 Points clés

1. **Composition > Héritage** : Toujours
2. **Compound Components** : Pour flexibilité
3. **Polymorphic** : as prop pour element type
4. **Controlled/Uncontrolled** : Supporter les deux
5. **CVA** : Variants type-safe
6. **Ref forwarding** : Pour accès DOM
7. **Séparation** : Logique (hooks) vs UI (components)
8. **TypeScript** : Types stricts et génériques

---

*Chapitre 02.6 | Pratiques Réutilisables*

