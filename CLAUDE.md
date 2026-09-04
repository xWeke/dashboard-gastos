# Dashboard de Gastos Mensuales

Aplicación web interactiva para visualizar y gestionar gastos mensuales personales. Permite registrar, categorizar y analizar el historial de gastos mediante gráficos y tablas dinámicas.

## Stack Tecnológico

- **Frontend**: React 18 + TypeScript + Vite
- **Estilos**: Tailwind CSS
- **Gráficos**: Recharts
- **Estado global**: Zustand
- **Persistencia**: localStorage (MVP) → opcional migrar a backend
- **Validación de formularios**: React Hook Form + Zod
- **Fechas**: date-fns
- **Iconos**: Lucide React

## Estructura del Proyecto

```
Dashboard_Gastos/
├── public/
├── src/
│   ├── components/
│   │   ├── charts/          # Componentes de gráficos (BarChart, PieChart, LineChart)
│   │   ├── dashboard/       # Tarjetas de resumen, KPIs
│   │   ├── expenses/        # Formulario, tabla y filtros de gastos
│   │   └── ui/              # Componentes base reutilizables (Button, Modal, Input)
│   ├── store/
│   │   └── useExpenseStore.ts   # Store Zustand con gastos y categorías
│   ├── types/
│   │   └── expense.ts           # Tipos: Expense, Category, MonthlyReport
│   ├── utils/
│   │   ├── calculations.ts      # Totales, promedios, comparativas
│   │   └── formatters.ts        # Formateo de moneda y fechas
│   ├── hooks/
│   │   └── useExpenses.ts       # Hook con lógica derivada del store
│   ├── App.tsx
│   └── main.tsx
├── CLAUDE.md
├── package.json
├── tsconfig.json
├── vite.config.ts
└── tailwind.config.ts
```

## Modelo de Datos

```typescript
// src/types/expense.ts

type Category =
  | 'alimentacion'
  | 'transporte'
  | 'vivienda'
  | 'salud'
  | 'entretenimiento'
  | 'ropa'
  | 'educacion'
  | 'otros';

interface Expense {
  id: string;           // uuid
  description: string;
  amount: number;       // siempre positivo, en moneda local
  category: Category;
  date: string;         // ISO 8601: "YYYY-MM-DD"
  note?: string;
}

interface MonthlyReport {
  month: string;        // "YYYY-MM"
  total: number;
  byCategory: Record<Category, number>;
  expenses: Expense[];
}
```

## Funcionalidades del Dashboard

### Vista principal
- **Resumen del mes actual**: total gastado, número de transacciones, categoría con más gasto
- **Comparativa**: diferencia porcentual respecto al mes anterior
- **Gráfico de barras**: gastos por categoría del mes seleccionado
- **Gráfico de líneas**: evolución de gastos de los últimos 6 meses
- **Gráfico de torta**: distribución porcentual por categoría

### Gestión de gastos
- Formulario para agregar/editar gastos (descripción, monto, categoría, fecha)
- Tabla con paginación, ordenamiento y filtros (por mes, categoría, rango de monto)
- Eliminar gasto con confirmación
- Importar/exportar CSV

### Navegación
- Selector de mes para ver histórico
- Barra lateral con menú: Dashboard, Gastos, Categorías, Reportes

## Convenciones de Código

- **Componentes**: PascalCase, un componente por archivo
- **Hooks**: prefijo `use`, en `/hooks`
- **Store**: un store central en `useExpenseStore.ts`; no duplicar estado derivado en otros stores
- **Tipos**: definir en `/types`, importar donde se necesiten; nunca usar `any`
- **Estilos**: solo clases Tailwind, sin CSS inline ni archivos `.css` (excepto `index.css` base)
- **Funciones puras**: lógica de cálculo siempre en `/utils`, nunca dentro de componentes
- **IDs**: generar con `crypto.randomUUID()`
- **Moneda**: almacenar en número (`number`), formatear solo en presentación con `formatters.ts`

## Comandos de Desarrollo

```bash
npm install          # Instalar dependencias
npm run dev          # Servidor de desarrollo en http://localhost:5173
npm run build        # Build de producción
npm run preview      # Preview del build
npm run typecheck    # Verificar tipos TypeScript sin compilar
npm run lint         # ESLint
```

## Persistencia con localStorage

El store Zustand persiste automáticamente en `localStorage` bajo la clave `expenses-store` usando el middleware `persist`. No se requiere backend en el MVP. Si se migra a API REST:
- Agregar `/src/api/` con funciones fetch tipadas
- Mover la lógica de persistencia fuera del store

## Criterios de Calidad

- Sin errores de TypeScript en `npm run typecheck`
- Sin warnings de ESLint en `npm run lint`
- Todos los montos siempre mayores a cero (validar con Zod en el formulario)
- Las fechas deben pertenecer al rango 2020-01-01 a hoy (validar al registrar)
- Los gráficos deben ser responsivos y funcionar en móvil
- El dashboard debe mostrar estado vacío si no hay gastos registrados

## Internacionalización

- Idioma: español
- Moneda: EUR (euros) por defecto, símbolo `€`
- Formato de fecha visible: `dd/MM/yyyy`
- Formato de moneda: `1.234,56 €` (punto para miles, coma para decimales)
