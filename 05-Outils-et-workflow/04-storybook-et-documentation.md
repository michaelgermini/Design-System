# Storybook et Documentation

## 🎯 Objectifs

- ✓ Mettre en place Storybook
- ✓ Créer des stories pour vos composants
- ✓ Générer une documentation interactive
- ✓ Tester visuellement les composants

---

## 🚀 Installation

```bash
npx storybook@latest init
```

---

## 📝 Créer une Story

```tsx
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  parameters: {
    layout: 'centered'
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'ghost']
    },
    size: {
      control: 'select',
      options: ['sm', 'md', 'lg']
    }
  }
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Button'
  }
};

export const Secondary: Story = {
  args: {
    variant: 'secondary',
    children: 'Button'
  }
};

export const AllSizes: Story = {
  render: () => (
    <div className="flex items-center gap-4">
      <Button size="sm">Small</Button>
      <Button size="md">Medium</Button>
      <Button size="lg">Large</Button>
    </div>
  )
};

export const WithIcons: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button leftIcon={<Plus />}>Ajouter</Button>
      <Button rightIcon={<ArrowRight />}>Suivant</Button>
    </div>
  )
};
```

---

## 📚 Documentation automatique

```tsx
/**
 * Button component for user interactions
 * 
 * @example
 * ```tsx
 * <Button variant="primary">Click me</Button>
 * ```
 */
export interface ButtonProps {
  /**
   * Visual style variant
   * @default 'primary'
   */
  variant?: 'primary' | 'secondary' | 'ghost';
  
  /**
   * Size of the button
   * @default 'md'
   */
  size?: 'sm' | 'md' | 'lg';
  
  /**
   * Loading state
   * @default false
   */
  loading?: boolean;
  
  /**
   * Button content
   */
  children: React.ReactNode;
}
```

---

## 🎨 Addons utiles

```bash
# Accessibility
npm install @storybook/addon-a11y

# Interactions testing
npm install @storybook/addon-interactions

# Measure performance
npm install @storybook/addon-performance
```

Configuration `.storybook/main.ts`:
```ts
export default {
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-a11y',
    '@storybook/addon-interactions'
  ]
};
```

---

## 🎭 States et Interactions

```tsx
export const Interactive: Story = {
  args: {
    variant: 'primary',
    children: 'Click me'
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    const button = canvas.getByRole('button');
    
    await userEvent.click(button);
    await expect(button).toHaveClass('active');
  }
};
```

---

## 🌓 Dark Mode

```tsx
// .storybook/preview.tsx
export const globalTypes = {
  theme: {
    name: 'Theme',
    description: 'Global theme',
    defaultValue: 'light',
    toolbar: {
      icon: 'circlehollow',
      items: ['light', 'dark'],
      dynamicTitle: true
    }
  }
};

export const decorators = [
  (Story, context) => {
    const theme = context.globals.theme;
    
    return (
      <div data-theme={theme}>
        <Story />
      </div>
    );
  }
];
```

---

## 📊 Design Tokens in Storybook

```tsx
// ColorPalette.stories.tsx
export const Colors: Story = {
  render: () => (
    <div className="grid grid-cols-5 gap-4">
      {Object.entries(colors).map(([name, shades]) => (
        <div key={name}>
          <h3>{name}</h3>
          {Object.entries(shades).map(([shade, value]) => (
            <div key={shade} className="flex items-center gap-2">
              <div
                className="w-10 h-10 rounded"
                style={{ background: value }}
              />
              <span>{shade}: {value}</span>
            </div>
          ))}
        </div>
      ))}
    </div>
  )
};
```

---

## 🚀 Build et Deploy

```bash
# Build static
npm run build-storybook

# Deploy to Netlify/Vercel
# Output: storybook-static/
```

---

## 📚 Points clés

1. **Stories** : Un par variant/état
2. **Args** : Controls interactifs
3. **JSDoc** : Documentation auto
4. **Addons** : A11y, interactions
5. **Dark mode** : Global decorator
6. **Play function** : Tests d'interaction
7. **Deploy** : Static site généré
8. **Autodocs** : Tag 'autodocs'

---

*Chapitre 05.4 | Storybook et Documentation*

