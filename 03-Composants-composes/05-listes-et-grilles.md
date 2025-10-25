# Listes et Grilles

## 🎯 Objectifs

- ✓ Créer des layouts de liste et grille performants
- ✓ Implémenter le lazy loading et la pagination infinie
- ✓ Gérer les états (loading, empty, error)
- ✓ Optimiser pour mobile

---

## 📐 Layouts

### 1. Liste verticale

```tsx
<div className="list">
  {items.map(item => (
    <ListItem key={item.id}>
      <ListItemIcon>
        <Avatar src={item.avatar} />
      </ListItemIcon>
      <ListItemContent>
        <ListItemTitle>{item.title}</ListItemTitle>
        <ListItemDescription>{item.description}</ListItemDescription>
      </ListItemContent>
      <ListItemAction>
        <Button size="sm">Voir</Button>
      </ListItemAction>
    </ListItem>
  ))}
</div>
```

### 2. Grille responsive

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  {items.map(item => (
    <Card key={item.id}>
      <CardImage src={item.image} />
      <CardTitle>{item.title}</CardTitle>
      <CardContent>{item.description}</CardContent>
    </Card>
  ))}
</div>
```

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: var(--spacing-6);
}
```

---

## ∞ Pagination Infinie

```tsx
import { useInfiniteQuery } from '@tanstack/react-query';
import { useInView } from 'react-intersection-observer';

export function InfiniteList() {
  const { ref, inView } = useInView();
  
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage
  } = useInfiniteQuery({
    queryKey: ['items'],
    queryFn: ({ pageParam = 0 }) => fetchItems(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextCursor
  });
  
  useEffect(() => {
    if (inView && hasNextPage) {
      fetchNextPage();
    }
  }, [inView, hasNextPage, fetchNextPage]);
  
  return (
    <div>
      {data?.pages.map(page => (
        page.items.map(item => (
          <ItemCard key={item.id} item={item} />
        ))
      ))}
      
      <div ref={ref}>
        {isFetchingNextPage && <Spinner />}
      </div>
    </div>
  );
}
```

---

## 🎭 États

### Loading State

```tsx
{isLoading && (
  <div className="grid">
    {Array.from({ length: 12 }).map((_, i) => (
      <CardSkeleton key={i} />
    ))}
  </div>
)}
```

### Empty State

```tsx
{items.length === 0 && (
  <EmptyState
    icon={<Inbox />}
    title="Aucun résultat"
    description="Essayez de modifier vos filtres"
    action={
      <Button onClick={resetFilters}>
        Réinitialiser
      </Button>
    }
  />
)}
```

### Error State

```tsx
{error && (
  <ErrorState
    title="Erreur de chargement"
    description={error.message}
    action={
      <Button onClick={retry}>
        Réessayer
      </Button>
    }
  />
)}
```

---

## ⚡ Optimisation

### Virtualisation

```tsx
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={items.length}
  itemSize={80}
  width="100%"
>
  {({ index, style }) => (
    <div style={style}>
      <Item data={items[index]} />
    </div>
  )}
</FixedSizeList>
```

### Memoization

```tsx
const ItemCard = React.memo(({ item }: { item: Item }) => {
  return (
    <Card>
      <h3>{item.title}</h3>
      <p>{item.description}</p>
    </Card>
  );
});
```

---

## 📱 Mobile

### Gestures

```tsx
import { useSwipeable } from 'react-swipeable';

const handlers = useSwipeable({
  onSwipedLeft: () => handleDelete(item.id),
  onSwipedRight: () => handleArchive(item.id)
});

<ListItem {...handlers}>
  {/* Contenu */}
</ListItem>
```

---

## ♿ Accessibilité

```tsx
<ul role="list" aria-label="Liste de produits">
  {items.map(item => (
    <li role="listitem" key={item.id}>
      <a href={`/item/${item.id}`}>
        <h3>{item.title}</h3>
        <p>{item.description}</p>
      </a>
    </li>
  ))}
</ul>

<div
  role="status"
  aria-live="polite"
  aria-atomic="true"
  className="sr-only"
>
  {`${items.length} résultats affichés`}
</div>
```

---

## 📚 Points clés

1. **Grid** : auto-fill + minmax pour responsive
2. **Lazy loading** : Intersection Observer
3. **Virtualisation** : Pour listes 100+ items
4. **États** : Loading, empty, error
5. **Skeleton** : Pendant chargement
6. **Memoization** : React.memo sur items
7. **Mobile** : Swipe gestures optionnels
8. **ARIA** : role="list" et "listitem"

---

*Chapitre 03.5 | Listes et Grilles*

