# Tableaux de Données

## 🎯 Objectifs

- ✓ Créer des tableaux performants et accessibles
- ✓ Implémenter tri, filtrage, pagination
- ✓ Gérer les grandes quantités de données
- ✓ Optimiser la responsive

---

## 📐 Anatomie d'un Tableau

```
┌─────────────────────────────────────────────┐
│ [Recherche]          [Filtres] [Actions]    │ ← Toolbar
├─────────────────────────────────────────────┤
│ Nom ↑  │ Email      │ Statut │ Actions     │ ← Header (sortable)
├─────────────────────────────────────────────┤
│ Alice  │ a@mail.com │ Actif  │ [···]       │ ← Row
│ Bob    │ b@mail.com │ Inactif│ [···]       │
│ Carol  │ c@mail.com │ Actif  │ [···]       │
├─────────────────────────────────────────────┤
│ ← 1  2  3 →              10 résultats       │ ← Pagination
└─────────────────────────────────────────────┘
```

---

## 💻 Implémentation avec TanStack Table

```tsx
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  flexRender
} from '@tanstack/react-table';

const columns = [
  {
    accessorKey: 'name',
    header: 'Nom',
    cell: info => info.getValue()
  },
  {
    accessorKey: 'email',
    header: 'Email'
  },
  {
    accessorKey: 'status',
    header: 'Statut',
    cell: ({ row }) => (
      <Badge variant={row.original.status === 'active' ? 'success' : 'neutral'}>
        {row.original.status}
      </Badge>
    )
  },
  {
    id: 'actions',
    cell: ({ row }) => (
      <DropdownMenu>
        <DropdownMenuItem onClick={() => edit(row.original)}>
          Modifier
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => delete(row.original)}>
          Supprimer
        </DropdownMenuItem>
      </DropdownMenu>
    )
  }
];

export function DataTable({ data }: { data: User[] }) {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
    getPaginationRowModel: getPaginationRowModel()
  });
  
  return (
    <div>
      <table>
        <thead>
          {table.getHeaderGroups().map(headerGroup => (
            <tr key={headerGroup.id}>
              {headerGroup.headers.map(header => (
                <th key={header.id}>
                  <div
                    onClick={header.column.getToggleSortingHandler()}
                    style={{ cursor: header.column.getCanSort() ? 'pointer' : 'default' }}
                  >
                    {flexRender(
                      header.column.columnDef.header,
                      header.getContext()
                    )}
                    {{
                      asc: ' ↑',
                      desc: ' ↓'
                    }[header.column.getIsSorted() as string] ?? null}
                  </div>
                </th>
              ))}
            </tr>
          ))}
        </thead>
        <tbody>
          {table.getRowModel().rows.map(row => (
            <tr key={row.id}>
              {row.getVisibleCells().map(cell => (
                <td key={cell.id}>
                  {flexRender(cell.column.columnDef.cell, cell.getContext())}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
      
      <div className="pagination">
        <Button
          onClick={() => table.previousPage()}
          disabled={!table.getCanPreviousPage()}
        >
          Précédent
        </Button>
        <span>
          Page {table.getState().pagination.pageIndex + 1} sur {table.getPageCount()}
        </span>
        <Button
          onClick={() => table.nextPage()}
          disabled={!table.getCanNextPage()}
        >
          Suivant
        </Button>
      </div>
    </div>
  );
}
```

---

## 🎨 Styles

```css
table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--color-gray-50);
  border-bottom: 2px solid var(--color-border);
}

th {
  padding: var(--spacing-3) var(--spacing-4);
  text-align: left;
  font-weight: 600;
  color: var(--color-text-primary);
}

td {
  padding: var(--spacing-3) var(--spacing-4);
  border-bottom: 1px solid var(--color-border);
}

tr:hover {
  background: var(--color-gray-50);
}
```

---

## 📱 Responsive

### Approche 1 : Scroll horizontal

```tsx
<div className="table-container">
  <table>
    {/* Table */}
  </table>
</div>
```

```css
.table-container {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}
```

### Approche 2 : Cards sur mobile

```tsx
{isMobile ? (
  <div className="cards">
    {data.map(item => (
      <Card key={item.id}>
        <div><strong>Nom:</strong> {item.name}</div>
        <div><strong>Email:</strong> {item.email}</div>
      </Card>
    ))}
  </div>
) : (
  <table>...</table>
)}
```

---

## ♿ Accessibilité

```tsx
<table
  role="table"
  aria-label="Liste des utilisateurs"
  aria-rowcount={totalRows}
>
  <thead>
    <tr role="row">
      <th
        role="columnheader"
        aria-sort={sortDirection}
        onClick={handleSort}
      >
        Nom
        <span aria-hidden="true">↑</span>
      </th>
    </tr>
  </thead>
  <tbody>
    {rows.map((row, index) => (
      <tr
        role="row"
        aria-rowindex={index + 1}
      >
        <td role="cell">{row.name}</td>
      </tr>
    ))}
  </tbody>
</table>

<div
  role="status"
  aria-live="polite"
  className="sr-only"
>
  {`Affichage de ${start} à ${end} sur ${total} résultats`}
</div>
```

---

## ⚡ Performance

### Virtualisation pour grandes listes

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';

const rowVirtualizer = useVirtualizer({
  count: data.length,
  getScrollElement: () => parentRef.current,
  estimateSize: () => 50,
  overscan: 10
});

<div ref={parentRef} style={{ height: '600px', overflow: 'auto' }}>
  <div style={{ height: `${rowVirtualizer.getTotalSize()}px` }}>
    {rowVirtualizer.getVirtualItems().map(virtualRow => (
      <div
        key={virtualRow.index}
        style={{
          position: 'absolute',
          top: 0,
          left: 0,
          width: '100%',
          height: `${virtualRow.size}px`,
          transform: `translateY(${virtualRow.start}px)`
        }}
      >
        {/* Row content */}
      </div>
    ))}
  </div>
</div>
```

---

## 📚 Points clés

1. **Tri** : Colonnes cliquables avec indicateur visuel
2. **Pagination** : Obligatoire au-delà de 25-50 lignes
3. **Filtres** : Au-dessus du tableau
4. **Hover** : Background léger sur ligne
5. **Mobile** : Scroll horizontal ou cards
6. **Loading** : Skeleton durant chargement
7. **Empty state** : Message si pas de données
8. **Sélection** : Checkbox si actions multiples

---

*Chapitre 03.4 | Tableaux de Données*

