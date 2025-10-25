# Tests et Qualité

## 🎯 Objectifs

- ✓ Mettre en place une stratégie de tests
- ✓ Tester les composants unitairement
- ✓ Vérifier l'accessibilité automatiquement
- ✓ Maintenir la qualité du code

---

## 🧪 Types de Tests

### Pyramide de tests

```
         E2E (5%)
        /       \
       /         \
    Intégration (15%)
     /             \
    /               \
  Unitaires (80%)
```

---

## ⚛️ Tests Unitaires (Jest + RTL)

### Setup

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

```js
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1'
  }
};
```

### Test d'un composant

```tsx
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
  
  it('handles click events', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  it('is disabled when loading', () => {
    render(<Button loading>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
  
  it('shows spinner when loading', () => {
    render(<Button loading>Click me</Button>);
    expect(screen.getByRole('status')).toBeInTheDocument();
  });
  
  it('supports keyboard navigation', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const button = screen.getByRole('button');
    fireEvent.keyPress(button, { key: 'Enter', code: 'Enter' });
    
    expect(handleClick).toHaveBeenCalled();
  });
  
  it('applies correct variant styles', () => {
    render(<Button variant="primary">Primary</Button>);
    const button = screen.getByRole('button');
    
    expect(button).toHaveClass('button-primary');
  });
});
```

---

## ♿ Tests d'accessibilité

### jest-axe

```bash
npm install --save-dev jest-axe
```

```tsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

describe('Button accessibility', () => {
  it('has no accessibility violations', async () => {
    const { container } = render(<Button>Click me</Button>);
    const results = await axe(container);
    
    expect(results).toHaveNoViolations();
  });
  
  it('has correct ARIA attributes', () => {
    render(<Button aria-label="Close">×</Button>);
    const button = screen.getByRole('button', { name: 'Close' });
    
    expect(button).toHaveAttribute('aria-label', 'Close');
  });
  
  it('supports screen readers', () => {
    render(
      <Button>
        <span aria-hidden="true">👍</span>
        <span className="sr-only">Like</span>
      </Button>
    );
    
    expect(screen.getByText('Like')).toBeInTheDocument();
  });
});
```

---

## 🎭 Tests Visuels (Storybook)

### Interaction tests

```tsx
// Button.stories.tsx
import { expect } from '@storybook/jest';
import { within, userEvent } from '@storybook/testing-library';

export const ClickableButton: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    const button = canvas.getByRole('button');
    
    // Verify initial state
    await expect(button).toBeInTheDocument();
    
    // Simulate click
    await userEvent.click(button);
    
    // Verify after click
    await expect(button).toHaveClass('button-active');
  }
};
```

---

## 🔍 Tests E2E (Playwright)

```bash
npm install --save-dev @playwright/test
```

```ts
// e2e/login.spec.ts
import { test, expect } from '@playwright/test';

test('user can login', async ({ page }) => {
  await page.goto('/login');
  
  await page.fill('[name="email"]', 'user@example.com');
  await page.fill('[name="password"]', 'password123');
  await page.click('button[type="submit"]');
  
  await expect(page).toHaveURL('/dashboard');
  await expect(page.locator('h1')).toContainText('Dashboard');
});
```

---

## 📊 Coverage

```bash
npm test -- --coverage
```

Objectifs:
- **Statements**: > 80%
- **Branches**: > 75%
- **Functions**: > 80%
- **Lines**: > 80%

```json
// jest.config.js
{
  "coverageThreshold": {
    "global": {
      "statements": 80,
      "branches": 75,
      "functions": 80,
      "lines": 80
    }
  }
}
```

---

## 🔒 Quality Gates

### ESLint

```bash
npm install --save-dev eslint @typescript-eslint/parser
```

```js
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:jsx-a11y/recommended'
  ],
  rules: {
    'react/prop-types': 'off', // Using TypeScript
    'jsx-a11y/anchor-is-valid': 'error'
  }
};
```

### TypeScript

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true
  }
}
```

### Prettier

```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "printWidth": 100
}
```

---

## 🤖 Pre-commit Hooks

```bash
npm install --save-dev husky lint-staged
```

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write",
      "jest --findRelatedTests"
    ]
  }
}
```

```bash
# Setup husky
npx husky-init
echo "npx lint-staged" > .husky/pre-commit
```

---

## 📊 Performance Testing

```tsx
import { render } from '@testing-library/react';
import { performance } from 'perf_hooks';

it('renders within performance budget', () => {
  const start = performance.now();
  
  render(<ExpensiveComponent data={largeDataset} />);
  
  const end = performance.now();
  const renderTime = end - start;
  
  expect(renderTime).toBeLessThan(16); // 60fps = 16ms
});
```

---

## 📚 Points clés

1. **Pyramide** : 80% unit, 15% integration, 5% E2E
2. **RTL** : Test behavior, not implementation
3. **Accessibility** : jest-axe automatique
4. **Coverage** : > 80% target
5. **ESLint** : Enforce code standards
6. **Prettier** : Format auto
7. **Husky** : Pre-commit hooks
8. **CI/CD** : Tests automatiques

---

*Chapitre 05.6 | Tests et Qualité*

