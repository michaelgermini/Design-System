# Intégration Front-end React

## 🎯 Objectifs

- ✓ Mettre en place l'architecture React
- ✓ Implémenter les composants avec TypeScript
- ✓ Gérer le styling (Tailwind, CSS-in-JS)
- ✓ Optimiser les performances

---

## 🏗️ Architecture

```
src/
├── components/
│   ├── ui/          # Design system primitives
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   └── ...
│   ├── composed/    # Composite components
│   │   ├── card.tsx
│   │   └── modal.tsx
│   └── patterns/    # Business patterns
│       ├── auth-form.tsx
│       └── dashboard.tsx
├── lib/
│   ├── utils.ts
│   └── cn.ts
├── hooks/
│   ├── use-toast.ts
│   └── use-theme.ts
└── styles/
    ├── globals.css
    └── tokens.css
```

---

## 🎨 Styling avec Tailwind

### Configuration

```js
// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eef2ff',
          500: '#6366f1',
          900: '#312e81'
        }
      },
      spacing: {
        1: '4px',
        2: '8px',
        4: '16px'
      },
      borderRadius: {
        sm: '4px',
        md: '6px',
        lg: '8px'
      }
    }
  }
};
```

---

## 💻 Composant Type-Safe

```tsx
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium transition-colors',
  {
    variants: {
      variant: {
        primary: 'bg-primary-500 text-white hover:bg-primary-600',
        secondary: 'border border-primary-500 text-primary-500'
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4',
        lg: 'h-12 px-6'
      }
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md'
    }
  }
);

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  loading?: boolean;
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, loading, children, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={buttonVariants({ variant, size, className })}
        disabled={loading}
        {...props}
      >
        {loading && <Spinner />}
        {children}
      </button>
    );
  }
);
```

---

## 🎣 Hooks réutilisables

```tsx
// use-toast.ts
export function useToast() {
  return {
    toast: (props: ToastProps) => toast(props),
    dismiss: (id: string) => toast.dismiss(id)
  };
}

// use-theme.ts
export function useTheme() {
  const [theme, setTheme] = useState<Theme>('light');
  
  useEffect(() => {
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);
  
  return { theme, setTheme };
}
```

---

## ⚡ Performance

### Code splitting

```tsx
const Dashboard = lazy(() => import('./Dashboard'));

<Suspense fallback={<Skeleton />}>
  <Dashboard />
</Suspense>
```

### Memoization

```tsx
const ExpensiveComponent = React.memo(({ data }: Props) => {
  return <div>{/* ... */}</div>;
});
```

---

## 📚 Points clés

1. **TypeScript** : Props strictement typées
2. **CVA** : Variants type-safe
3. **Forwarded refs** : Accès au DOM
4. **Tailwind** : Utility-first styling
5. **Code splitting** : Lazy loading
6. **Memoization** : React.memo, useMemo
7. **Hooks** : Logique réutilisable
8. **Tests** : Jest + RTL

---

*Chapitre 05.2 | Intégration Front-end React*

