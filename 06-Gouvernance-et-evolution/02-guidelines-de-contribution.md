# Guidelines de Contribution

## 🎯 Objectifs

- ✓ Faciliter les contributions
- ✓ Maintenir la qualité du code
- ✓ Assurer la cohérence
- ✓ Documenter les processus

---

## 📝 CONTRIBUTING.md

```markdown
# Contributing to [Design System]

## Code of Conduct

Soyez respectueux et inclusif.

## How to Contribute

1. Fork le repository
2. Créez une branche (`git checkout -b feature/amazing`)
3. Committez vos changements (`git commit -m 'feat: add amazing feature'`)
4. Push (`git push origin feature/amazing`)
5. Ouvrez une Pull Request

## Development Setup

```bash
git clone https://github.com/org/design-system
cd design-system
npm install
npm run dev
```

## Commit Convention

Nous utilisons [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add new component
fix: resolve button hover issue
docs: update README
style: format code
refactor: simplify logic
test: add unit tests
chore: update dependencies
```

## Pull Request Process

1. Assurez-vous que les tests passent
2. Mettez à jour la documentation
3. Ajoutez des stories Storybook
4. Demandez une review à 2 personnes minimum
5. Attendez l'approbation avant merge

## Component Checklist

- [ ] TypeScript types
- [ ] Tests unitaires
- [ ] Tests d'accessibilité
- [ ] Storybook stories
- [ ] Documentation
- [ ] Responsive
- [ ] Dark mode support
```

---

## 🔍 Code Review Checklist

```markdown
## Code Review Template

### Functionality
- [ ] Le composant fonctionne comme prévu
- [ ] Tous les cas d'usage sont couverts
- [ ] Pas de régression

### Code Quality
- [ ] Code lisible et maintenable
- [ ] Pas de duplication
- [ ] Bonnes pratiques respectées
- [ ] TypeScript correctement typé

### Tests
- [ ] Tests unitaires présents
- [ ] Coverage > 80%
- [ ] Tests d'accessibilité

### Documentation
- [ ] Props documentés (JSDoc)
- [ ] Stories Storybook
- [ ] Exemples d'usage
- [ ] Migration guide si breaking change

### Accessibilité
- [ ] WCAG AA respecté
- [ ] Navigation clavier
- [ ] Screen reader compatible
- [ ] Focus visible

### Performance
- [ ] Pas de re-render inutiles
- [ ] Optimisation images
- [ ] Code splitting si nécessaire
```

---

## 🎯 Issue Templates

```markdown
---
name: Bug Report
about: Signaler un bug
---

## Description
Décrivez le bug clairement

## Steps to Reproduce
1. Allez sur '...'
2. Cliquez sur '...'
3. Le bug apparaît

## Expected Behavior
Ce qui devrait se passer

## Actual Behavior
Ce qui se passe réellement

## Screenshots
Si applicable

## Environment
- OS: [e.g. macOS]
- Browser: [e.g. Chrome 120]
- Version: [e.g. 2.1.0]
```

---

## 📚 Points clés

1. **CONTRIBUTING.md** : Toujours présent
2. **Commit convention** : Conventional Commits
3. **PR template** : Checklist complète
4. **Code review** : 2 approbations minimum
5. **Tests** : Obligatoires
6. **Documentation** : À jour
7. **Accessibilité** : Non négociable

---

*Chapitre 06.2 | Guidelines de Contribution*

