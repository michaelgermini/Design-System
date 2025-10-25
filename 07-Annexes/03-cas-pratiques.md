# Cas Pratiques

## 🏢 Études de Cas Réelles

### Cas 1 : Startup SaaS (15 personnes)

**Contexte**
- 1 application web
- 3 développeurs front-end
- 1 designer
- Besoin de vélocité

**Solution mise en place**
```
- shadcn/ui comme base
- 15 composants customs
- Storybook pour documentation
- Tokens en CSS variables
```

**Résultats après 6 mois**
- ✅ Temps de dev : -40%
- ✅ Bugs UI : -60%
- ✅ Onboarding nouveau dev : 1 semaine (vs 3)
- ✅ Score accessibilité : 92/100

**Budget**
- Initial : 20k€
- Maintenance : 1k€/mois
- ROI : 180% première année

---

### Cas 2 : E-commerce (100+ personnes)

**Contexte**
- Site principal + 5 microsites
- 15 développeurs front-end
- 3 designers
- Forte croissance

**Solution mise en place**
```
- Design system custom complet
- 80+ composants
- Multi-theming
- Documentation extensive
- Design tokens multi-plateformes
```

**Résultats après 12 mois**
- ✅ Cohérence : 95% (vs 40%)
- ✅ Réduction CSS : -35%
- ✅ Lighthouse score : 92 (vs 68)
- ✅ Conversion : +8%

**Budget**
- Initial : 150k€
- Maintenance : 10k€/mois
- ROI : 240% première année

---

### Cas 3 : Application Mobile (React Native)

**Contexte**
- App iOS + Android
- 8 développeurs
- Design system web existant

**Solution mise en place**
```
- Adaptation du DS web
- Tokens partagés (Style Dictionary)
- Composants React Native
- Storybook React Native
```

**Résultats**
- ✅ Time-to-market : -50%
- ✅ Cohérence cross-platform : 90%
- ✅ Code partagé : 70%

---

## 🎯 Exercices Pratiques

### Exercice 1 : Audit de votre projet

**Objectif** : Identifier les gains potentiels d'un design system

```
1. Comptez le nombre de :
   - Boutons uniques dans votre app
   - Couleurs différentes utilisées
   - Tailles de texte différentes
   - Espacements différents

2. Calculez :
   - Temps moyen pour créer une nouvelle page
   - Nombre de bugs UI par sprint
   - Temps d'onboarding d'un nouveau dev

3. Projetez avec un DS :
   - Nombre de composants nécessaires
   - Réduction de temps estimée
   - ROI potentiel
```

---

### Exercice 2 : Créer votre premier composant

**Objectif** : Implémenter un Button avec variants

```tsx
// 1. Définir les variants
type ButtonVariant = 'primary' | 'secondary' | 'ghost';
type ButtonSize = 'sm' | 'md' | 'lg';

// 2. Implémenter
export const Button = ({ variant, size, children }: ButtonProps) => {
  // ...
};

// 3. Tester
describe('Button', () => {
  // Tests unitaires
});

// 4. Documenter
// Button.stories.tsx

// 5. Utiliser
<Button variant="primary" size="md">
  Click me
</Button>
```

---

### Exercice 3 : Migration vers tokens

**Objectif** : Remplacer les valeurs hardcodées

```tsx
// Avant
<div style={{ 
  color: '#333',
  padding: '12px',
  borderRadius: '6px'
}}>

// Après
<div style={{ 
  color: 'var(--color-text-primary)',
  padding: 'var(--spacing-3)',
  borderRadius: 'var(--radius-md)'
}}>
```

**Script d'aide** :
```bash
# Trouver les couleurs hardcodées
grep -r "#[0-9a-f]\{6\}" src/

# Remplacer
sed -i 's/#333333/var(--color-text-primary)/g' src/**/*.tsx
```

---

## 📊 Templates de Calcul ROI

### Template 1 : Économies de temps

```
Temps actuel sans DS :
- Création page : _____ heures
- Debug UI : _____ heures/sprint
- Onboarding : _____ semaines

Temps estimé avec DS :
- Création page : _____ heures (généralement -50%)
- Debug UI : _____ heures/sprint (généralement -70%)
- Onboarding : _____ semaines (généralement -60%)

Économie annuelle :
(Heures économisées × Taux horaire × Nombre de devs)
= _______________ €
```

### Template 2 : Budget DS

```
Coûts initiaux :
- Audit : _____ €
- Setup : _____ €
- Composants (nombre × 4h × taux) : _____ €
- Documentation : _____ €
Total : _____ €

Coûts récurrents (annuel) :
- Maintenance : _____ €
- Évolutions : _____ €
Total : _____ €

ROI = (Économies - Coûts) / Coûts × 100
    = _____ %
```

---

## 🎓 Quiz de validation

### Niveau Débutant

1. Qu'est-ce qu'un design token ?
2. Citez 3 bénéfices d'un design system
3. Quelle est la différence entre WCAG AA et AAA ?

### Niveau Intermédiaire

1. Comment calculer le ratio de contraste WCAG ?
2. Expliquez le pattern de compound components
3. Quelle est la formule du semantic versioning ?

### Niveau Avancé

1. Comment implémenter un polymorphic component en TypeScript ?
2. Expliquez la stratégie de migration d'un design system
3. Comment mesurer le ROI d'un design system ?

---

*Annexe 03 | Cas Pratiques*

