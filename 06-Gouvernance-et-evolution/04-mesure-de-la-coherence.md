# Mesure de la Cohérence

## 🎯 Objectifs

- ✓ Définir des métriques de cohérence
- ✓ Mesurer l'adoption du design system
- ✓ Identifier les incohérences
- ✓ Suivre l'évolution

---

## 📊 Métriques Clés

### 1. Taux d'adoption

```
Adoption = (Components_DS_utilisés / Components_totaux) × 100

Objectif : > 80%
```

### 2. Cohérence visuelle

```
Cohérence = (Spacings_conformes / Spacings_totaux) × 100

Objectif : > 90%
```

### 3. Coverage Design Tokens

```
Coverage = (Tokens_utilisés / Tokens_hardcoded) × 100

Objectif : 100%
```

---

## 🔍 Outils de Mesure

### ESLint Rules

```js
// Interdire les couleurs hardcodées
'no-hardcoded-colors': 'error'

// Forcer l'usage des tokens
'use-design-tokens': 'error'
```

### Audit Script

```ts
async function auditColors() {
  const files = await glob('src/**/*.tsx');
  const hardcoded = [];
  
  files.forEach(file => {
    const content = fs.readFileSync(file, 'utf-8');
    const colorRegex = /#[0-9a-f]{6}|rgb\(/gi;
    
    if (colorRegex.test(content)) {
      hardcoded.push(file);
    }
  });
  
  return {
    total: files.length,
    violations: hardcoded.length,
    compliance: ((files.length - hardcoded.length) / files.length) × 100
  };
}
```

---

## 📈 Dashboard de Cohérence

```tsx
export function CoherenceDashboard() {
  return (
    <div className="grid grid-cols-3 gap-6">
      <KPICard
        title="Adoption DS"
        value="85%"
        change={5}
        target={90}
      />
      
      <KPICard
        title="Tokens utilisés"
        value="92%"
        change={2}
        target={100}
      />
      
      <KPICard
        title="Tests A11y"
        value="94%"
        change={3}
        target={95}
      />
    </div>
  );
}
```

---

## 📚 Points clés

1. **Adoption > 80%** : Objectif minimum
2. **Tokens 100%** : Pas de hardcoded values
3. **Audit régulier** : Mensuel
4. **Dashboard** : Visible par tous
5. **Trend tracking** : Évolution dans le temps

---

*Chapitre 06.4 | Mesure de la Cohérence*

