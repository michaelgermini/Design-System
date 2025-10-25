# Formulaires Complexes

## 🎯 Objectifs

- ✓ Créer des formulaires accessibles et ergonomiques
- ✓ Gérer la validation complexe
- ✓ Implémenter les patterns UX optimaux
- ✓ Optimiser les performances

---

## 📐 Architecture d'un formulaire

```
┌─────────────────────────────────────────────┐
│ Form Title                                  │
│ Description (optionnelle)                   │
├─────────────────────────────────────────────┤
│ Section 1: Informations personnelles        │
│ ├─ Input: Nom                              │
│ ├─ Input: Email                            │
│ └─ Input: Téléphone                        │
├─────────────────────────────────────────────┤
│ Section 2: Adresse                          │
│ ├─ Input: Rue                              │
│ ├─ Select: Pays                            │
│ └─ Input: Code postal                      │
├─────────────────────────────────────────────┤
│ [Annuler]                     [Enregistrer] │
└─────────────────────────────────────────────┘
```

---

## 🎨 Patterns de formulaire

### 1. Form avec React Hook Form

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';

const formSchema = z.object({
  name: z.string().min(2, 'Minimum 2 caractères'),
  email: z.string().email('Email invalide'),
  age: z.number().min(18, 'Minimum 18 ans')
});

type FormData = z.infer<typeof formSchema>;

export function UserForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm<FormData>({
    resolver: zodResolver(formSchema)
  });
  
  const onSubmit = async (data: FormData) => {
    await saveUser(data);
  };
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input
        label="Nom"
        {...register('name')}
        error={errors.name?.message}
      />
      
      <Input
        label="Email"
        type="email"
        {...register('email')}
        error={errors.email?.message}
      />
      
      <Input
        label="Âge"
        type="number"
        {...register('age', { valueAsNumber: true })}
        error={errors.age?.message}
      />
      
      <Button
        type="submit"
        loading={isSubmitting}
        disabled={isSubmitting}
      >
        Enregistrer
      </Button>
    </form>
  );
}
```

---

## ✓ Validation

### Validation côté client

```tsx
// Validation inline (temps réel)
<Input
  onChange={(e) => {
    const value = e.target.value;
    if (!isEmail(value)) {
      setError('Email invalide');
    }
  }}
/>

// Validation sur blur (perte de focus)
<Input
  onBlur={(e) => {
    validateField(e.target.value);
  }}
/>

// Validation sur submit
const onSubmit = (data) => {
  const errors = validate(data);
  if (Object.keys(errors).length > 0) {
    setErrors(errors);
    return;
  }
  // Soumettre
};
```

### Validation côté serveur

```tsx
const onSubmit = async (data) => {
  try {
    await api.post('/users', data);
  } catch (error) {
    if (error.validationErrors) {
      // Afficher erreurs serveur
      setErrors(error.validationErrors);
    }
  }
};
```

---

## 🎯 UX Patterns

### 1. Feedback immédiat

```tsx
<Input
  value={email}
  onChange={handleChange}
  rightIcon={
    isValidating ? <Spinner /> :
    isValid ? <Check className="text-success" /> :
    hasError ? <X className="text-error" /> :
    null
  }
/>
```

### 2. Champs conditionnels

```tsx
<Select
  label="Type de compte"
  value={accountType}
  onChange={setAccountType}
>
  <option value="personal">Personnel</option>
  <option value="business">Professionnel</option>
</Select>

{accountType === 'business' && (
  <>
    <Input label="Nom de l'entreprise" />
    <Input label="SIRET" />
  </>
)}
```

### 3. Multi-step forms

```tsx
const [step, setStep] = useState(1);

return (
  <div>
    <Steps current={step} total={3} />
    
    {step === 1 && <PersonalInfo onNext={() => setStep(2)} />}
    {step === 2 && <Address onNext={() => setStep(3)} onBack={() => setStep(1)} />}
    {step === 3 && <Confirmation onBack={() => setStep(2)} />}
  </div>
);
```

---

## ♿ Accessibilité

```tsx
<form
  onSubmit={handleSubmit}
  aria-label="Formulaire d'inscription"
  noValidate // Validation custom
>
  <fieldset>
    <legend>Informations personnelles</legend>
    
    <Input
      id="name"
      label="Nom"
      required
      aria-required="true"
      aria-invalid={!!errors.name}
      aria-describedby={errors.name ? 'name-error' : undefined}
    />
    
    {errors.name && (
      <span
        id="name-error"
        role="alert"
        className="error"
      >
        {errors.name}
      </span>
    )}
  </fieldset>
  
  <Button type="submit">
    Valider
  </Button>
</form>
```

---

## ⚡ Performance

### Debouncing

```tsx
import { useDebouncedCallback } from 'use-debounce';

const debouncedValidate = useDebouncedCallback(
  (value) => validateEmail(value),
  500
);

<Input
  onChange={(e) => debouncedValidate(e.target.value)}
/>
```

### Memoization

```tsx
const expensiveOptions = useMemo(
  () => computeOptions(data),
  [data]
);
```

---

## 📚 Points clés

1. **Validation** : Client + Serveur
2. **Feedback** : Immédiat et clair
3. **Labels** : Toujours visibles (pas placeholder seul)
4. **Required** : Indication visuelle + ARIA
5. **Erreurs** : Associées aux champs (aria-describedby)
6. **Submit** : État loading pendant traitement
7. **Focus** : Premier champ en erreur
8. **Autosave** : Pour formulaires longs

---

*Chapitre 03.1 | Formulaires*

