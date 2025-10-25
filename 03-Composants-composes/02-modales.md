# Modales et Dialogs

## 🎯 Objectifs

- ✓ Créer des modales accessibles avec focus trap
- ✓ Gérer l'overlay et les animations
- ✓ Implémenter différents types (dialog, drawer, sheet)
- ✓ Optimiser l'UX mobile

---

## 📐 Anatomie d'une Modale

```
┌─────────────────────────────────────────────┐
│                 Overlay (backdrop)          │
│                                             │
│    ┌─────────────────────────────────┐     │
│    │ [X]                             │     │ ← Close button
│    ├─────────────────────────────────┤     │
│    │ Title                           │     │ ← Header
│    ├─────────────────────────────────┤     │
│    │ Content area...                 │     │ ← Content
│    │                                 │     │
│    ├─────────────────────────────────┤     │
│    │ [Cancel]          [Confirm]     │     │ ← Footer
│    └─────────────────────────────────┘     │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 🎨 Types de Modales

### 1. Dialog (modale centrée)

```tsx
<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <DialogTrigger asChild>
    <Button>Ouvrir</Button>
  </DialogTrigger>
  
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Titre</DialogTitle>
      <DialogDescription>Description</DialogDescription>
    </DialogHeader>
    
    <div>Contenu</div>
    
    <DialogFooter>
      <Button variant="secondary" onClick={() => setIsOpen(false)}>
        Annuler
      </Button>
      <Button onClick={handleConfirm}>
        Confirmer
      </Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### 2. Drawer (panneau latéral)

```tsx
<Drawer side="right" open={isOpen} onOpenChange={setIsOpen}>
  <DrawerContent>
    {/* Contenu */}
  </DrawerContent>
</Drawer>
```

### 3. Sheet (panneau glissant)

```tsx
<Sheet>
  <SheetTrigger>Filtres</SheetTrigger>
  <SheetContent side="left">
    {/* Filtres */}
  </SheetContent>
</Sheet>
```

---

## 💻 Implémentation

```tsx
import * as Dialog from '@radix-ui/react-dialog';
import { X } from 'lucide-react';

export function Modal({
  open,
  onOpenChange,
  title,
  description,
  children,
  footer
}: ModalProps) {
  return (
    <Dialog.Root open={open} onOpenChange={onOpenChange}>
      <Dialog.Portal>
        <Dialog.Overlay className="modal-overlay" />
        
        <Dialog.Content className="modal-content">
          <Dialog.Title className="modal-title">
            {title}
          </Dialog.Title>
          
          {description && (
            <Dialog.Description className="modal-description">
              {description}
            </Dialog.Description>
          )}
          
          <div className="modal-body">
            {children}
          </div>
          
          {footer && (
            <div className="modal-footer">
              {footer}
            </div>
          )}
          
          <Dialog.Close asChild>
            <button className="modal-close" aria-label="Fermer">
              <X />
            </button>
          </Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

---

## 🎨 Styles & Animations

```css
/* Overlay */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  animation: fadeIn 150ms ease-out;
  z-index: 50;
}

/* Content */
.modal-content {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: var(--color-bg-primary);
  border-radius: var(--radius-lg);
  padding: var(--spacing-6);
  max-width: 500px;
  width: 90vw;
  max-height: 90vh;
  overflow-y: auto;
  animation: slideIn 200ms ease-out;
  z-index: 51;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translate(-50%, -48%) scale(0.96);
  }
  to {
    opacity: 1;
    transform: translate(-50%, -50%) scale(1);
  }
}

/* Close button */
.modal-close {
  position: absolute;
  top: var(--spacing-4);
  right: var(--spacing-4);
  width: 32px;
  height: 32px;
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  background: transparent;
  border: none;
  color: var(--color-text-secondary);
}

.modal-close:hover {
  background: var(--color-gray-100);
  color: var(--color-text-primary);
}
```

---

## ♿ Accessibilité

### Focus Trap

```tsx
import { useEffect, useRef } from 'react';

function useFocusTrap(isOpen: boolean) {
  const contentRef = useRef<HTMLDivElement>(null);
  
  useEffect(() => {
    if (!isOpen || !contentRef.current) return;
    
    const focusableElements = contentRef.current.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    
    const firstElement = focusableElements[0] as HTMLElement;
    const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;
    
    firstElement?.focus();
    
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return;
      
      if (e.shiftKey && document.activeElement === firstElement) {
        e.preventDefault();
        lastElement.focus();
      } else if (!e.shiftKey && document.activeElement === lastElement) {
        e.preventDefault();
        firstElement.focus();
      }
    };
    
    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, [isOpen]);
  
  return contentRef;
}
```

### Escape to Close

```tsx
useEffect(() => {
  const handleEscape = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
      onOpenChange(false);
    }
  };
  
  if (open) {
    document.addEventListener('keydown', handleEscape);
    return () => document.removeEventListener('keydown', handleEscape);
  }
}, [open, onOpenChange]);
```

### ARIA Attributes

```tsx
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Titre</h2>
  <p id="modal-description">Description</p>
</div>
```

---

## 📱 Mobile Optimization

### Full-screen sur mobile

```css
@media (max-width: 768px) {
  .modal-content {
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    transform: none;
    max-width: 100%;
    width: 100%;
    max-height: 100%;
    border-radius: 0;
  }
}
```

### Bottom Sheet

```tsx
<Sheet>
  <SheetContent side="bottom">
    {/* Contenu */}
  </SheetContent>
</Sheet>
```

---

## 🎯 Best Practices

### 1. Ne pas emboîter les modales

❌ **Mauvais**
```tsx
<Modal>
  <Modal>  {/* Modale dans modale */}
    ...
  </Modal>
</Modal>
```

✓ **Bon**
```tsx
// Fermer la première, ouvrir la seconde
setModal1Open(false);
setModal2Open(true);
```

### 2. Taille appropriée

```tsx
// Small (confirmation)
maxWidth: '400px'

// Medium (formulaire)
maxWidth: '600px'

// Large (contenu riche)
maxWidth: '900px'

// Full (image, vidéo)
maxWidth: '90vw'
```

### 3. Body scroll lock

```tsx
useEffect(() => {
  if (isOpen) {
    document.body.style.overflow = 'hidden';
    return () => {
      document.body.style.overflow = 'unset';
    };
  }
}, [isOpen]);
```

---

## ⚠️ Erreurs courantes

❌ **Pas de titre** → Utilisateur perdu
❌ **Pas de bouton close** → Piège de focus
❌ **Overlay non cliquable** → Frustration
❌ **Pas d'animation** → Transition brusque
❌ **Contenu coupé** → Pas de scroll

---

## 📚 Points clés

1. **Focus trap** : Obligatoire
2. **Escape** : Ferme la modale
3. **Overlay click** : Ferme (sauf confirmation)
4. **ARIA** : role="dialog", aria-modal="true"
5. **Animation** : Fade in + Scale
6. **Body scroll** : Bloqué quand modale ouverte
7. **Mobile** : Full-screen ou bottom sheet
8. **Titre** : Toujours présent

---

*Chapitre 03.2 | Modales*

