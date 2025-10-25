# Gestion des Versions

## 🎯 Objectifs

- ✓ Comprendre le versioning sémantique
- ✓ Gérer les breaking changes
- ✓ Créer des changelogs clairs
- ✓ Maintenir la compatibilité

---

## 📊 Semantic Versioning

```
MAJOR.MINOR.PATCH

Exemple : 2.4.7
         │ │ │
         │ │ └─ PATCH: Bug fixes
         │ └─── MINOR: Nouvelles features (backward compatible)
         └───── MAJOR: Breaking changes
```

### Règles

```
1.0.0 → 1.0.1  : Bug fix (patch)
1.0.1 → 1.1.0  : Nouvelle feature (minor)
1.1.0 → 2.0.0  : Breaking change (major)
```

---

## 🔄 Changelog

```markdown
# Changelog

## [2.0.0] - 2025-01-15

### 💥 BREAKING CHANGES
- **Button**: Removed `color` prop (use `variant` instead)
- **Input**: Renamed `error` to `errorMessage`

### ✨ Features
- Added `Toast` component with 4 variants
- New `useMediaQuery` hook
- Dark mode support for all components

### 🐛 Bug Fixes
- Fixed focus trap in Modal component
- Corrected spacing in Card footer
- Resolved Safari border-radius bug

### 📚 Documentation
- Updated Storybook to v7
- Added migration guide
- New examples for form validation

### 🔧 Internal
- Upgraded to React 18
- Improved TypeScript types
- Better tree-shaking support
```

---

## 🎯 Release Process

```
1. Feature development (feature branch)
2. Pull Request + Review
3. Merge to main
4. Update version number
5. Generate changelog
6. Create git tag
7. Publish to npm
8. Deploy Storybook
9. Announce release
```

---

## 📦 Migration Guides

```markdown
# Migration v1 → v2

## Breaking Changes

### Button component

**Before (v1)**:
```tsx
<Button color="primary">Click</Button>
```

**After (v2)**:
```tsx
<Button variant="primary">Click</Button>
```

**Migration script**:
```bash
# Find and replace
find . -name "*.tsx" -exec sed -i 's/color=/variant=/g' {} +
```

## Deprecation Warnings

- `color` prop: Deprecated in v1.5, removed in v2.0
- Use `variant` instead

---

## 📚 Points clés

1. **SemVer** : MAJOR.MINOR.PATCH
2. **Changelog** : Toujours à jour
3. **Breaking changes** : MAJOR version
4. **Migration guide** : Pour chaque MAJOR
5. **Deprecation** : Warning avant removal
6. **Backward compat** : Maintenir 2-3 versions

---

*Chapitre 06.1 | Gestion des Versions*

