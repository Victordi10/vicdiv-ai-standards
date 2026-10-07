# Skill: Setup Project

## Objetivo

Inicializar un proyecto nuevo aplicando el marco de trabajo estándar: estructura, toolkit, theme y dependencias base.

## Procedimiento

1. Definir tipo de proyecto: web (Next.js), mobile (Expo) o monorepo.
2. Instalar base con el package manager del proyecto (`pnpm` en web, `npm` en mobile).
3. Web con Next.js 16 (Turbopack): scripts `next dev` / `next build` sin flag; no añadir clave `webpack`.
4. Dependencias estándar según plataforma:
   - web: `tailwindcss`, `zustand`, `@tanstack/react-query`, `axios`, `vicdev-ui-lib` (+ peers `lucide-react`, `framer-motion`, `primereact`/`primeicons` si se usan `DataTable`), `next-themes` (opcional);
   - mobile: `nativewind` (opcional), `zustand`, `@tanstack/react-query`, `axios`, `@react-native-async-storage/async-storage`.
5. Crear estructura base (`docs/architecture.md`):
   - web: `src/app/` (páginas y `api/`), `src/components/<dominio>/`, `src/components/ui/`, `src/hooks/`, `src/lib/`, `src/providers/`, `src/stores/`, `src/constants/theme/`;
   - mobile: `src/screens/<dominio>/`, `src/stack/<dominio>/`, `src/components/<dominio>/`, `src/navigation/`, `src/stores/`, `src/providers/`, `src/hooks/`, `src/lib/`, `src/theme/`.
6. Copiar el toolkit desde `docs/toolkit.md`:
   - `logger`;
   - cliente `api` (axios);
   - `successResponse` / `errorResponse` (solo Next.js);
   - `formatCurrency`.
7. Crear store de theme (Zustand) siguiendo `docs/theming.md` y `docs/state-management.md`:
   - `store.ts`, `actions.ts`, `types.ts`, `storage.ts` (persistencia);
   - `themePreference: 'light' | 'dark' | 'system'`.
8. Crear `ThemeProvider` + hook `useTheme()`; en web sincronizar `data-theme` en el DOM.
9. Instalar y montar `vicdev-ui-lib` (web) siguiendo `docs/ui-library.md`:
   - registry (`pnpm add vicdev-ui-lib`) o ruta local (`pnpm add file:../vicdev-ui-react-lib`);
   - `VicdevUIProvider` en `providers/` con `config.theme = createTheme({ brand, light, dark })`;
   - alternar `.dark` según `isDark` del store;
   - `next.config.ts` con `transpilePackages: ['vicdev-ui-lib']`.
10. Configurar Tailwind con los tokens del theme:
    - web con `vicdev-ui-lib` (Tailwind v4): `@import "tailwindcss"` + `@import "vicdev-ui-lib/tailwind.css"`;
    - web sin librería (Tailwind v4): variables CSS en `globals.css` + `@theme`;
    - mobile (NativeWind): escalas importadas de la fuente compartida, `darkMode: ["class"]`.
11. Configurar `app.json` mobile con `"userInterfaceStyle": "automatic"`.
12. Conectar Root Layout y providers centralizados (Query, Theme, VicdevUI).
13. Añadir `AGENTS.md` local si el proyecto tiene reglas específicas.
14. Verificar cargo-check / typecheck / lint.

## No hacer

- Crear carpetas vacías por plantilla.
- Crear `features/`, `src/<dominio>/` al lado de `app/` en Next.js, `src/api/` aparte de `app/api/`, `store/` en singular o providers dentro de `components/`.
- Introducir librerías que no se usan.
- Reimplementar componentes que ya existen en `vicdev-ui-lib`.
- Duplicar paletas de colores en varios archivos.
- Hardcodear colores en componentes.
- Crear múltiples clientes axios equivalentes.