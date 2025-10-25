# Pages de Paramètres (Settings)

## 🎯 Objectifs

- ✓ Créer des pages de paramètres organisées et intuitives
- ✓ Implémenter la navigation et les sections
- ✓ Gérer les préférences utilisateur
- ✓ Optimiser l'UX de configuration

---

## 📐 Layout des Paramètres

```
┌─────────────────────────────────────────────┐
│ Paramètres                                  │
├───────────────┬─────────────────────────────┤
│ ☐ Compte      │  Informations du compte     │
│ • Profil      │  ┌─────────────────────┐    │
│ • Sécurité    │  │ Avatar  Name        │    │
│ • Notif.      │  │         Email       │    │
│ ☐ Préférences │  └─────────────────────┘    │
│ • Apparence   │                             │
│ • Langue      │  [Input] Nom               │
│ ☐ Facturation │  [Input] Email             │
│ • Plan        │  [Button] Enregistrer      │
│ • Paiement    │                             │
└───────────────┴─────────────────────────────┘
   Sidebar           Content
```

---

## 🎨 Implementation

```tsx
const settingsSections = [
  {
    id: 'account',
    title: 'Compte',
    icon: User,
    items: [
      { id: 'profile', label: 'Profil', href: '/settings/profile' },
      { id: 'security', label: 'Sécurité', href: '/settings/security' },
      { id: 'notifications', label: 'Notifications', href: '/settings/notifications' }
    ]
  },
  {
    id: 'preferences',
    title: 'Préférences',
    icon: Settings,
    items: [
      { id: 'appearance', label: 'Apparence', href: '/settings/appearance' },
      { id: 'language', label: 'Langue', href: '/settings/language' }
    ]
  },
  {
    id: 'billing',
    title: 'Facturation',
    icon: CreditCard,
    items: [
      { id: 'plan', label: 'Abonnement', href: '/settings/plan' },
      { id: 'payment', label: 'Moyens de paiement', href: '/settings/payment' },
      { id: 'invoices', label: 'Factures', href: '/settings/invoices' }
    ]
  }
];

export function SettingsLayout({ children }: { children: React.ReactNode }) {
  const pathname = usePathname();
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);
  
  return (
    <div className="container mx-auto py-8">
      <h1 className="text-3xl font-bold mb-8">Paramètres</h1>
      
      <div className="grid grid-cols-12 gap-8">
        {/* Sidebar - Desktop */}
        <aside className="hidden md:block col-span-3">
          <nav className="space-y-6">
            {settingsSections.map(section => (
              <div key={section.id}>
                <div className="flex items-center gap-2 text-sm font-semibold text-muted-foreground mb-2">
                  <section.icon className="w-4 h-4" />
                  {section.title}
                </div>
                
                <ul className="space-y-1">
                  {section.items.map(item => (
                    <li key={item.id}>
                      <Link
                        href={item.href}
                        className={cn(
                          'block px-3 py-2 rounded-md text-sm transition-colors',
                          pathname === item.href
                            ? 'bg-primary text-primary-foreground'
                            : 'hover:bg-muted'
                        )}
                      >
                        {item.label}
                      </Link>
                    </li>
                  ))}
                </ul>
              </div>
            ))}
          </nav>
        </aside>
        
        {/* Mobile Menu */}
        <div className="md:hidden col-span-12">
          <Select
            value={pathname}
            onValueChange={(value) => router.push(value)}
          >
            <SelectTrigger>
              <SelectValue placeholder="Sélectionner une section" />
            </SelectTrigger>
            <SelectContent>
              {settingsSections.map(section => (
                <SelectGroup key={section.id}>
                  <SelectLabel>{section.title}</SelectLabel>
                  {section.items.map(item => (
                    <SelectItem key={item.id} value={item.href}>
                      {item.label}
                    </SelectItem>
                  ))}
                </SelectGroup>
              ))}
            </SelectContent>
          </Select>
        </div>
        
        {/* Content */}
        <main className="col-span-12 md:col-span-9">
          {children}
        </main>
      </div>
    </div>
  );
}
```

---

## 👤 Page Profil

```tsx
export function ProfileSettings() {
  const { user } = useAuth();
  const { register, handleSubmit, formState } = useForm({
    defaultValues: {
      name: user.name,
      email: user.email,
      bio: user.bio,
      website: user.website
    }
  });
  
  return (
    <Card>
      <CardHeader>
        <CardTitle>Profil</CardTitle>
        <CardDescription>
          Gérez vos informations personnelles
        </CardDescription>
      </CardHeader>
      
      <form onSubmit={handleSubmit(onSubmit)}>
        <CardContent className="space-y-6">
          {/* Avatar */}
          <div>
            <label className="text-sm font-medium">Photo de profil</label>
            <div className="flex items-center gap-4 mt-2">
              <Avatar size="xl">
                <AvatarImage src={user.avatar} />
                <AvatarFallback>{user.initials}</AvatarFallback>
              </Avatar>
              
              <div className="space-x-2">
                <Button
                  type="button"
                  variant="secondary"
                  onClick={() => uploadRef.current?.click()}
                >
                  Changer
                </Button>
                <Button
                  type="button"
                  variant="ghost"
                  onClick={removeAvatar}
                >
                  Supprimer
                </Button>
              </div>
              
              <input
                ref={uploadRef}
                type="file"
                accept="image/*"
                className="hidden"
                onChange={handleAvatarUpload}
              />
            </div>
          </div>
          
          {/* Name */}
          <Input
            label="Nom complet"
            {...register('name')}
            error={formState.errors.name?.message}
          />
          
          {/* Email */}
          <Input
            label="Email"
            type="email"
            {...register('email')}
            error={formState.errors.email?.message}
            helperText="Utilisé pour les notifications et la connexion"
          />
          
          {/* Bio */}
          <Textarea
            label="Bio"
            rows={4}
            {...register('bio')}
            helperText="Brève description de vous"
          />
          
          {/* Website */}
          <Input
            label="Site web"
            type="url"
            placeholder="https://example.com"
            {...register('website')}
          />
        </CardContent>
        
        <CardFooter className="flex justify-between">
          <Button
            type="button"
            variant="ghost"
            onClick={() => reset()}
          >
            Annuler
          </Button>
          
          <Button
            type="submit"
            loading={formState.isSubmitting}
          >
            Enregistrer
          </Button>
        </CardFooter>
      </form>
    </Card>
  );
}
```

---

## 🔒 Page Sécurité

```tsx
export function SecuritySettings() {
  return (
    <div className="space-y-6">
      {/* Change Password */}
      <Card>
        <CardHeader>
          <CardTitle>Mot de passe</CardTitle>
        </CardHeader>
        <CardContent className="space-y-4">
          <Input
            label="Mot de passe actuel"
            type="password"
          />
          <Input
            label="Nouveau mot de passe"
            type="password"
          />
          <Input
            label="Confirmer le mot de passe"
            type="password"
          />
        </CardContent>
        <CardFooter>
          <Button>Changer le mot de passe</Button>
        </CardFooter>
      </Card>
      
      {/* 2FA */}
      <Card>
        <CardHeader>
          <CardTitle>Authentification à deux facteurs</CardTitle>
          <CardDescription>
            Ajoutez une couche de sécurité supplémentaire
          </CardDescription>
        </CardHeader>
        <CardContent>
          <div className="flex items-center justify-between">
            <div>
              <p className="font-medium">2FA par SMS</p>
              <p className="text-sm text-muted-foreground">
                {user.twoFactorEnabled
                  ? 'Activé sur +33 6 ** ** ** 42'
                  : 'Non activé'
                }
              </p>
            </div>
            
            <Switch
              checked={user.twoFactorEnabled}
              onCheckedChange={toggle2FA}
            />
          </div>
        </CardContent>
      </Card>
      
      {/* Sessions */}
      <Card>
        <CardHeader>
          <CardTitle>Sessions actives</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="space-y-4">
            {sessions.map(session => (
              <div key={session.id} className="flex items-center justify-between">
                <div className="flex items-center gap-3">
                  {session.device === 'desktop' ? <Monitor /> : <Smartphone />}
                  <div>
                    <p className="font-medium">{session.location}</p>
                    <p className="text-sm text-muted-foreground">
                      {session.browser} • {formatRelativeTime(session.lastActive)}
                    </p>
                  </div>
                  {session.isCurrent && (
                    <Badge variant="success">Actuelle</Badge>
                  )}
                </div>
                
                {!session.isCurrent && (
                  <Button
                    variant="ghost"
                    size="sm"
                    onClick={() => revokeSession(session.id)}
                  >
                    Déconnecter
                  </Button>
                )}
              </div>
            ))}
          </div>
        </CardContent>
        <CardFooter>
          <Button variant="secondary" onClick={revokeAllSessions}>
            Déconnecter toutes les autres sessions
          </Button>
        </CardFooter>
      </Card>
    </div>
  );
}
```

---

## 🎨 Page Apparence

```tsx
export function AppearanceSettings() {
  const { theme, setTheme } = useTheme();
  const [accentColor, setAccentColor] = useState('blue');
  
  return (
    <Card>
      <CardHeader>
        <CardTitle>Apparence</CardTitle>
        <CardDescription>
          Personnalisez l'apparence de l'application
        </CardDescription>
      </CardHeader>
      
      <CardContent className="space-y-6">
        {/* Theme */}
        <div>
          <label className="text-sm font-medium mb-3 block">Thème</label>
          <div className="grid grid-cols-3 gap-4">
            <ThemeOption
              value="light"
              label="Clair"
              selected={theme === 'light'}
              onClick={() => setTheme('light')}
            >
              <div className="w-full h-20 bg-white border rounded" />
            </ThemeOption>
            
            <ThemeOption
              value="dark"
              label="Sombre"
              selected={theme === 'dark'}
              onClick={() => setTheme('dark')}
            >
              <div className="w-full h-20 bg-gray-900 border border-gray-700 rounded" />
            </ThemeOption>
            
            <ThemeOption
              value="system"
              label="Système"
              selected={theme === 'system'}
              onClick={() => setTheme('system')}
            >
              <div className="w-full h-20 bg-gradient-to-r from-white via-gray-400 to-gray-900 border rounded" />
            </ThemeOption>
          </div>
        </div>
        
        {/* Accent Color */}
        <div>
          <label className="text-sm font-medium mb-3 block">Couleur d'accentuation</label>
          <div className="grid grid-cols-6 gap-3">
            {['blue', 'green', 'purple', 'orange', 'pink', 'red'].map(color => (
              <button
                key={color}
                onClick={() => setAccentColor(color)}
                className={cn(
                  'w-10 h-10 rounded-full border-2',
                  `bg-${color}-500`,
                  accentColor === color ? 'border-foreground scale-110' : 'border-transparent'
                )}
              />
            ))}
          </div>
        </div>
        
        {/* Font Size */}
        <div>
          <label className="text-sm font-medium mb-3 block">
            Taille de police
          </label>
          <Slider
            value={[fontSize]}
            onValueChange={([value]) => setFontSize(value)}
            min={12}
            max={18}
            step={1}
          />
          <div className="flex justify-between text-xs text-muted-foreground mt-1">
            <span>Petit</span>
            <span>{fontSize}px</span>
            <span>Grand</span>
          </div>
        </div>
      </CardContent>
      
      <CardFooter>
        <Button onClick={savePreferences}>
          Enregistrer les préférences
        </Button>
      </CardFooter>
    </Card>
  );
}
```

---

## 💳 Page Facturation

```tsx
export function BillingSettings() {
  return (
    <div className="space-y-6">
      {/* Current Plan */}
      <Card>
        <CardHeader>
          <div className="flex items-center justify-between">
            <div>
              <CardTitle>Plan actuel</CardTitle>
              <CardDescription>
                Gérez votre abonnement
              </CardDescription>
            </div>
            <Badge variant="primary">Pro</Badge>
          </div>
        </CardHeader>
        
        <CardContent>
          <div className="flex items-baseline gap-2">
            <span className="text-3xl font-bold">29 €</span>
            <span className="text-muted-foreground">/mois</span>
          </div>
          
          <ul className="mt-4 space-y-2">
            <li className="flex items-center gap-2">
              <Check className="w-4 h-4 text-success" />
              <span>Utilisateurs illimités</span>
            </li>
            <li className="flex items-center gap-2">
              <Check className="w-4 h-4 text-success" />
              <span>Stockage 100 GB</span>
            </li>
            <li className="flex items-center gap-2">
              <Check className="w-4 h-4 text-success" />
              <span>Support prioritaire</span>
            </li>
          </ul>
        </CardContent>
        
        <CardFooter className="flex gap-2">
          <Button variant="secondary">Changer de plan</Button>
          <Button variant="ghost">Annuler l'abonnement</Button>
        </CardFooter>
      </Card>
      
      {/* Payment Methods */}
      <Card>
        <CardHeader>
          <CardTitle>Moyens de paiement</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="space-y-3">
            {paymentMethods.map(method => (
              <div key={method.id} className="flex items-center justify-between p-4 border rounded">
                <div className="flex items-center gap-3">
                  <CreditCard />
                  <div>
                    <p className="font-medium">
                      {method.brand} •••• {method.last4}
                    </p>
                    <p className="text-sm text-muted-foreground">
                      Expire {method.expMonth}/{method.expYear}
                    </p>
                  </div>
                  {method.isDefault && (
                    <Badge variant="secondary">Par défaut</Badge>
                  )}
                </div>
                
                <DropdownMenu>
                  <DropdownMenuTrigger asChild>
                    <Button variant="ghost" size="sm">
                      <MoreVertical />
                    </Button>
                  </DropdownMenuTrigger>
                  <DropdownMenuContent>
                    <DropdownMenuItem onClick={() => setDefault(method.id)}>
                      Définir par défaut
                    </DropdownMenuItem>
                    <DropdownMenuItem onClick={() => remove(method.id)}>
                      Supprimer
                    </DropdownMenuItem>
                  </DropdownMenuContent>
                </DropdownMenu>
              </div>
            ))}
          </div>
        </CardContent>
        <CardFooter>
          <Button>Ajouter un moyen de paiement</Button>
        </CardFooter>
      </Card>
    </div>
  );
}
```

---

## 📚 Points clés

1. **Navigation** : Sidebar desktop, Select mobile
2. **Sections** : Regroupement logique (Account, Preferences, Billing)
3. **Feedback** : Toast après sauvegarde
4. **Validation** : Temps réel sur les champs
5. **État** : Unsaved changes warning
6. **Mobile** : Stack vertical, sections collapsibles
7. **Loading** : Skeleton durant chargement initial
8. **Dangerous actions** : Confirmation dialog (delete account)

---

*Chapitre 04.3 | Pages de Paramètres*

