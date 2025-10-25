# Erreurs et Feedbacks

## 🎯 Objectifs

- ✓ Gérer les états d'erreur de manière claire et actionnable
- ✓ Fournir un feedback immédiat et pertinent
- ✓ Créer des messages d'erreur utiles
- ✓ Implémenter les notifications et toasts

---

## 📐 Types de Feedback

### 1. Inline (dans le champ)
### 2. Toast/Snackbar (notification temporaire)
### 3. Alert/Banner (message persistant)
### 4. Dialog (action critique)
### 5. Empty State (absence de données)
### 6. Error Page (404, 500)

---

## ⚠️ Messages d'Erreur

### Anatomie d'un bon message d'erreur

```
[Icon] Titre clair
       Description du problème
       → Action pour résoudre
```

```tsx
// ❌ Mauvais
<span>Erreur</span>

// ✓ Bon
<Alert variant="error">
  <AlertIcon>⚠️</AlertIcon>
  <AlertTitle>Impossible d'enregistrer</AlertTitle>
  <AlertDescription>
    Le serveur ne répond pas. Vérifiez votre connexion internet.
  </AlertDescription>
  <AlertAction>
    <Button onClick={retry}>Réessayer</Button>
  </AlertAction>
</Alert>
```

### Principes des messages d'erreur

```
1. CLAIR : Que s'est-il passé ?
   ❌ "Erreur 422"
   ✓ "Format d'email invalide"

2. CAUSE : Pourquoi l'erreur ?
   ❌ "Échec"
   ✓ "Le serveur est temporairement indisponible"

3. ACTION : Comment résoudre ?
   ❌ "Erreur de connexion"
   ✓ "Vérifiez votre connexion internet et réessayez"

4. HUMAIN : Ton empathique
   ❌ "Invalid input format"
   ✓ "Oups ! Ce format n'est pas reconnu"
```

---

## 🍞 Toast Notifications

```tsx
import { toast } from 'sonner';

// Success
toast.success('Enregistré avec succès !');

// Error
toast.error('Impossible d'enregistrer', {
  description: 'Le serveur ne répond pas',
  action: {
    label: 'Réessayer',
    onClick: () => save()
  }
});

// Info
toast.info('Nouvelle mise à jour disponible');

// Warning
toast.warning('Votre session expire dans 5 minutes');

// Loading
const toastId = toast.loading('Envoi en cours...');
// Plus tard
toast.success('Envoyé !', { id: toastId });

// Custom
toast.custom(
  <div className="flex items-center gap-3">
    <Avatar src={user.avatar} />
    <div>
      <p className="font-medium">{user.name} vous a mentionné</p>
      <p className="text-sm text-muted-foreground">{message}</p>
    </div>
  </div>
);
```

### Implementation Toast Provider

```tsx
// app/layout.tsx
import { Toaster } from 'sonner';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Toaster
          position="top-right"
          richColors
          closeButton
          duration={4000}
        />
      </body>
    </html>
  );
}
```

---

## 📢 Alert Banners

```tsx
interface AlertProps {
  variant: 'info' | 'success' | 'warning' | 'error';
  title?: string;
  children: React.ReactNode;
  action?: React.ReactNode;
  onDismiss?: () => void;
}

export function Alert({
  variant,
  title,
  children,
  action,
  onDismiss
}: AlertProps) {
  const styles = {
    info: 'bg-blue-50 text-blue-900 border-blue-200',
    success: 'bg-green-50 text-green-900 border-green-200',
    warning: 'bg-yellow-50 text-yellow-900 border-yellow-200',
    error: 'bg-red-50 text-red-900 border-red-200'
  };
  
  const icons = {
    info: Info,
    success: CheckCircle,
    warning: AlertTriangle,
    error: XCircle
  };
  
  const Icon = icons[variant];
  
  return (
    <div className={cn('border rounded-lg p-4', styles[variant])}>
      <div className="flex items-start gap-3">
        <Icon className="w-5 h-5 mt-0.5 flex-shrink-0" />
        
        <div className="flex-1">
          {title && <div className="font-semibold mb-1">{title}</div>}
          <div className="text-sm">{children}</div>
          {action && <div className="mt-3">{action}</div>}
        </div>
        
        {onDismiss && (
          <button
            onClick={onDismiss}
            className="flex-shrink-0"
            aria-label="Fermer"
          >
            <X className="w-4 h-4" />
          </button>
        )}
      </div>
    </div>
  );
}

// Usage
<Alert
  variant="error"
  title="Erreur de paiement"
  action={<Button size="sm">Mettre à jour</Button>}
  onDismiss={() => setShowAlert(false)}
>
  Votre carte bancaire a expiré. Mettez à jour vos informations de paiement.
</Alert>
```

---

## 💬 Dialog de Confirmation

```tsx
export function DeleteConfirmDialog({
  open,
  onOpenChange,
  onConfirm,
  title = 'Êtes-vous sûr ?',
  description
}: DialogProps) {
  const [isDeleting, setIsDeleting] = useState(false);
  
  const handleConfirm = async () => {
    setIsDeleting(true);
    try {
      await onConfirm();
      onOpenChange(false);
      toast.success('Supprimé avec succès');
    } catch (error) {
      toast.error('Échec de la suppression');
    } finally {
      setIsDeleting(false);
    }
  };
  
  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent>
        <DialogHeader>
          <div className="flex items-center gap-3">
            <div className="p-3 rounded-full bg-error/10">
              <AlertTriangle className="w-6 h-6 text-error" />
            </div>
            <DialogTitle>{title}</DialogTitle>
          </div>
          <DialogDescription>{description}</DialogDescription>
        </DialogHeader>
        
        <DialogFooter>
          <Button
            variant="ghost"
            onClick={() => onOpenChange(false)}
            disabled={isDeleting}
          >
            Annuler
          </Button>
          
          <Button
            variant="danger"
            onClick={handleConfirm}
            loading={isDeleting}
          >
            Supprimer
          </Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
}

// Usage
<DeleteConfirmDialog
  open={showDelete}
  onOpenChange={setShowDelete}
  onConfirm={() => deleteItem(item.id)}
  title="Supprimer cet élément ?"
  description="Cette action est irréversible. L'élément sera définitivement supprimé."
/>
```

---

## 📭 Empty States

```tsx
interface EmptyStateProps {
  icon?: React.ReactNode;
  title: string;
  description?: string;
  action?: React.ReactNode;
  illustration?: string;
}

export function EmptyState({
  icon,
  title,
  description,
  action,
  illustration
}: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12 px-4 text-center">
      {illustration ? (
        <img
          src={illustration}
          alt=""
          className="w-64 h-64 mb-6 opacity-50"
        />
      ) : icon ? (
        <div className="p-4 rounded-full bg-muted mb-6">
          {icon}
        </div>
      ) : null}
      
      <h3 className="text-lg font-semibold mb-2">{title}</h3>
      
      {description && (
        <p className="text-muted-foreground max-w-md mb-6">
          {description}
        </p>
      )}
      
      {action}
    </div>
  );
}

// Usage
<EmptyState
  icon={<Inbox className="w-12 h-12" />}
  title="Aucun message"
  description="Vous n'avez pas encore reçu de messages. Invitez des collègues pour commencer à échanger."
  action={
    <Button>
      <Plus /> Inviter un collègue
    </Button>
  }
/>
```

---

## 🚫 Error Pages

### 404 Not Found

```tsx
export function NotFoundPage() {
  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="text-center">
        <h1 className="text-9xl font-bold text-muted-foreground">404</h1>
        <h2 className="text-2xl font-semibold mt-4 mb-2">
          Page introuvable
        </h2>
        <p className="text-muted-foreground mb-8">
          La page que vous recherchez n'existe pas ou a été déplacée.
        </p>
        
        <div className="flex gap-4 justify-center">
          <Button variant="secondary" onClick={() => router.back()}>
            <ArrowLeft /> Retour
          </Button>
          
          <Button onClick={() => router.push('/')}>
            <Home /> Accueil
          </Button>
        </div>
      </div>
    </div>
  );
}
```

### 500 Server Error

```tsx
export function ServerErrorPage() {
  return (
    <div className="min-h-screen flex items-center justify-center">
      <Card className="max-w-md">
        <CardHeader>
          <div className="p-3 rounded-full bg-error/10 w-fit mb-4">
            <AlertTriangle className="w-8 h-8 text-error" />
          </div>
          <CardTitle>Erreur serveur</CardTitle>
          <CardDescription>
            Quelque chose s'est mal passé de notre côté. Nos équipes ont été notifiées.
          </CardDescription>
        </CardHeader>
        
        <CardContent className="space-y-4">
          <Alert variant="info">
            Erreur ID: {errorId}
            <br />
            Timestamp: {timestamp}
          </Alert>
        </CardContent>
        
        <CardFooter className="flex gap-2">
          <Button variant="secondary" onClick={() => location.reload()}>
            <RefreshCw /> Recharger
          </Button>
          
          <Button onClick={() => router.push('/')}>
            Retour à l'accueil
          </Button>
        </CardFooter>
      </Card>
    </div>
  );
}
```

---

## 🎯 Validation en temps réel

```tsx
export function EmailInput() {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');
  const [isValidating, setIsValidating] = useState(false);
  
  const debouncedValidate = useDebouncedCallback(async (value: string) => {
    setIsValidating(true);
    
    if (!value) {
      setError('');
      setIsValidating(false);
      return;
    }
    
    if (!isValidEmail(value)) {
      setError('Format d'email invalide');
      setIsValidating(false);
      return;
    }
    
    // Check if email exists
    const exists = await checkEmailExists(value);
    setError(exists ? 'Cet email est déjà utilisé' : '');
    setIsValidating(false);
  }, 500);
  
  return (
    <Input
      label="Email"
      value={email}
      onChange={(e) => {
        setEmail(e.target.value);
        debouncedValidate(e.target.value);
      }}
      error={error}
      rightIcon={
        isValidating ? <Spinner /> :
        !error && email ? <Check className="text-success" /> :
        error ? <X className="text-error" /> :
        null
      }
    />
  );
}
```

---

## 📊 Error Tracking

```tsx
// Sentry, Bugsnag, etc.
import * as Sentry from '@sentry/nextjs';

function handleError(error: Error, context?: any) {
  // Log to console in dev
  if (process.env.NODE_ENV === 'development') {
    console.error(error, context);
  }
  
  // Send to error tracking service
  Sentry.captureException(error, {
    contexts: { custom: context },
    level: 'error'
  });
  
  // Show user-friendly message
  toast.error('Une erreur est survenue', {
    description: 'Nos équipes ont été notifiées',
    action: {
      label: 'Réessayer',
      onClick: () => retry()
    }
  });
}

// Error Boundary
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    handleError(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    
    return this.props.children;
  }
}
```

---

## 📚 Points clés

1. **Messages clairs** : Problème + Cause + Action
2. **Ton empathique** : "Oups" pas "Error"
3. **Toast** : Max 4-5 secondes, dismissible
4. **Alert** : Persistant, action possible
5. **Dialog** : Actions critiques/irréversibles
6. **Empty state** : Proposition d'action
7. **Error pages** : Branded, navigation claire
8. **Validation** : Temps réel avec debounce
9. **Tracking** : Sentry/Bugsnag pour monitoring
10. **Fallback** : Error boundary React

---

*Chapitre 04.5 | Erreurs et Feedbacks*

