# Patterns d'Authentification

## 🎯 Objectifs

- ✓ Créer des flows d'authentification sécurisés et UX optimaux
- ✓ Implémenter login, signup, reset password
- ✓ Gérer les sessions et tokens
- ✓ Optimiser la conversion

---

## 🔐 Login Form

```tsx
export function LoginForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting } } = useForm<{
    email: string;
    password: string;
    remember: boolean;
  }>();
  
  const onSubmit = async (data) => {
    try {
      await login(data);
      router.push('/dashboard');
    } catch (error) {
      setError(error.message);
    }
  };
  
  return (
    <Card className="max-w-md mx-auto">
      <CardHeader>
        <CardTitle>Connexion</CardTitle>
        <CardDescription>
          Accédez à votre compte
        </CardDescription>
      </CardHeader>
      
      <form onSubmit={handleSubmit(onSubmit)}>
        <CardContent className="space-y-4">
          <Input
            label="Email"
            type="email"
            autoComplete="email"
            {...register('email', {
              required: 'Email requis',
              pattern: {
                value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
                message: 'Email invalide'
              }
            })}
            error={errors.email?.message}
          />
          
          <Input
            label="Mot de passe"
            type="password"
            autoComplete="current-password"
            {...register('password', {
              required: 'Mot de passe requis'
            })}
            error={errors.password?.message}
          />
          
          <div className="flex items-center justify-between">
            <Checkbox
              label="Se souvenir de moi"
              {...register('remember')}
            />
            
            <Link href="/forgot-password" className="text-sm">
              Mot de passe oublié ?
            </Link>
          </div>
        </CardContent>
        
        <CardFooter className="flex-col space-y-4">
          <Button
            type="submit"
            className="w-full"
            loading={isSubmitting}
          >
            Se connecter
          </Button>
          
          <div className="text-center text-sm">
            Pas encore de compte ?{' '}
            <Link href="/signup">S'inscrire</Link>
          </div>
          
          <Divider>ou</Divider>
          
          <Button
            variant="secondary"
            className="w-full"
            leftIcon={<GoogleIcon />}
            onClick={loginWithGoogle}
          >
            Continuer avec Google
          </Button>
        </CardFooter>
      </form>
    </Card>
  );
}
```

---

## ✍️ Signup Form

```tsx
const passwordSchema = z.string()
  .min(8, 'Minimum 8 caractères')
  .regex(/[A-Z]/, 'Au moins 1 majuscule')
  .regex(/[a-z]/, 'Au moins 1 minuscule')
  .regex(/[0-9]/, 'Au moins 1 chiffre');

const signupSchema = z.object({
  name: z.string().min(2, 'Minimum 2 caractères'),
  email: z.string().email('Email invalide'),
  password: passwordSchema,
  confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
  message: 'Les mots de passe ne correspondent pas',
  path: ['confirmPassword']
});

export function SignupForm() {
  const { register, handleSubmit, watch, formState } = useForm({
    resolver: zodResolver(signupSchema)
  });
  
  const password = watch('password');
  const strength = calculatePasswordStrength(password);
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input
        label="Nom complet"
        autoComplete="name"
        {...register('name')}
        error={formState.errors.name?.message}
      />
      
      <Input
        label="Email"
        type="email"
        autoComplete="email"
        {...register('email')}
        error={formState.errors.email?.message}
      />
      
      <div>
        <Input
          label="Mot de passe"
          type="password"
          autoComplete="new-password"
          {...register('password')}
          error={formState.errors.password?.message}
        />
        
        <PasswordStrength value={strength} />
      </div>
      
      <Input
        label="Confirmer le mot de passe"
        type="password"
        autoComplete="new-password"
        {...register('confirmPassword')}
        error={formState.errors.confirmPassword?.message}
      />
      
      <Checkbox
        label={
          <>
            J'accepte les{' '}
            <Link href="/terms">conditions d'utilisation</Link>
          </>
        }
        required
      />
      
      <Button type="submit" className="w-full" loading={isSubmitting}>
        Créer mon compte
      </Button>
    </form>
  );
}
```

---

## 🔑 Forgot Password

```tsx
export function ForgotPasswordFlow() {
  const [step, setStep] = useState<'email' | 'sent' | 'reset'>('email');
  const [email, setEmail] = useState('');
  
  if (step === 'email') {
    return (
      <form onSubmit={handleSendEmail}>
        <Input
          label="Adresse email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          helperText="Nous vous enverrons un lien de réinitialisation"
        />
        <Button type="submit">Envoyer</Button>
      </form>
    );
  }
  
  if (step === 'sent') {
    return (
      <div className="text-center">
        <CheckCircle className="w-16 h-16 text-success mx-auto mb-4" />
        <h2>Email envoyé</h2>
        <p>
          Consultez votre boîte mail ({email}) pour réinitialiser votre mot de passe.
        </p>
        <Button onClick={() => setStep('email')}>
          Renvoyer l'email
        </Button>
      </div>
    );
  }
  
  // Reset password form with token validation
  return <ResetPasswordForm token={tokenFromUrl} />;
}
```

---

## 🔐 2FA (Authentification à 2 facteurs)

```tsx
export function TwoFactorAuth() {
  const [code, setCode] = useState(['', '', '', '', '', '']);
  const inputRefs = useRef<(HTMLInputElement | null)[]>([]);
  
  const handleChange = (index: number, value: string) => {
    if (value.length > 1) return;
    
    const newCode = [...code];
    newCode[index] = value;
    setCode(newCode);
    
    // Auto-focus next input
    if (value && index < 5) {
      inputRefs.current[index + 1]?.focus();
    }
  };
  
  const handleKeyDown = (index: number, e: React.KeyboardEvent) => {
    if (e.key === 'Backspace' && !code[index] && index > 0) {
      inputRefs.current[index - 1]?.focus();
    }
  };
  
  return (
    <div>
      <p className="mb-4">Entrez le code à 6 chiffres envoyé par SMS</p>
      
      <div className="flex gap-2 justify-center">
        {code.map((digit, index) => (
          <input
            key={index}
            ref={(el) => (inputRefs.current[index] = el)}
            type="text"
            inputMode="numeric"
            pattern="[0-9]"
            maxLength={1}
            value={digit}
            onChange={(e) => handleChange(index, e.target.value)}
            onKeyDown={(e) => handleKeyDown(index, e)}
            className="w-12 h-14 text-center text-2xl border-2 rounded-md"
            aria-label={`Chiffre ${index + 1}`}
          />
        ))}
      </div>
      
      <Button
        onClick={() => verify(code.join(''))}
        disabled={code.some(d => !d)}
        className="w-full mt-4"
      >
        Vérifier
      </Button>
      
      <button
        onClick={resendCode}
        className="text-sm text-center w-full mt-4"
      >
        Renvoyer le code
      </button>
    </div>
  );
}
```

---

## 🎨 Social Login Buttons

```tsx
const socialProviders = [
  { name: 'Google', icon: GoogleIcon, color: '#4285F4' },
  { name: 'GitHub', icon: GitHubIcon, color: '#333' },
  { name: 'Apple', icon: AppleIcon, color: '#000' }
];

export function SocialAuth() {
  return (
    <div className="space-y-3">
      {socialProviders.map(provider => (
        <Button
          key={provider.name}
          variant="secondary"
          className="w-full"
          leftIcon={<provider.icon />}
          onClick={() => loginWith(provider.name.toLowerCase())}
        >
          Continuer avec {provider.name}
        </Button>
      ))}
    </div>
  );
}
```

---

## ♿ Accessibilité

```tsx
// Labels explicites
<Input
  id="email"
  label="Adresse email"
  aria-required="true"
  aria-describedby="email-hint email-error"
/>
<span id="email-hint">Format : nom@example.com</span>
{error && <span id="email-error" role="alert">{error}</span>}

// Messages d'erreur annoncés
<div role="alert" aria-live="assertive">
  {loginError}
</div>

// Autofocus sur premier champ
<Input autoFocus label="Email" />
```

---

## 🔒 Sécurité Best Practices

```tsx
// 1. Rate limiting côté serveur
// 2. CAPTCHA après 3 tentatives
{attempts >= 3 && <Captcha onVerify={handleVerify} />}

// 3. Masquer les erreurs spécifiques
// ❌ "Email invalide"
// ✓ "Email ou mot de passe incorrect"

// 4. HTTPS uniquement
// 5. HttpOnly cookies pour tokens
// 6. CSRF tokens
// 7. Content Security Policy headers
```

---

## 📊 UX Metrics

```
Conversion Funnel:
──────────────────────────────────────
Landing Page      100% (1000 users)
    ↓
Signup Start      40%  (400 users)  ← Drop-off : trop complexe ?
    ↓
Form Completed    75%  (300 users)  ← Drop-off : validation ?
    ↓
Email Verified    85%  (255 users)  ← Drop-off : email non reçu ?
    ↓
First Login       95%  (242 users)
──────────────────────────────────────
Overall CR:       24.2%

Optimizations:
- Social login: +15% conversion
- Email pre-fill: +8% conversion
- Password strength indicator: +5% completion
- Inline validation: +12% completion
```

---

## 📚 Points clés

1. **Validation** : Temps réel avec debounce
2. **Password** : Force indicator, min 8 chars
3. **Autocomplete** : Utiliser les attributs HTML5
4. **Social login** : Augmente conversion 10-20%
5. **2FA** : Optionnel mais recommandé
6. **Forgot password** : Flow simple 3 étapes max
7. **Errors** : Messages clairs, pas de détails sécurité
8. **Loading** : Indicateur pendant requête

---

*Chapitre 04.1 | Authentification*

