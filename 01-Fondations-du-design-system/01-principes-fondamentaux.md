# Principes Fondamentaux d'un Design System

## 🎯 Objectifs d'apprentissage

À la fin de ce chapitre, vous serez capable de :
- ✓ Identifier et appliquer les 8 principes fondamentaux du design
- ✓ Créer une hiérarchie visuelle efficace
- ✓ Comprendre et utiliser la théorie de la Gestalt
- ✓ Appliquer les lois de l'UX dans vos composants
- ✓ Établir des principes de design personnalisés pour votre projet

---

## 📐 Les 8 Principes Fondamentaux

### 1. Cohérence (Consistency)

**Définition** : Les éléments similaires doivent se comporter de manière similaire.

#### Formule de cohérence

```
C = 1 - (V / E)

où :
C = Coefficient de cohérence (0-1)
V = Nombre de variations
E = Nombre d'éléments totaux

Objectif : C > 0.85
```

#### Exemple concret

❌ **Incohérent** :
```tsx
// Page 1 : Bouton primaire
<button className="bg-blue-500 hover:bg-blue-600 px-4 py-2">
  Valider
</button>

// Page 2 : Bouton primaire (différent !)
<button className="bg-indigo-600 hover:bg-indigo-700 px-6 py-3">
  Confirmer
</button>

// Page 3 : Bouton primaire (encore différent !)
<button className="bg-sky-400 hover:bg-sky-500 px-5 py-2.5">
  OK
</button>
```

✓ **Cohérent** :
```tsx
// Partout dans l'app
<Button variant="primary">Valider</Button>
<Button variant="primary">Confirmer</Button>
<Button variant="primary">OK</Button>

// Tous utilisent les mêmes tokens
```

#### Types de cohérence

| Type | Description | Exemple |
|------|-------------|---------|
| **Visuelle** | Même apparence | Tous les boutons primaires en bleu #3B82F6 |
| **Fonctionnelle** | Même comportement | Cliquer un bouton déclenche toujours une action |
| **Verbale** | Même langage | "Enregistrer" partout, jamais "Sauvegarder" ou "Valider" |
| **Structurelle** | Même organisation | Formulaires : Label → Input → Message d'erreur |

### 2. Hiérarchie (Hierarchy)

**Définition** : Guider l'attention de l'utilisateur par l'importance visuelle.

#### Formule de poids visuel

```
P = (T × 2) + (C × 1.5) + (E × 1.2) + (P × 1)

où :
T = Taille (size factor)
C = Contraste (contrast ratio)
E = Épaisseur (font-weight factor)
P = Position (layout factor)

Plus P est élevé, plus l'élément attire l'attention
```

#### Exemple de hiérarchie typographique

```css
/* Niveau 1 : Titre principal */
h1 {
  font-size: 48px;        /* T = 3 */
  font-weight: 700;       /* E = 2 */
  color: #000000;         /* C = 21 (sur blanc) */
  /* Poids visuel : P ≈ 15 */
}

/* Niveau 2 : Sous-titre */
h2 {
  font-size: 32px;        /* T = 2 */
  font-weight: 600;       /* E = 1.5 */
  color: #1F2937;         /* C = 18 */
  /* Poids visuel : P ≈ 10 */
}

/* Niveau 3 : Corps de texte */
p {
  font-size: 16px;        /* T = 1 */
  font-weight: 400;       /* E = 1 */
  color: #6B7280;         /* C = 7 */
  /* Poids visuel : P ≈ 4 */
}
```

#### Règle des 3 niveaux

💡 **Règle d'or** : Limitez-vous à **3 niveaux de hiérarchie maximum** par section.

```
Niveau 1 : Information primaire    (1 élément)
Niveau 2 : Information secondaire  (2-3 éléments)
Niveau 3 : Information tertiaire   (reste)

Ratio visuel : 3:2:1
```

### 3. Espacement (Spacing)

**Définition** : L'espace négatif structure le contenu et améliore la lisibilité.

#### Échelle d'espacement harmonique

```
Suite géométrique : S(n) = S₀ × r^n

où :
S₀ = Espacement de base (4px ou 8px)
r = Ratio (souvent 1.5 ou 2)
n = Niveau

Échelle en base 4 :
S = {4, 8, 12, 16, 24, 32, 48, 64, 96, 128}px
```

#### Loi de Fitts et espacement

```
T = a + b × log₂(D/W + 1)

où :
T = Temps pour atteindre une cible
D = Distance à la cible
W = Largeur de la cible
a, b = Constantes empiriques

💡 Implication : Plus les éléments sont espacés et grands, 
plus ils sont faciles à atteindre.
```

#### Application pratique

```tsx
// Espacement interne (padding)
<Button className="px-4 py-2">     {/* 16px × 8px */}
<Button className="px-6 py-3">     {/* 24px × 12px */}
<Button className="px-8 py-4">     {/* 32px × 16px */}

// Espacement entre éléments (gap)
<div className="space-y-4">        {/* 16px entre enfants */}
<div className="space-y-8">        {/* 32px entre sections */}
<div className="space-y-16">       {/* 64px entre blocs majeurs */}
```

### 4. Alignement (Alignment)

**Définition** : Créer des relations visuelles par l'organisation spatiale.

#### Types d'alignement

```
ALIGNEMENT GAUCHE (Left-aligned)
─────────────────────────────────
Titre du formulaire
Nom : [____________]
Email : [____________]
Message : [____________]

→ Meilleur pour la lecture (langues LTR)
→ Scan en forme de F

ALIGNEMENT CENTRÉ (Center-aligned)
─────────────────────────────────
       Titre principal
    ──────────────
    [   Bouton   ]

→ Pour contenus courts et symétriques
→ Pages de landing

ALIGNEMENT DROIT (Right-aligned)
─────────────────────────────────
                    Titre
          [____________] : Nom
          [____________] : Email

→ Pour les labels de formulaire
→ Alignement de nombres
```

#### Grille de base (Baseline Grid)

```
Grille verticale de 8px :
─────────────────────────
Ligne 1  (8px)  ─────
                     ↕ Texte (16px)
Ligne 3  (24px) ─────
                     ↕ Espace (8px)
Ligne 4  (32px) ─────
                     ↕ Texte (16px)
Ligne 6  (48px) ─────
```

**Formule d'alignement** :
```
Position_Y = n × grid_base

où grid_base = 8px
```

### 5. Contraste (Contrast)

**Définition** : Différencier les éléments par leur apparence visuelle.

#### Ratio de contraste WCAG

```
Contraste = (L₁ + 0.05) / (L₂ + 0.05)

où :
L₁ = Luminance relative du plus clair
L₂ = Luminance relative du plus foncé

Luminance : L = 0.2126×R + 0.7152×G + 0.0722×B
(avec R, G, B normalisés entre 0 et 1)
```

#### Standards WCAG

| Niveau | Texte normal | Texte large | Usage |
|--------|--------------|-------------|-------|
| **AAA** | 7:1 | 4.5:1 | Idéal |
| **AA** | 4.5:1 | 3:1 | Minimum |
| **Échec** | < 4.5:1 | < 3:1 | Non conforme |

#### Exemple de calcul

```javascript
// Blanc (#FFFFFF) sur Bleu (#3B82F6)
L_blanc = 1.0
L_bleu = 0.258

Contraste = (1.0 + 0.05) / (0.258 + 0.05)
          = 1.05 / 0.308
          = 3.41:1

Verdict : ❌ Échec WCAG AA pour texte normal (besoin 4.5:1)
Solution : Utiliser un bleu plus foncé (#1E40AF) → 7.2:1 ✓
```

### 6. Échelle (Scale)

**Définition** : Utiliser des proportions harmonieuses pour créer de la cohérence.

#### Échelle typographique

**Ratio modulaire** : progression géométrique basée sur un ratio musical

```
Ratios classiques :
- 1.125 (Octave majeure)
- 1.200 (Tierce mineure)
- 1.250 (Quarte parfaite)
- 1.333 (Quarte augmentée)
- 1.414 (Triton - √2)
- 1.500 (Quinte parfaite)
- 1.618 (Nombre d'or φ)

Calcul : size(n) = base × ratio^n
```

#### Exemple avec le nombre d'or (φ = 1.618)

```
Base : 16px

size(-2) = 16 × (1/1.618)² ≈ 6.11px   → 6px
size(-1) = 16 × (1/1.618)  ≈ 9.88px   → 10px
size(0)  = 16px                        → 16px (base)
size(1)  = 16 × 1.618      ≈ 25.89px  → 26px
size(2)  = 16 × 1.618²     ≈ 41.89px  → 42px
size(3)  = 16 × 1.618³     ≈ 67.77px  → 68px
```

### 7. Couleur (Color)

**Définition** : Transmettre du sens et créer une hiérarchie émotionnelle.

#### Théorie des couleurs : Modèle HSL

```
HSL = (Hue, Saturation, Lightness)

H (Teinte) : 0-360° sur le cercle chromatique
S (Saturation) : 0-100% (gris → couleur pure)
L (Luminosité) : 0-100% (noir → blanc)
```

#### Palette harmonique

```css
/* Couleur primaire */
--primary: hsl(217, 91%, 60%);      /* Bleu #3B82F6 */

/* Variations harmoniques */
--primary-50:  hsl(217, 91%, 95%);  /* Très clair */
--primary-100: hsl(217, 91%, 90%);
--primary-200: hsl(217, 91%, 80%);
--primary-300: hsl(217, 91%, 70%);
--primary-400: hsl(217, 91%, 65%);
--primary-500: hsl(217, 91%, 60%);  /* Base */
--primary-600: hsl(217, 91%, 50%);
--primary-700: hsl(217, 91%, 40%);
--primary-800: hsl(217, 91%, 30%);
--primary-900: hsl(217, 91%, 20%);  /* Très foncé */
```

#### Règle 60-30-10

```
Interface équilibrée :
────────────────────
60% - Couleur dominante (neutre, backgrounds)
30% - Couleur secondaire (accents, sections)
10% - Couleur d'action (CTAs, highlights)

Exemple :
60% → Gris clair (#F9FAFB)
30% → Gris moyen (#E5E7EB)
10% → Bleu primaire (#3B82F6)
```

### 8. Typographie (Typography)

**Définition** : Rendre le texte lisible, hiérarchisé et esthétique.

#### Formule de lisibilité de Flesch

```
Score = 206.835 - 1.015 × (mots/phrases) - 84.6 × (syllabes/mots)

Score > 60 : Facile à lire
Score 30-60 : Moyen
Score < 30 : Difficile
```

#### Longueur de ligne optimale

```
Formule de Tschichold :
────────────────────────
L_optimale = 2 × font_size × 30

Pour font-size = 16px :
L_optimale = 2 × 16 × 30 = 960px / 16 = 60 caractères

Plage acceptable : 45-75 caractères par ligne
```

#### Interlignage (line-height)

```
line-height = font-size × ratio

Ratios recommandés :
- Titres (grands) : 1.1 - 1.3
- Corps de texte : 1.5 - 1.7
- Texte dense : 1.7 - 2.0

Exemple :
font-size: 16px
line-height: 16px × 1.5 = 24px
```

---

## 🎨 Théorie de la Gestalt

### Les 6 lois de la perception

#### 1. Loi de proximité

```
Éléments proches → Perçus comme un groupe

••  ••     ••••
••  ••     ••••

2 groupes  1 groupe
```

**Application** :
```tsx
// Grouper visuellement les champs liés
<div className="space-y-1">  {/* Groupe : label + input */}
  <label>Nom</label>
  <input />
</div>

<div className="mt-6" />  {/* Séparation entre groupes */}

<div className="space-y-1">
  <label>Email</label>
  <input />
</div>
```

#### 2. Loi de similarité

```
Éléments similaires → Perçus comme liés

●●●●●     ○●○●○
●●●●●     ○●○●○

1 groupe  Colonnes perçues
```

**Application** :
```css
/* Tous les CTA partagent le même style */
.cta-button {
  background: #3B82F6;
  color: white;
  font-weight: 600;
  /* → Utilisateur comprend : "boutons d'action" */
}
```

#### 3. Loi de continuité

```
L'œil suit naturellement les lignes

────→────→────→

Perception d'un flux
```

**Application** :
```tsx
// Timeline horizontale
<div className="flex space-x-4">
  <Step completed>1. Info</Step>
  <Step current>2. Paiement</Step>
  <Step>3. Confirmation</Step>
</div>
```

#### 4. Loi de clôture

```
Le cerveau complète les formes incomplètes

( )  → Perçu comme un cercle
```

**Application** :
```tsx
// Icons outline → le cerveau "complète" la forme
<svg>
  <circle r="10" fill="none" stroke="black" />
</svg>
```

#### 5. Loi figure-fond

```
Distinction entre premier plan et arrière-plan

████████    ═══════════
██   ██  vs      ║║
████████    ═══════════

Contraste clair → Bonne séparation
```

#### 6. Loi du destin commun

```
Éléments qui bougent ensemble → Perçus comme un groupe

Animation synchronisée → Groupement perçu
```

---

## 📏 Lois de l'UX appliquées

### 1. Loi de Hick

```
T = b × log₂(n + 1)

où :
T = Temps de décision
n = Nombre de choix
b = Constante empirique

💡 Implication : Moins de choix = Décision plus rapide
```

**Application** :
```tsx
// ❌ Trop de choix
<Select>
  <option>Option 1</option>
  <option>Option 2</option>
  {/* ... 50 options ... */}
</Select>

// ✓ Groupement ou recherche
<Select searchable groups>
  <optgroup label="Populaires">
    <option>Option A</option>
  </optgroup>
</Select>
```

### 2. Loi de Miller (7±2)

```
Capacité de mémoire de travail : 5-9 éléments

Optimal : 7 éléments
```

**Application** :
```tsx
// Navigation : max 7 items principaux
<nav>
  <Link>Accueil</Link>
  <Link>Produits</Link>
  <Link>Solutions</Link>
  <Link>Tarifs</Link>
  <Link>Ressources</Link>
  <Link>À propos</Link>
  <Link>Contact</Link>
</nav>
```

### 3. Loi de Pareto (80/20)

```
80% des utilisations → 20% des fonctionnalités

Focus sur les 20% critiques
```

**Application** :
```
Hiérarchie des fonctionnalités :
────────────────────────────────
Primaires (20%) : Boutons visibles, accessibles
Secondaires (30%) : Menus, dropdowns
Tertiaires (50%) : Paramètres avancés, cachés
```

### 4. Loi de Fitts (déjà vue)

```
Cibles importantes = Grandes + Proches
```

### 5. Loi de Jakob

```
"Les utilisateurs passent la majorité de leur temps 
sur d'autres sites que le vôtre."

→ Respecter les conventions établies
```

**Application** :
```
Conventions à respecter :
✓ Logo en haut à gauche
✓ Navigation horizontale en haut ou verticale à gauche
✓ Bouton principal à droite dans les dialogs
✓ Icône hamburger pour menu mobile
✓ Liens bleus soulignés
```

---

## ✓ Checklist des principes

```
Mon design system respecte-t-il :

Cohérence
☐ Mêmes composants utilisés partout
☐ Nomenclature uniforme
☐ Comportements prévisibles

Hiérarchie
☐ 3 niveaux max par section
☐ Poids visuels proportionnels
☐ Chemin visuel clair

Espacement
☐ Échelle harmonique (base 4 ou 8)
☐ Espacement cohérent
☐ White space suffisant

Alignement
☐ Grille de base respectée
☐ Alignements intentionnels
☐ Pas d'éléments "orphelins"

Contraste
☐ WCAG AA minimum (4.5:1)
☐ Hiérarchie par contraste
☐ États visuellement distincts

Échelle
☐ Ratio modulaire défini
☐ Tailles harmonieuses
☐ Progression logique

Couleur
☐ Palette cohérente
☐ Règle 60-30-10
☐ Signification claire

Typographie
☐ 2-3 polices maximum
☐ Échelle définie
☐ 45-75 caractères/ligne
```

---

## 📊 Synthèse visuelle

```
LES 8 PRINCIPES FONDAMENTAUX
═══════════════════════════════════════════════════════

1. COHÉRENCE          2. HIÉRARCHIE        3. ESPACEMENT
   ████ ████            H1 ████              ██    ██
   ████ ████            H2 ███               ██    ██
   ████ ████            P  ██                
   Identique            Taille               Respirant

4. ALIGNEMENT         5. CONTRASTE         6. ÉCHELLE
   ████                 ████ ▓▓▓▓            ████████
   ████                 Fort Faible          ████
   ████                                      ██
   Structuré           Distinct             Harmonieux

7. COULEUR            8. TYPOGRAPHIE
   🔵 60%              Inter
   🟢 30%              Roboto
   🔴 10%              Max 2-3 fonts
   Équilibré           Lisible
```

---

## ✍️ Exercice pratique

### Auditez une interface existante

Prenez une page de votre application et évaluez chaque principe (score /10) :

```
AUDIT DE : ________________________

Principe           Score /10    Notes
─────────────────────────────────────────
Cohérence          [  ]        
Hiérarchie         [  ]        
Espacement         [  ]        
Alignement         [  ]        
Contraste          [  ]        
Échelle            [  ]        
Couleur            [  ]        
Typographie        [  ]        

TOTAL              [  ]/80

Interprétation :
65-80 : Excellent
50-64 : Bon
35-49 : À améliorer
0-34  : Refonte nécessaire
```

---

## 📚 Points clés à retenir

1. **Cohérence** > Créativité dans un design system
2. **3 niveaux de hiérarchie** maximum par section
3. **Échelle harmonique** pour espacement et typo
4. **WCAG AA minimum** : 4.5:1 pour le contraste
5. **60-30-10** pour l'équilibre des couleurs
6. **45-75 caractères** par ligne pour la lisibilité
7. Les **lois de la Gestalt** guident la perception
8. Suivre les **conventions établies** (Loi de Jakob)

---

## 🎯 Prochaines étapes

Maintenant que nous avons posé les principes fondamentaux, nous allons voir comment les traduire en **design tokens** : les variables atomiques de votre design system.

---

*Chapitre 01.1 | Principes Fondamentaux*

