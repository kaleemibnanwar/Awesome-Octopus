# AI Rules & Project Guidelines

## Tech Stack
- **Core Framework**: React (TypeScript) powered by Vite for modern, fast single-page application development.
- **Routing**: React Router for client-side routing, with central route definitions maintained in `src/App.tsx`.
- **Styling**: Tailwind CSS for responsive, utility-first styling and dynamic theming.
- **UI Component Library**: shadcn/ui (built on accessible Radix UI primitives) for reusable, accessible UI elements.
- **Icons**: Lucide React (`lucide-react`) for standard and consistent iconography.
- **Data Fetching & Server State**: TanStack React Query (`@tanstack/react-query`) for query caching, synchronization, and data management.
- **Form Management & Validation**: React Hook Form combined with Zod for declarative, type-safe schema validation.
- **Notifications & Feedback**: Sonner / Toast components for toasts and user feedback alerts.

---

## Library & Component Usage Rules

### 1. UI Components & Design System
- **shadcn/ui (`src/components/ui/`)**: Use existing shadcn/ui components as the primary building blocks. Do not directly alter base primitives; compose them into custom domain components instead.
- **Radix UI Primitives**: Use for headless accessible patterns (Dialogs, Dropdowns, Tooltips, Popovers, Tabs, Accordions).
- **Tailwind CSS**: Always use Tailwind utility classes for layout, flexbox/grid, spacing, sizing, colors, and responsive modifiers. Avoid writing standalone custom CSS classes.
- **Lucide Icons**: Exclusively use `lucide-react` icons. Maintain consistent sizing (e.g., `h-4 w-4` or `h-5 w-5`) and stroke widths.

### 2. Project Architecture & File Organization
- **Source Root (`src/`)**: All application code lives inside `src/`.
- **Pages (`src/pages/`)**: Route-level screen components belong in `src/pages/`. The primary default landing view is `src/pages/Index.tsx`.
- **Components (`src/components/`)**: Custom reusable business logic and layout components belong in `src/components/`.
- **Routes (`src/App.tsx`)**: Keep all application route declarations centralized in `src/App.tsx`.
- **Custom Hooks (`src/hooks/`)**: Extract reusable reactive logic, browser event listeners, and data-fetching helpers into dedicated hook files.
- **Utilities (`src/lib/` or `src/utils/`)**: General helper functions, formatters, and utility functions (such as `cn` for class merging).
- **Types (`src/types/`)**: Shared TypeScript interfaces, types, and model definitions.

### 3. State Management & Data Fetching
- **Client State**: Use standard React hooks (`useState`, `useReducer`, `useContext`) for local UI state (modals, active tabs, toggles).
- **Server / Async Data**: Use `@tanstack/react-query` for API requests, caching, refetching, and mutation handling.

### 4. Forms & Validation
- **React Hook Form**: Use `useForm` for managing form state, dirty checking, and submission handling.
- **Zod**: Define strict validation schemas (`z.object({...})`) and infer TypeScript types directly from schemas with `z.infer<typeof schema>`.

### 5. Implementation Standards
- **Complete & Functional Code**: Write fully implemented components without placeholders or TODO comments.
- **Discoverability**: Ensure newly created components are imported and integrated into their respective pages or the main index page (`src/pages/Index.tsx`) so they are immediately visible.
- **Type Safety**: Strictly type component props, function parameters, and state; avoid using `any`.
