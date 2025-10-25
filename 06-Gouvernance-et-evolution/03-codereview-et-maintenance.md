# Code Review et Maintenance

## 🎯 Objectifs

- ✓ Établir un processus de review efficace
- ✓ Maintenir la qualité dans le temps
- ✓ Gérer la dette technique
- ✓ Planifier les évolutions

---

## 👥 Process de Review

### Rôles

```
Author     → Crée la PR
Reviewer 1 → Review code quality
Reviewer 2 → Review UX/accessibility
Maintainer → Merge final
```

### Timeline

```
J0: PR créée
J1: Premier review
J2: Corrections
J3: Second review
J4: Approval + Merge
```

---

## ✅ Review Checklist

### Code
- [ ] Lisible et maintenable
- [ ] Pas de code mort
- [ ] Pas de console.log
- [ ] Error handling correct

### Types
- [ ] Props TypeScript
- [ ] Pas de `any`
- [ ] Generics appropriés

### Tests
- [ ] Unitaires présents
- [ ] Coverage suffisant
- [ ] Edge cases testés

### Perf
- [ ] Pas de re-renders inutiles
- [ ] useMemo/useCallback appropriés
- [ ] Code splitting si besoin

### A11y
- [ ] ARIA correct
- [ ] Keyboard navigation
- [ ] Screen reader

---

## 🔧 Maintenance

### Dette technique

```
Score = (Deprecated_APIs × 2) + (Outdated_deps × 1) + (Missing_tests × 3)

< 10 : Bonne santé
10-20 : Attention
> 20 : Action requise
```

### Planning

```
Sprint N:
- 70% Features
- 20% Bugs
- 10% Dette technique
```

---

## 📊 Metrics

### Health Score

```
H = (Tests × 0.3) + (Docs × 0.2) + (Coverage × 0.3) + (A11y × 0.2)

> 80 : Excellent
60-80 : Bon
< 60 : À améliorer
```

---

## 📚 Points clés

1. **2 reviewers** : Minimum
2. **24-48h** : Délai de review
3. **Feedback constructif** : Toujours
4. **Automatisation** : Linting, tests
5. **Dette technique** : 10% du temps
6. **Metrics** : Tracking continu

---

*Chapitre 06.3 | Code Review et Maintenance*

