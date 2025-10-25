# Accessibilité et Contrastes

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Calculer et valider les ratios de contraste WCAG
- ✓ Créer des palettes accessibles
- ✓ Implémenter les standards ARIA
- ✓ Tester l'accessibilité de vos composants
- ✓ Supporter les technologies d'assistance

---

## ♿ L'accessibilité : un impératif, pas une option

### Pourquoi l'accessibilité ?

```
15% de la population mondiale vit avec un handicap

1 milliard de personnes concernées

Types de handicaps :
- Visuel : 285M (cécité, daltonisme, basse vision)
- Auditif : 466M (surdité, malentendance)
- Moteur : 200M (mobilité réduite)
- Cognitif : Variable (dyslexie, TDAH, autisme)
```

### Impact business

| Métrique | Sans accessibilité | Avec accessibilité | Gain |
|----------|-------------------|-------------------|------|
| Audience accessible | 85% | 100% | **+17%** |
| Satisfaction utilisateur | 65% | 85% | **+31%** |
| Taux de conversion | 2% | 2.6% | **+30%** |
| Risque légal | Élevé | Faible | **-90%** |

**ROI de l'accessibilité** : Chaque 1€ investi rapporte 3-5€

---

## 📐 WCAG : Web Content Accessibility Guidelines

### Les 4 principes POUR

```
P - Perceptible
    L'information doit être présentée de façon perceptible

O - Opérable  
    L'interface doit être utilisable

U - Compréhensible
    L'information et l'interface doivent être compréhensibles

R - Robuste
    Le contenu doit être interprétable par diverses technologies
```

### Niveaux de conformité

| Niveau | Critères | Conformité | Objectif |
|--------|----------|------------|----------|
| **A** | Minimum | Base | Utilisable |
| **AA** | Recommandé | **Standard** | Confortable |
| **AAA** | Optimal | Excellence | Idéal |

💡 **Cible recommandée** : **WCAG 2.1 niveau AA**

---

## 🎨 Contraste de couleurs

### Formule de luminance relative

```
Luminance relative (L) pour une couleur RGB :

1. Normaliser RGB : R, G, B = RGB / 255

2. Appliquer la courbe gamma :
   Pour chaque canal C (R, G ou B) :
   
   Si C ≤ 0.03928 :
     C_lin = C / 12.92
   Sinon :
     C_lin = ((C + 0.055) / 1.055)^2.4

3. Calculer la luminance :
   L = 0.2126 × R_lin + 0.7152 × G_lin + 0.0722 × B_lin
```

### Formule du ratio de contraste

```
Ratio = (L_clair + 0.05) / (L_foncé + 0.05)

où :
L_clair = Luminance du plus clair
L_foncé = Luminance du plus foncé

Résultat : Ratio entre 1:1 (aucun contraste) et 21:1 (noir sur blanc)
```

### Standards WCAG pour le contraste

#### Texte normal (< 18pt ou < 14pt gras)

| Niveau | Ratio minimum | Exemple |
|--------|---------------|---------|
| **AA** | **4.5:1** | #767676 sur #FFFFFF ✓ |
| **AAA** | **7:1** | #595959 sur #FFFFFF ✓ |

#### Texte large (≥ 18pt ou ≥ 14pt gras)

| Niveau | Ratio minimum | Exemple |
|--------|---------------|---------|
| **AA** | **3:1** | #949494 sur #FFFFFF ✓ |
| **AAA** | **4.5:1** | #767676 sur #FFFFFF ✓ |

#### Éléments graphiques et UI

| Élément | Ratio minimum | Niveau |
|---------|---------------|--------|
| Icônes, borders | **3:1** | AA |
| États focus | **3:1** | AA |
| Graphiques | **3:1** | AA |

### Exemple de calcul

```javascript
function getLuminance(r, g, b) {
  // Normaliser
  const [R, G, B] = [r, g, b].map(c => c / 255);
  
  // Appliquer gamma
  const gammaCorrect = (channel) => {
    return channel <= 0.03928
      ? channel / 12.92
      : Math.pow((channel + 0.055) / 1.055, 2.4);
  };
  
  const [R_lin, G_lin, B_lin] = [R, G, B].map(gammaCorrect);
  
  // Calculer luminance
  return 0.2126 * R_lin + 0.7152 * G_lin + 0.0722 * B_lin;
}

function getContrastRatio(rgb1, rgb2) {
  const L1 = getLuminance(...rgb1);
  const L2 = getLuminance(...rgb2);
  
  const lighter = Math.max(L1, L2);
  const darker = Math.min(L1, L2);
  
  return (lighter + 0.05) / (darker + 0.05);
}

// Exemple : Blanc sur Bleu
const white = [255, 255, 255];  // #FFFFFF
const blue = [59, 130, 246];    // #3B82F6

const ratio = getContrastRatio(white, blue);
console.log(ratio.toFixed(2)); // 3.45:1

// Verdict : ❌ Échec WCAG AA pour texte normal (besoin 4.5:1)
```

### Palette de gris accessible

```css
/* Sur fond blanc #FFFFFF */
:root {
  /* Luminance du blanc : 1.0 */
  
  --gray-50: #F9FAFB;   /* Ratio: 1.04:1 - Background */
  --gray-100: #F3F4F6;  /* Ratio: 1.08:1 - Background */
  --gray-200: #E5E7EB;  /* Ratio: 1.21:1 - Borders (< 3:1) */
  --gray-300: #D1D5DB;  /* Ratio: 1.43:1 - Borders */
  --gray-400: #9CA3AF;  /* Ratio: 2.42:1 - Disabled text */
  --gray-500: #6B7280;  /* Ratio: 4.54:1 - ✓ AA normal text */
  --gray-600: #4B5563;  /* Ratio: 7.03:1 - ✓ AAA normal text */
  --gray-700: #374151;  /* Ratio: 10.22:1 - ✓ AAA texte */
  --gray-800: #1F2937;  /* Ratio: 14.04:1 - ✓ AAA texte */
  --gray-900: #111827;  /* Ratio: 17.78:1 - ✓ AAA texte */
}

/* Usage sémantique */
:root {
  --text-primary: var(--gray-900);     /* 17.78:1 ✓ */
  --text-secondary: var(--gray-600);   /* 7.03:1 ✓ */
  --text-tertiary: var(--gray-500);    /* 4.54:1 ✓ */
  --text-disabled: var(--gray-400);    /* 2.42:1 ❌ (intentionnel) */
  --border-default: var(--gray-300);   /* 1.43:1 - OK pour UI (3:1) */
}
```

### Palette de couleurs accessibles

```css
/* Couleurs primaires sur blanc */
:root {
  /* Primary (Bleu) */
  --primary-50: #EEF2FF;   /* BG only */
  --primary-100: #E0E7FF;  /* BG only */
  --primary-200: #C7D2FE;  /* BG only */
  --primary-300: #A5B4FC;  /* Large text only (3.2:1) */
  --primary-400: #818CF8;  /* Large text only (4.1:1) */
  --primary-500: #6366F1;  /* ✓ AA normal (4.53:1) */
  --primary-600: #4F46E5;  /* ✓ AA normal (6.1:1) */
  --primary-700: #4338CA;  /* ✓ AAA normal (7.84:1) */
  --primary-800: #3730A3;  /* ✓ AAA normal (10.4:1) */
  --primary-900: #312E81;  /* ✓ AAA normal (12.9:1) */
  
  /* Success (Vert) */
  --success-50: #F0FDF4;
  --success-500: #10B981;  /* 3.02:1 - Large text only */
  --success-600: #059669;  /* ✓ AA normal (4.51:1) */
  --success-700: #047857;  /* ✓ AAA normal (7.04:1) */
  --success-900: #064E3B;  /* ✓ AAA normal (12.3:1) */
  
  /* Error (Rouge) */
  --error-50: #FEF2F2;
  --error-500: #EF4444;    /* 3.97:1 - Presque AA */
  --error-600: #DC2626;    /* ✓ AA normal (5.15:1) */
  --error-700: #B91C1C;    /* ✓ AAA normal (7.72:1) */
  --error-900: #7F1D1D;    /* ✓ AAA normal (11.5:1) */
  
  /* Warning (Orange) */
  --warning-50: #FFFBEB;
  --warning-500: #F59E0B;  /* 2.48:1 - ❌ Pas pour texte */
  --warning-600: #D97706;  /* 3.88:1 - Large text only */
  --warning-700: #B45309;  /* ✓ AA normal (5.53:1) */
  --warning-800: #92400E;  /* ✓ AAA normal (8.25:1) */
}
```

### Règles d'usage

```
RÈGLE 1 : Texte normal sur fond clair
─────────────────────────────────────
Utiliser : -600, -700, -800, -900
Éviter : -50 à -500

RÈGLE 2 : Texte normal sur fond foncé
──────────────────────────────────────
Utiliser : -50, -100, -200, -300
Éviter : -400 à -950

RÈGLE 3 : Backgrounds et accents
──────────────────────────────────
Tous niveaux OK, mais vérifier le contraste du texte dessus

RÈGLE 4 : Liens et boutons
────────────────────────────
Minimum : -500 (pour large text)
Recommandé : -600 ou plus foncé
```

---

## 🦾 ARIA : Accessible Rich Internet Applications

### Les 3 piliers d'ARIA

```
1. ROLES : Que fait cet élément ?
   role="button", role="navigation", role="alert"

2. STATES : Dans quel état est-il ?
   aria-disabled="true", aria-expanded="false"

3. PROPERTIES : Quelles sont ses caractéristiques ?
   aria-label="Menu", aria-describedby="help-text"
```

### Rôles ARIA essentiels

```html
<!-- Navigation -->
<nav role="navigation" aria-label="Menu principal">
  <ul role="list">
    <li role="listitem"><a href="/">Accueil</a></li>
  </ul>
</nav>

<!-- Bouton custom -->
<div 
  role="button" 
  tabindex="0"
  aria-pressed="false"
  onClick={handleClick}
  onKeyPress={handleKeyPress}
>
  Toggle
</div>

<!-- Zone d'alerte -->
<div role="alert" aria-live="assertive">
  Erreur : Champ obligatoire
</div>

<!-- Onglets -->
<div role="tablist" aria-label="Paramètres">
  <button role="tab" aria-selected="true" aria-controls="panel-1">
    Général
  </button>
  <button role="tab" aria-selected="false" aria-controls="panel-2">
    Avancé
  </button>
</div>

<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">
  Contenu général
</div>
```

### États ARIA

```html
<!-- Expansion -->
<button 
  aria-expanded="false" 
  aria-controls="dropdown-menu"
>
  Menu
</button>

<!-- Sélection -->
<div role="option" aria-selected="true">
  Option sélectionnée
</div>

<!-- Désactivation -->
<button aria-disabled="true">
  Bouton désactivé
</button>

<!-- Checkbox/Radio -->
<div role="checkbox" aria-checked="true">
  <input type="checkbox" checked />
  Option cochée
</div>

<!-- Progression -->
<div 
  role="progressbar" 
  aria-valuenow="60" 
  aria-valuemin="0" 
  aria-valuemax="100"
>
  60%
</div>
```

### Propriétés ARIA

```html
<!-- Labels -->
<button aria-label="Fermer la modale">
  <Icon name="close" />
</button>

<!-- Description -->
<input 
  type="password"
  aria-describedby="password-requirements"
/>
<div id="password-requirements">
  Minimum 8 caractères, 1 majuscule, 1 chiffre
</div>

<!-- Relations -->
<h2 id="section-title">Paramètres</h2>
<div aria-labelledby="section-title">
  Contenu de la section
</div>

<!-- Live regions -->
<div aria-live="polite" aria-atomic="true">
  3 messages non lus
</div>
```

### aria-live : Régions dynamiques

```html
<!-- Assertive : Interruption immédiate -->
<div role="alert" aria-live="assertive">
  ⚠️ Erreur critique : Connexion perdue
</div>

<!-- Polite : Annonce quand possible -->
<div aria-live="polite" aria-atomic="true">
  ✓ Modifications enregistrées
</div>

<!-- Off : Pas d'annonce -->
<div aria-live="off">
  Contenu statique
</div>
```

---

## ⌨️ Navigation au clavier

### Ordre de tab (tabindex)

```html
<!-- tabindex="0" : Dans le flow naturel -->
<button tabindex="0">Accessible au clavier</button>

<!-- tabindex="-1" : Focusable par JS, pas par Tab -->
<div tabindex="-1" id="skip-target">
  Contenu avec focus programmatique
</div>

<!-- tabindex="1+" : ❌ À ÉVITER (casse l'ordre naturel) -->
<button tabindex="1">Ne pas faire ça</button>
```

### Touches de navigation standards

| Touche | Action | Usage |
|--------|--------|-------|
| **Tab** | Focus suivant | Navigation globale |
| **Shift + Tab** | Focus précédent | Navigation arrière |
| **Enter** | Activer | Liens, boutons |
| **Space** | Activer/Toggle | Boutons, checkbox |
| **Escape** | Fermer/Annuler | Modales, dropdowns |
| **Arrow keys** | Navigation interne | Menus, tabs, listes |
| **Home** | Premier élément | Listes, inputs |
| **End** | Dernier élément | Listes, inputs |

### Implémentation React

```tsx
// Button avec support clavier complet
function Button({ onClick, children, ...props }) {
  const handleKeyPress = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      onClick?.(e);
    }
  };
  
  return (
    <button
      onClick={onClick}
      onKeyPress={handleKeyPress}
      {...props}
    >
      {children}
    </button>
  );
}

// Dropdown avec navigation au clavier
function Dropdown({ options }) {
  const [isOpen, setIsOpen] = useState(false);
  const [focusedIndex, setFocusedIndex] = useState(0);
  
  const handleKeyDown = (e: React.KeyboardEvent) => {
    switch (e.key) {
      case 'Escape':
        setIsOpen(false);
        break;
      case 'ArrowDown':
        e.preventDefault();
        setFocusedIndex((i) => (i + 1) % options.length);
        break;
      case 'ArrowUp':
        e.preventDefault();
        setFocusedIndex((i) => (i - 1 + options.length) % options.length);
        break;
      case 'Enter':
        // Sélectionner l'option focusée
        break;
    }
  };
  
  return (
    <div onKeyDown={handleKeyDown}>
      {/* ... */}
    </div>
  );
}

// Focus trap pour modales
function Modal({ children, onClose }) {
  const modalRef = useRef<HTMLDivElement>(null);
  
  useEffect(() => {
    if (!modalRef.current) return;
    
    const focusableElements = modalRef.current.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    
    const firstElement = focusableElements[0] as HTMLElement;
    const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;
    
    // Piéger le focus dans la modale
    const trapFocus = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return;
      
      if (e.shiftKey && document.activeElement === firstElement) {
        e.preventDefault();
        lastElement.focus();
      } else if (!e.shiftKey && document.activeElement === lastElement) {
        e.preventDefault();
        firstElement.focus();
      }
    };
    
    document.addEventListener('keydown', trapFocus);
    firstElement?.focus();
    
    return () => document.removeEventListener('keydown', trapFocus);
  }, []);
  
  return (
    <div 
      ref={modalRef}
      role="dialog" 
      aria-modal="true"
    >
      {children}
    </div>
  );
}
```

---

## 🧪 Tests d'accessibilité

### Outils de test

#### 1. Lighthouse (Chrome DevTools)

```bash
# Score d'accessibilité sur 100
Lighthouse → Accessibility → Run audit

Vérifie :
- Contraste
- ARIA
- Labels
- Focus indicators
- Alt text
```

#### 2. axe DevTools

```javascript
// Extension Chrome/Firefox
// Tests automatisés WCAG

// Ou en CLI
npm install -D @axe-core/cli
npx axe https://your-site.com
```

#### 3. Pa11y

```bash
npm install -D pa11y

# Test d'une page
npx pa11y https://your-site.com

# Test avec rapport
npx pa11y https://your-site.com --reporter html > report.html
```

#### 4. Jest + jest-axe

```javascript
// Test unitaire d'accessibilité
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

test('Button is accessible', async () => {
  const { container } = render(<Button>Click me</Button>);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

### Checklist manuelle

```
☐ Navigation au clavier complète (Tab, Enter, Space, Escape)
☐ Ordre de tab logique
☐ Focus visible sur tous les éléments interactifs
☐ Contraste WCAG AA minimum (4.5:1 texte, 3:1 UI)
☐ Images avec alt text
☐ Formulaires avec labels
☐ ARIA roles, states, properties appropriés
☐ Pas de flash > 3 fois/seconde
☐ Responsive (mobile accessible)
☐ Testable au lecteur d'écran (NVDA, JAWS, VoiceOver)
```

---

## 🎤 Lecteurs d'écran

### Principaux lecteurs

| Platform | Lecteur | Navigateur | Part de marché |
|----------|---------|------------|----------------|
| Windows | NVDA | Firefox | 41% |
| Windows | JAWS | Chrome | 40% |
| macOS | VoiceOver | Safari | 12% |
| iOS | VoiceOver | Safari | 5% |
| Android | TalkBack | Chrome | 2% |

### Raccourcis NVDA (Windows)

```
Ctrl + NVDA : Démarrer/Arrêter NVDA
NVDA + Q : Quitter
NVDA + Down : Lire tout
NVDA + Ctrl : Arrêter la lecture
Tab : Élément interactif suivant
H : Heading suivant
K : Link suivant
B : Button suivant
F : Form field suivant
T : Table suivante
```

### Raccourcis VoiceOver (macOS)

```
Cmd + F5 : Activer/Désactiver VoiceOver
VO + A : Lire tout
VO + Right/Left : Élément suivant/précédent
VO + Space : Activer élément
VO + U : Rotor (navigation par headings, links, etc.)
VO + H : Heading suivant
```

---

## ✓ Checklist design system accessible

```
COULEURS ET CONTRASTES
☐ Toutes les couleurs testées pour contraste
☐ Palette accessible définie (gray-500+ pour texte)
☐ Mode focus visible (outline ou ring)
☐ Ne pas utiliser uniquement la couleur pour transmettre l'info

TYPOGRAPHIE
☐ Font-size minimum : 16px (14px acceptable pour labels)
☐ Line-height minimum : 1.5 pour body text
☐ Éviter le texte justifié (difficile pour dyslexie)
☐ Max 75 caractères par ligne

COMPOSANTS
☐ Tous les composants navigables au clavier
☐ Focus indicators présents et visibles
☐ ARIA roles/states/properties appropriés
☐ Labels explicites (pas de placeholder seul)
☐ Messages d'erreur associés aux champs
☐ États (loading, error, success) annoncés

CONTENU
☐ Headings hiérarchiques (h1 → h2 → h3)
☐ Images avec alt descriptif
☐ Liens avec texte explicite (pas "cliquez ici")
☐ Icônes avec aria-label si seules

STRUCTURE
☐ Skip links pour navigation rapide
☐ Landmarks HTML5 (<nav>, <main>, <aside>)
☐ Ordre de tab logique
☐ Pas de piège de focus
```

---

## 📊 Synthèse visuelle

```
ACCESSIBILITÉ = POUR + CONTRASTE + ARIA + CLAVIER + TESTS
════════════════════════════════════════════════════════════

POUR (4 principes)         CONTRASTE WCAG
──────────────────         ──────────────────
P - Perceptible            Texte normal : 4.5:1 ✓
O - Opérable               Texte large : 3:1 ✓
U - Compréhensible         UI/Graphiques : 3:1 ✓
R - Robuste

ARIA (3 piliers)           NAVIGATION CLAVIER
────────────────           ──────────────────
Roles                      Tab / Shift+Tab
States                     Enter / Space
Properties                 Escape / Arrows

TESTS
─────────────────────────────────────────────
✓ Lighthouse (automatique)
✓ axe DevTools (automatique)
✓ Lecteur d'écran (manuel)
✓ Navigation clavier (manuel)
```

---

## 📚 Points clés à retenir

1. **WCAG AA minimum** : 4.5:1 pour texte, 3:1 pour UI
2. **Formule** : (L_clair + 0.05) / (L_foncé + 0.05)
3. **Palette accessible** : gray-500+ pour texte sur blanc
4. **ARIA** : Roles, States, Properties
5. **Clavier** : Tab, Enter, Space, Escape, Arrows
6. **Focus** : Toujours visible
7. **Tests** : Lighthouse + axe + lecteur d'écran
8. **ROI** : +15% d'audience, +30% conversion

---

## 🎯 Prochaines étapes

Maintenant que nos fondations sont accessibles, nous allons voir comment implémenter le **theming et le mode sombre**.

---

*Chapitre 01.4 | Accessibilité et Contrastes*

