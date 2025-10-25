# Fichiers Design Figma

## 🎯 Objectifs

- ✓ Structurer vos fichiers Figma efficacement
- ✓ Créer des composants et variants réutilisables
- ✓ Gérer les styles et variables
- ✓ Optimiser la collaboration design-dev

---

## 📁 Structure de fichiers

```
Design System/
├── 📄 Foundations
│   ├── Colors
│   ├── Typography
│   ├── Spacing
│   └── Icons
├── 📄 Components
│   ├── Primitives (Button, Input, etc.)
│   ├── Composite (Card, Modal, etc.)
│   └── Patterns
├── 📄 Templates
│   ├── Auth
│   ├── Dashboard
│   └── Settings
└── 📄 Documentation
```

---

## 🎨 Variables Figma

```
Colors/
├── Primitive/
│   ├── blue-50 to blue-950
│   ├── gray-50 to gray-950
│   └── ...
├── Semantic/
│   ├── text-primary
│   ├── text-secondary
│   ├── bg-primary
│   └── border-default
└── Component/
    ├── button-primary-bg
    └── button-primary-text
```

---

## 🧩 Composants Figma

### Auto Layout

```
Button (Auto Layout)
├── [Icon] (optional)
├── Label (Text)
└── [Icon] (optional)

Props:
- Padding: 16px horizontal, 8px vertical
- Gap: 8px
- Fill: Hug contents
```

### Variants

```
Button
├── Variant = Primary
│   ├── State = Default
│   ├── State = Hover
│   ├── State = Active
│   └── State = Disabled
├── Variant = Secondary
│   └── ...
└── Variant = Ghost
    └── ...
```

---

## 🔗 Dev Mode & Handoff

```
// Export settings
PNG: @2x, @3x
SVG: Outline strokes
CSS: Copy styles
```

---

## 📚 Points clés

1. **Variables** : Couleurs, spacing, radius
2. **Auto Layout** : Tous les composants
3. **Variants** : États et types
4. **Nommage** : Cohérent avec le code
5. **Components** : Published dans library
6. **Documentation** : Inline dans Figma
7. **Dev Mode** : Inspect et export

---

*Chapitre 05.1 | Fichiers Design Figma*

