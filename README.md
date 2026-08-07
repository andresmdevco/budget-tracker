# 💰 Budget Tracker
Planificador de gastos construido con **React**, **TypeScript** y **Tailwind CSS**. Permite definir un presupuesto, registrar gastos por categoría, visualizar el porcentaje consumido con una barra de progreso circular y filtrar el listado de gastos por categoría. Toda la información persiste en el `localStorage` del navegador.


## 🌐 Demo
🔗 [https://budget-tracker-andresmdevco.vercel.app/](https://budget-tracker-andresmdevco.vercel.app/)


## 🛠️ Tecnologías Utilizadas
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Context-API](https://img.shields.io/badge/Context--Api-000000?style=for-the-badge&logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)

- **React 19**
- **Tailwind CSS 4**
Además:
- **react-circular-progressbar** — barra de progreso circular para visualizar el % gastado del presupuesto
- **react-swipeable-list** — acciones de "Actualizar" y "Eliminar" mediante swipe sobre cada gasto
- **react-date-picker** / **react-calendar** — selector de fecha para cada gasto
- **@headlessui/react** — modal accesible para el formulario de gastos
- **@heroicons/react** — íconos de la interfaz
- **uuid** — generación de identificadores únicos para cada gasto


## ✨ Características
- 💵 Definición de un presupuesto inicial.
- 📊 Barra de progreso circular que muestra el porcentaje gastado, cambiando a rojo al llegar al 100%.
- ➕ Registro de gastos mediante un modal, con nombre, cantidad, categoría y fecha.
- 🚫 Validación que impide registrar un gasto que exceda el presupuesto disponible.
- ✏️ Edición de gastos existentes deslizando (swipe) hacia la derecha, acción "Actualizar".
- 🗑️ Eliminación de gastos deslizando (swipe) hacia la izquierda, acción "Eliminar".
- 🔍 Filtro de gastos por categoría.
- 🔄 Botón para resetear la app por completo (presupuesto y gastos).
- 💾 Persistencia de presupuesto y gastos mediante `localStorage`.
- 💲 Formato de moneda con `Intl.NumberFormat` y de fecha con `Intl.DateTimeFormat`.
- 📱 Diseño responsive con Tailwind CSS.
- **Estado global con Context API + `useReducer`** — `BudgetProvider` expone `state`, `dispatch`, `totalExpenses` y `remainingBudget` a toda la app.
- **Custom hook `useBudget`** — encapsula el `useContext` y lanza un error si se usa fuera del `BudgetProvider`.


## 📂 Archivos principales
| Archivo | Descripción |
|---|---|
| `App.tsx` | Componente raíz. Persiste `budget` y `expenses` en `localStorage` y alterna entre `BudgetForm` (sin presupuesto) y `BudgetTracker` + `ExpenseList` (con presupuesto definido) |
| `context/BudgetContext.ts` | Define el contexto (`BudgetContext`) con `state`, `dispatch`, `totalExpenses` y `remainingBudget` |
| `context/BudgetProvider.tsx` | Inicializa `budgetReducer` con `useReducer` y calcula `totalExpenses` y `remainingBudget` con `useMemo` |
| `hooks/useBudget.ts` | Custom hook que consume `BudgetContext` y valida que se use dentro del `BudgetProvider` |
| `reducers/budget-reducer.ts` | Reducer con la lógica de estado: definir presupuesto, abrir/cerrar modal, agregar/editar/eliminar gastos, filtrar por categoría y resetear la app. Inicializa `budget` y `expenses` leyendo `localStorage` |
| `components/BudgetForm.tsx` | Formulario inicial para definir el presupuesto, dispara `add-budget` |
| `components/BudgetTracker.tsx` | Muestra la barra de progreso circular, el presupuesto, lo disponible y lo gastado; dispara `reset-app` |
| `components/ExpenseModal.tsx` | Modal (Headless UI) con botón flotante para abrir/cerrar el formulario de gastos, dispara `show-modal` / `close-modal` |
| `components/ExpenseForm.tsx` | Formulario para crear o editar un gasto (nombre, cantidad, categoría, fecha). Precarga los datos cuando `state.editingId` está definido y valida que el gasto no exceda el presupuesto disponible |
| `components/ExpenseList.tsx` | Renderiza el listado de gastos, aplicando el filtro por categoría (`state.currentCategory`) |
| `components/ExpenseDetail.tsx` | Ítem de gasto con swipe: "Actualizar" (dispara `get-expense-by-id`) y "Eliminar" (dispara `remove-expense`) |
| `components/FilterByCategory.tsx` | Select para filtrar el listado de gastos por categoría, dispara `add-filter-category` |
| `components/AmountDisplay.tsx` | Componente reutilizable que muestra un monto formateado como moneda, con etiqueta opcional |
| `components/ErrorMessage.tsx` | Componente reutilizable para mostrar mensajes de error de validación |
| `data/categories.ts` | Catálogo de categorías de gasto (`id`, `name`, `icon`) |
| `helpers/index.ts` | Funciones `formatCurrency` y `formatDate` para formatear moneda y fecha |
| `types/index.ts` | Tipos `Expense`, `DraftExpense` (`Omit<Expense, 'id'>`), `Category` y `Value` compartidos por la app |


## 🧠 Cómo funciona
1. Al iniciar, `budgetReducer` lee `budget` y `expenses` desde `localStorage` (o los inicializa en `0` y `[]`).
2. Mientras no haya un presupuesto definido (`budget === 0`), `App.tsx` muestra `BudgetForm`, que dispara `add-budget` al enviarse.
3. Con presupuesto definido, `App.tsx` muestra `BudgetTracker` (resumen y barra de progreso) junto con `FilterByCategory`, `ExpenseList` y `ExpenseModal`.
4. El botón flotante de `ExpenseModal` dispara `show-modal`, abriendo el `ExpenseForm` dentro de un modal de Headless UI.
5. Al registrar un gasto, `ExpenseForm` valida que ningún campo esté vacío y que el monto no exceda `remainingBudget`; luego dispara `add-expense` (o `update-expense` si `state.editingId` está definido).
6. `ExpenseList` filtra los gastos según `state.currentCategory` y renderiza un `ExpenseDetail` por cada uno.
7. En `ExpenseDetail`, deslizar (swipe) hacia la derecha dispara `get-expense-by-id` (precarga el formulario en modo edición y abre el modal); deslizar hacia la izquierda dispara `remove-expense` (elimina el gasto).
8. `BudgetProvider` recalcula `totalExpenses` y `remainingBudget` con `useMemo` cada vez que cambian los gastos, y `BudgetTracker` usa esos valores para actualizar la barra de progreso circular.
9. Un `useEffect` en `App.tsx` sincroniza `budget` y `expenses` con `localStorage` en cada cambio de estado.
10. El botón "Resetear App" dispara `reset-app`, devolviendo el estado a sus valores iniciales.


## 📚 Conceptos practicados
- Manejo de estado global con **Context API + `useReducer`**, evitando prop drilling.
- Modelado de acciones con discriminated unions (`BudgetActions`) para tipar `type` y `payload` de forma segura.
- Custom hook (`useBudget`) que encapsula `useContext` y valida su uso dentro del `Provider` correspondiente.
- Inicialización del estado del reducer leyendo datos desde `localStorage`.
- Sincronización de estado global con `localStorage` mediante `useEffect`.
- Estado derivado con `useMemo` (`totalExpenses`, `remainingBudget`, `percentage`, `isValidBudget`, `isEmpty`, `categoryInfo`).
- Definición de tipos reutilizables con `Omit` (`DraftExpense`) y uniones de tipos (`Value`).
- Formularios controlados y edición de registros reutilizando un mismo formulario (crear/actualizar).
- Validaciones de formulario (campos obligatorios, límite de presupuesto disponible).
- Integración de librerías de terceros: modal accesible (Headless UI), swipe actions (react-swipeable-list), selector de fecha (react-date-picker) y barra de progreso circular (react-circular-progressbar).
- Formato de moneda y fecha con `Intl.NumberFormat` e `Intl.DateTimeFormat`.
- Organización del proyecto por responsabilidades (`components`, `context`, `hooks`, `reducers`, `data`, `helpers`, `types`).


## 🚀 Cómo ejecutar el proyecto
1. Clonar el repositorio:
```bash
   git clone https://github.com/andresmdevco/budget-tracker.git
   cd budget-tracker
```
2. Instalar las dependencias:
```bash
   npm install
```
3. Ejecutar el proyecto en modo desarrollo:
```bash
   npm run dev
```
4. Abrir [http://localhost:5173](http://localhost:5173) en el navegador.
