# Flux d'Onboarding

## 🎯 Objectifs

- ✓ Créer un onboarding engageant et efficace
- ✓ Guider l'utilisateur vers la valeur rapidement
- ✓ Collecter les informations essentielles
- ✓ Maximiser l'activation et la rétention

---

## 📐 Types d'Onboarding

### 1. Multi-step (étapes)
### 2. Checklist (tâches à compléter)
### 3. Guided tour (tooltips interactifs)
### 4. Video tutorial
### 5. Progressive disclosure (au fur et à mesure)

---

## 🎨 Multi-step Onboarding

```tsx
const onboardingSteps = [
  {
    id: 'welcome',
    title: 'Bienvenue !',
    description: 'Créons votre compte en quelques étapes'
  },
  {
    id: 'profile',
    title: 'Votre profil',
    description: 'Parlez-nous un peu de vous'
  },
  {
    id: 'preferences',
    title: 'Préférences',
    description: 'Personnalisez votre expérience'
  },
  {
    id: 'done',
    title: 'Tout est prêt !',
    description: 'Vous pouvez maintenant commencer'
  }
];

export function Onboarding() {
  const [currentStep, setCurrentStep] = useState(0);
  const [data, setData] = useState({});
  
  const isLastStep = currentStep === onboardingSteps.length - 1;
  const progress = ((currentStep + 1) / onboardingSteps.length) * 100;
  
  const handleNext = async () => {
    if (isLastStep) {
      await completeOnboarding(data);
      router.push('/dashboard');
    } else {
      setCurrentStep(prev => prev + 1);
    }
  };
  
  const handleBack = () => {
    setCurrentStep(prev => Math.max(0, prev - 1));
  };
  
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <Card className="w-full max-w-2xl">
        {/* Progress */}
        <div className="h-2 bg-gray-200">
          <div
            className="h-full bg-primary transition-all duration-300"
            style={{ width: `${progress}%` }}
          />
        </div>
        
        {/* Steps indicator */}
        <div className="flex justify-center gap-2 p-6 border-b">
          {onboardingSteps.map((step, index) => (
            <div
              key={step.id}
              className={cn(
                'w-2 h-2 rounded-full transition-colors',
                index === currentStep
                  ? 'bg-primary w-8'
                  : index < currentStep
                  ? 'bg-primary'
                  : 'bg-gray-300'
              )}
            />
          ))}
        </div>
        
        <CardHeader>
          <CardTitle>{onboardingSteps[currentStep].title}</CardTitle>
          <CardDescription>
            {onboardingSteps[currentStep].description}
          </CardDescription>
        </CardHeader>
        
        <CardContent>
          {currentStep === 0 && <WelcomeStep />}
          {currentStep === 1 && <ProfileStep data={data} setData={setData} />}
          {currentStep === 2 && <PreferencesStep data={data} setData={setData} />}
          {currentStep === 3 && <DoneStep />}
        </CardContent>
        
        <CardFooter className="flex justify-between">
          <Button
            variant="ghost"
            onClick={handleBack}
            disabled={currentStep === 0}
          >
            Retour
          </Button>
          
          <div className="flex gap-2">
            <Button
              variant="ghost"
              onClick={() => router.push('/dashboard')}
            >
              Passer
            </Button>
            
            <Button onClick={handleNext}>
              {isLastStep ? 'Terminer' : 'Suivant'}
            </Button>
          </div>
        </CardFooter>
      </Card>
    </div>
  );
}
```

---

## ✅ Checklist Onboarding

```tsx
const onboardingTasks = [
  {
    id: 'profile',
    title: 'Complétez votre profil',
    description: 'Ajoutez une photo et une bio',
    completed: false,
    action: () => router.push('/settings/profile')
  },
  {
    id: 'invite',
    title: 'Invitez votre équipe',
    description: '3 membres minimum recommandés',
    completed: false,
    action: () => openInviteDialog()
  },
  {
    id: 'first-project',
    title: 'Créez votre premier projet',
    description: 'Commencez à collaborer',
    completed: false,
    action: () => router.push('/projects/new')
  },
  {
    id: 'integration',
    title: 'Connectez vos outils',
    description: 'GitHub, Slack, etc.',
    completed: false,
    action: () => router.push('/integrations')
  }
];

export function OnboardingChecklist() {
  const [tasks, setTasks] = useState(onboardingTasks);
  const completedCount = tasks.filter(t => t.completed).length;
  const progress = (completedCount / tasks.length) * 100;
  
  const [isDismissed, setIsDismissed] = useState(false);
  
  if (isDismissed || progress === 100) return null;
  
  return (
    <Card className="mb-6">
      <CardHeader>
        <div className="flex items-center justify-between">
          <div>
            <CardTitle>Prise en main</CardTitle>
            <CardDescription>
              {completedCount} sur {tasks.length} tâches complétées
            </CardDescription>
          </div>
          
          <Button
            variant="ghost"
            size="sm"
            onClick={() => setIsDismissed(true)}
          >
            <X />
          </Button>
        </div>
        
        <Progress value={progress} className="mt-4" />
      </CardHeader>
      
      <CardContent>
        <div className="space-y-3">
          {tasks.map(task => (
            <div
              key={task.id}
              className={cn(
                'flex items-start gap-3 p-3 rounded-lg border transition-colors',
                task.completed ? 'bg-success/5 border-success/20' : 'hover:bg-muted'
              )}
            >
              <Checkbox
                checked={task.completed}
                onCheckedChange={(checked) => {
                  setTasks(prev =>
                    prev.map(t =>
                      t.id === task.id ? { ...t, completed: !!checked } : t
                    )
                  );
                }}
              />
              
              <div className="flex-1">
                <p className={cn(
                  'font-medium',
                  task.completed && 'line-through text-muted-foreground'
                )}>
                  {task.title}
                </p>
                <p className="text-sm text-muted-foreground">
                  {task.description}
                </p>
              </div>
              
              {!task.completed && (
                <Button
                  size="sm"
                  variant="ghost"
                  onClick={task.action}
                >
                  Démarrer →
                </Button>
              )}
            </div>
          ))}
        </div>
      </CardContent>
    </Card>
  );
}
```

---

## 🎯 Guided Tour (Tooltips)

```tsx
import Joyride, { Step } from 'react-joyride';

const tourSteps: Step[] = [
  {
    target: '#dashboard',
    content: 'Voici votre tableau de bord. Vous retrouverez ici vos statistiques principales.',
    placement: 'center'
  },
  {
    target: '#sidebar-projects',
    content: 'Accédez à tous vos projets depuis ce menu.',
    placement: 'right'
  },
  {
    target: '#create-button',
    content: 'Créez un nouveau projet en cliquant ici.',
    placement: 'bottom'
  },
  {
    target: '#settings',
    content: 'Personnalisez votre expérience dans les paramètres.',
    placement: 'left'
  }
];

export function GuidedTour() {
  const [run, setRun] = useState(true);
  const [stepIndex, setStepIndex] = useState(0);
  
  const handleJoyrideCallback = (data: CallBackProps) => {
    const { status, type } = data;
    
    if (status === 'finished' || status === 'skipped') {
      setRun(false);
      markTourCompleted();
    }
    
    if (type === 'step:after') {
      setStepIndex(data.index + 1);
    }
  };
  
  return (
    <Joyride
      steps={tourSteps}
      run={run}
      stepIndex={stepIndex}
      continuous
      showProgress
      showSkipButton
      callback={handleJoyrideCallback}
      styles={{
        options: {
          primaryColor: 'hsl(var(--primary))',
          zIndex: 10000
        }
      }}
      locale={{
        back: 'Retour',
        close: 'Fermer',
        last: 'Terminer',
        next: 'Suivant',
        skip: 'Passer'
      }}
    />
  );
}
```

---

## 🎬 Video Tutorial

```tsx
export function VideoOnboarding() {
  const [hasWatched, setHasWatched] = useState(false);
  
  return (
    <Card>
      <CardHeader>
        <CardTitle>Découvrez {appName} en 2 minutes</CardTitle>
      </CardHeader>
      
      <CardContent>
        <div className="relative aspect-video rounded-lg overflow-hidden bg-black">
          <video
            controls
            onEnded={() => setHasWatched(true)}
            poster="/onboarding-thumbnail.jpg"
            className="w-full h-full"
          >
            <source src="/onboarding-video.mp4" type="video/mp4" />
            <track
              kind="subtitles"
              src="/onboarding-fr.vtt"
              srcLang="fr"
              label="Français"
              default
            />
          </video>
        </div>
      </CardContent>
      
      <CardFooter className="flex justify-between">
        <Button variant="ghost" onClick={skip}>
          Passer
        </Button>
        
        <Button onClick={next} disabled={!hasWatched}>
          {hasWatched ? 'Continuer' : 'Regardez la vidéo pour continuer'}
        </Button>
      </CardFooter>
    </Card>
  );
}
```

---

## 📊 Metrics d'Onboarding

```
Funnel d'activation:
──────────────────────────────────────
Signup            100% (1000 users)
    ↓
Start Onboarding   80%  (800 users)  ← 20% drop-off
    ↓
Complete Step 1    70%  (700 users)  ← 10% drop-off
    ↓
Complete Step 2    60%  (600 users)  ← 10% drop-off
    ↓
Complete Step 3    50%  (500 users)  ← 10% drop-off
    ↓
First Action       45%  (450 users)  ← 5% drop-off
    ↓
Activated User     40%  (400 users)
──────────────────────────────────────

KPIs:
- Time to Value (TTV): 5 min
- Completion Rate: 40%
- D1 Retention: 65%
- D7 Retention: 45%

Optimizations:
- Réduire à 3 étapes max: +15% completion
- Progress bar: +8% completion
- Skip option: +12% completion (moins de frustration)
- Video: +20% engagement
```

---

## 💡 Best Practices

### 1. Time to Value rapide

```
Mauvais: 10 questions avant d'accéder à l'app
Bon: 2-3 questions essentielles, le reste peut attendre
```

### 2. Progressive disclosure

```tsx
// Ne demandez que le nécessaire immédiatement
Step 1: Nom + Email
Step 2: (dans l'app) Préférences basiques
Step 3: (après 1ère utilisation) Préférences avancées
```

### 3. Motivation intrinsèque

```tsx
// Montrez la valeur, pas les features
❌ "Connectez votre CRM"
✓ "Synchronisez vos contacts en 1 clic"

❌ "Configurez les webhooks"
✓ "Automatisez vos notifications"
```

### 4. Skip option

```tsx
// Toujours permettre de passer
<Button variant="ghost" onClick={skip}>
  Je ferai ça plus tard
</Button>
```

---

## ♿ Accessibilité

```tsx
// Progress annoncé
<div
  role="status"
  aria-live="polite"
  aria-atomic="true"
  className="sr-only"
>
  Étape {currentStep + 1} sur {totalSteps}
</div>

// Navigation au clavier
<div
  role="region"
  aria-label="Onboarding"
  onKeyDown={(e) => {
    if (e.key === 'ArrowRight') next();
    if (e.key === 'ArrowLeft') prev();
  }}
>
  {/* Content */}
</div>
```

---

## 📚 Points clés

1. **TTV** : Time to Value < 5 minutes
2. **Étapes** : Max 3-5 étapes
3. **Progress** : Toujours visible
4. **Skip** : Option présente à chaque étape
5. **Mobile** : Full-screen, simple
6. **Persistence** : Sauvegarder progression
7. **Réactivation** : Accessible depuis settings
8. **Analytics** : Tracker drop-off à chaque étape

---

*Chapitre 04.4 | Flux d'Onboarding*

