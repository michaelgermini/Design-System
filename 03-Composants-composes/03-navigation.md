# Navigation

## 🎯 Objectifs

- ✓ Créer des systèmes de navigation accessibles
- ✓ Implémenter navbar, sidebar, tabs, breadcrumbs
- ✓ Gérer la navigation responsive
- ✓ Optimiser l'UX mobile

---

## 🧭 Types de Navigation

### 1. Navbar (Header horizontal)

```tsx
<nav className="navbar">
  <div className="navbar-brand">
    <Logo />
  </div>
  
  <div className="navbar-menu">
    <NavLink href="/" active>Accueil</NavLink>
    <NavLink href="/products">Produits</NavLink>
    <NavLink href="/about">À propos</NavLink>
  </div>
  
  <div className="navbar-actions">
    <Button variant="ghost">Connexion</Button>
    <Button>Inscription</Button>
  </div>
</nav>
```

### 2. Sidebar (Panneau latéral)

```tsx
<aside className="sidebar">
  <nav>
    <SidebarItem icon={<Home />} href="/dashboard">
      Dashboard
    </SidebarItem>
    <SidebarItem icon={<Users />} href="/users">
      Utilisateurs
    </SidebarItem>
    <SidebarItem icon={<Settings />} href="/settings">
      Paramètres
    </SidebarItem>
  </nav>
</aside>
```

### 3. Tabs (Onglets)

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <TabsList>
    <TabsTrigger value="account">Compte</TabsTrigger>
    <TabsTrigger value="password">Mot de passe</TabsTrigger>
    <TabsTrigger value="notifications">Notifications</TabsTrigger>
  </TabsList>
  
  <TabsContent value="account">
    {/* Contenu Compte */}
  </TabsContent>
  <TabsContent value="password">
    {/* Contenu Mot de passe */}
  </TabsContent>
</Tabs>
```

### 4. Breadcrumbs (Fil d'Ariane)

```tsx
<nav aria-label="Fil d'Ariane">
  <ol className="breadcrumb">
    <li><a href="/">Accueil</a></li>
    <li><a href="/products">Produits</a></li>
    <li aria-current="page">T-Shirt</li>
  </ol>
</nav>
```

---

## 📱 Navigation Mobile

### Menu Hamburger

```tsx
const [isOpen, setIsOpen] = useState(false);

<div className="mobile-nav">
  <button
    onClick={() => setIsOpen(!isOpen)}
    aria-label="Menu"
    aria-expanded={isOpen}
  >
    {isOpen ? <X /> : <Menu />}
  </button>
  
  {isOpen && (
    <div className="mobile-menu">
      <NavLink href="/">Accueil</NavLink>
      <NavLink href="/products">Produits</NavLink>
    </div>
  )}
</div>
```

---

## ♿ Accessibilité

```tsx
<nav aria-label="Navigation principale">
  <ul role="list">
    <li>
      <a
        href="/home"
        aria-current={isActive ? 'page' : undefined}
      >
        Accueil
      </a>
    </li>
  </ul>
</nav>

// Skiplink
<a href="#main-content" className="skip-link">
  Aller au contenu principal
</a>
```

---

## 📚 Points clés

1. **Landmark** : `<nav>` avec aria-label
2. **Current page** : aria-current="page"
3. **Skip link** : Pour accès rapide au contenu
4. **Focus visible** : Sur liens actifs
5. **Mobile** : Menu hamburger accessible
6. **Keyboard** : Tab, Enter, Arrow keys pour menus
7. **Active state** : Visuellement distinct

---

*Chapitre 03.3 | Navigation*

