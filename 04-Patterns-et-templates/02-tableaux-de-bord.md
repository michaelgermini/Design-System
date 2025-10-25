# Tableaux de Bord (Dashboards)

## 🎯 Objectifs

- ✓ Créer des dashboards informatifs et actionnables
- ✓ Organiser les métriques et KPIs
- ✓ Implémenter les graphiques et visualisations
- ✓ Optimiser pour différents rôles utilisateurs

---

## 📐 Anatomie d'un Dashboard

```
┌─────────────────────────────────────────────┐
│ [Logo] Dashboard        [Notif] [Avatar]    │ ← Header
├─────────────────────────────────────────────┤
│ Bienvenue, John! 👋                         │ ← Greeting
├─────────────────────────────────────────────┤
│ [KPI 1]  [KPI 2]  [KPI 3]  [KPI 4]         │ ← Key Metrics
├─────────────────────────────────────────────┤
│ ┌─────────────────┐ ┌───────────────────┐   │
│ │  Chart 1        │ │  Chart 2          │   │ ← Primary Charts
│ │  [Line graph]   │ │  [Bar chart]      │   │
│ └─────────────────┘ └───────────────────┘   │
├─────────────────────────────────────────────┤
│ ┌─────────────────┐ ┌───────────────────┐   │
│ │  Recent Items   │ │  Quick Actions    │   │ ← Secondary Content
│ │  • Item 1       │ │  [+ New]          │   │
│ │  • Item 2       │ │  [Import]         │   │
│ └─────────────────┘ └───────────────────┘   │
└─────────────────────────────────────────────┘
```

---

## 📊 KPI Cards

```tsx
interface KPICardProps {
  title: string;
  value: string | number;
  change?: number;
  changeType?: 'increase' | 'decrease';
  icon?: React.ReactNode;
  trend?: number[];
}

export function KPICard({
  title,
  value,
  change,
  changeType,
  icon,
  trend
}: KPICardProps) {
  const isPositive = changeType === 'increase';
  
  return (
    <Card>
      <CardContent className="p-6">
        <div className="flex items-center justify-between">
          <div>
            <p className="text-sm text-muted-foreground">{title}</p>
            <h3 className="text-2xl font-bold mt-2">{value}</h3>
            
            {change !== undefined && (
              <div className={`flex items-center mt-2 text-sm ${
                isPositive ? 'text-success' : 'text-error'
              }`}>
                {isPositive ? <TrendingUp /> : <TrendingDown />}
                <span className="ml-1">{change}%</span>
                <span className="ml-2 text-muted-foreground">
                  vs mois dernier
                </span>
              </div>
            )}
          </div>
          
          <div className="flex flex-col items-end gap-2">
            {icon && (
              <div className="p-3 rounded-lg bg-primary/10 text-primary">
                {icon}
              </div>
            )}
            
            {trend && (
              <Sparkline data={trend} width={100} height={30} />
            )}
          </div>
        </div>
      </CardContent>
    </Card>
  );
}

// Usage
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
  <KPICard
    title="Revenus"
    value="45 231 €"
    change={12.5}
    changeType="increase"
    icon={<DollarSign />}
    trend={[10, 15, 12, 18, 20, 25, 30]}
  />
  
  <KPICard
    title="Utilisateurs actifs"
    value="2 350"
    change={-5.2}
    changeType="decrease"
    icon={<Users />}
  />
  
  <KPICard
    title="Commandes"
    value="156"
    change={8.1}
    changeType="increase"
    icon={<ShoppingCart />}
  />
  
  <KPICard
    title="Taux de conversion"
    value="3.2%"
    change={0.5}
    changeType="increase"
    icon={<TrendingUp />}
  />
</div>
```

---

## 📈 Graphiques

### Line Chart (Recharts)

```tsx
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from 'recharts';

const data = [
  { month: 'Jan', value: 4000 },
  { month: 'Fév', value: 3000 },
  { month: 'Mar', value: 5000 },
  { month: 'Avr', value: 4500 },
  { month: 'Mai', value: 6000 },
  { month: 'Juin', value: 5500 }
];

export function RevenueChart() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Revenus mensuels</CardTitle>
        <CardDescription>Performance sur les 6 derniers mois</CardDescription>
      </CardHeader>
      <CardContent>
        <ResponsiveContainer width="100%" height={300}>
          <LineChart data={data}>
            <CartesianGrid strokeDasharray="3 3" />
            <XAxis dataKey="month" />
            <YAxis />
            <Tooltip />
            <Line
              type="monotone"
              dataKey="value"
              stroke="hsl(var(--primary))"
              strokeWidth={2}
            />
          </LineChart>
        </ResponsiveContainer>
      </CardContent>
    </Card>
  );
}
```

---

## 🎯 Filtres et Période

```tsx
export function DashboardFilters() {
  const [period, setPeriod] = useState<'7d' | '30d' | '90d' | '1y'>('30d');
  const [dateRange, setDateRange] = useState<DateRange | undefined>();
  
  return (
    <div className="flex items-center gap-4 mb-6">
      <Select value={period} onValueChange={setPeriod}>
        <SelectTrigger className="w-[180px]">
          <SelectValue />
        </SelectTrigger>
        <SelectContent>
          <SelectItem value="7d">7 derniers jours</SelectItem>
          <SelectItem value="30d">30 derniers jours</SelectItem>
          <SelectItem value="90d">90 derniers jours</SelectItem>
          <SelectItem value="1y">Cette année</SelectItem>
        </SelectContent>
      </Select>
      
      <DateRangePicker
        value={dateRange}
        onChange={setDateRange}
      />
      
      <Button
        variant="secondary"
        onClick={exportData}
        leftIcon={<Download />}
      >
        Exporter
      </Button>
    </div>
  );
}
```

---

## 📋 Recent Activity

```tsx
export function RecentActivity() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Activité récente</CardTitle>
      </CardHeader>
      <CardContent>
        <div className="space-y-4">
          {activities.map((activity) => (
            <div key={activity.id} className="flex items-start gap-4">
              <Avatar>
                <AvatarImage src={activity.user.avatar} />
                <AvatarFallback>{activity.user.initials}</AvatarFallback>
              </Avatar>
              
              <div className="flex-1">
                <p className="text-sm">
                  <strong>{activity.user.name}</strong>{' '}
                  {activity.action}
                </p>
                <p className="text-xs text-muted-foreground">
                  {formatRelativeTime(activity.timestamp)}
                </p>
              </div>
              
              <Badge variant={activity.status === 'success' ? 'success' : 'neutral'}>
                {activity.status}
              </Badge>
            </div>
          ))}
        </div>
        
        <Button variant="ghost" className="w-full mt-4">
          Voir toute l'activité
        </Button>
      </CardContent>
    </Card>
  );
}
```

---

## 🎨 Layouts Responsifs

### Desktop (Grid 3 colonnes)

```tsx
<div className="grid grid-cols-12 gap-6">
  {/* KPIs full width */}
  <div className="col-span-12">
    <KPICards />
  </div>
  
  {/* Main chart 8 cols */}
  <div className="col-span-8">
    <RevenueChart />
  </div>
  
  {/* Sidebar 4 cols */}
  <div className="col-span-4">
    <QuickActions />
  </div>
  
  {/* Secondary content */}
  <div className="col-span-6">
    <RecentActivity />
  </div>
  
  <div className="col-span-6">
    <TopProducts />
  </div>
</div>
```

### Mobile (Stack vertical)

```tsx
<div className="space-y-6">
  <KPICards />
  <RevenueChart />
  <QuickActions />
  <RecentActivity />
  <TopProducts />
</div>
```

---

## ⚡ Performance

### Lazy Loading Charts

```tsx
const RevenueChart = lazy(() => import('./RevenueChart'));

<Suspense fallback={<ChartSkeleton />}>
  <RevenueChart data={data} />
</Suspense>
```

### Data Fetching

```tsx
export function Dashboard() {
  const { data: metrics } = useQuery({
    queryKey: ['dashboard-metrics'],
    queryFn: fetchMetrics,
    refetchInterval: 60000 // Refresh every minute
  });
  
  const { data: chartData } = useQuery({
    queryKey: ['dashboard-chart', period],
    queryFn: () => fetchChartData(period),
    staleTime: 300000 // 5 minutes
  });
  
  return (
    <div>
      {/* Dashboard content */}
    </div>
  );
}
```

---

## 🎭 États

### Loading

```tsx
{isLoading && (
  <div className="grid grid-cols-4 gap-6">
    <Skeleton className="h-32" />
    <Skeleton className="h-32" />
    <Skeleton className="h-32" />
    <Skeleton className="h-32" />
  </div>
)}
```

### Empty State

```tsx
{metrics.totalUsers === 0 && (
  <EmptyState
    icon={<BarChart />}
    title="Pas encore de données"
    description="Les métriques apparaîtront dès que vous aurez des utilisateurs actifs"
  />
)}
```

---

## 🎨 Personnalisation

```tsx
export function DashboardSettings() {
  const [widgets, setWidgets] = useState([
    { id: 'revenue', enabled: true, order: 1 },
    { id: 'users', enabled: true, order: 2 },
    { id: 'orders', enabled: false, order: 3 }
  ]);
  
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button variant="ghost" size="sm">
          <Settings /> Personnaliser
        </Button>
      </DialogTrigger>
      
      <DialogContent>
        <DialogTitle>Widgets du dashboard</DialogTitle>
        
        <DragDropContext onDragEnd={handleDragEnd}>
          <Droppable droppableId="widgets">
            {(provided) => (
              <div {...provided.droppableProps} ref={provided.innerRef}>
                {widgets.map((widget, index) => (
                  <Draggable key={widget.id} draggableId={widget.id} index={index}>
                    {(provided) => (
                      <div
                        ref={provided.innerRef}
                        {...provided.draggableProps}
                        {...provided.dragHandleProps}
                        className="flex items-center justify-between p-4 border rounded mb-2"
                      >
                        <div className="flex items-center gap-3">
                          <GripVertical className="text-muted-foreground" />
                          <span>{widget.id}</span>
                        </div>
                        
                        <Switch
                          checked={widget.enabled}
                          onCheckedChange={(checked) =>
                            updateWidget(widget.id, { enabled: checked })
                          }
                        />
                      </div>
                    )}
                  </Draggable>
                ))}
                {provided.placeholder}
              </div>
            )}
          </Droppable>
        </DragDropContext>
      </DialogContent>
    </Dialog>
  );
}
```

---

## 📚 Points clés

1. **KPIs en premier** : Métriques clés visibles immédiatement
2. **Hiérarchie claire** : Primary > Secondary > Tertiary
3. **Filtres accessibles** : Période, date range en haut
4. **Graphiques lisibles** : Taille min 300px hauteur
5. **Loading states** : Skeletons pour chaque section
6. **Refresh** : Auto ou manuel, indicateur visible
7. **Mobile** : Stack vertical, graphiques responsifs
8. **Personnalisation** : Widgets réorganisables

---

*Chapitre 04.2 | Tableaux de Bord*

