# Skill: Setup Project

## Objetivo

Inicializar un proyecto nuevo aplicando el marco de trabajo estándar: estructura, toolkit, theme y dependencias base.

## Procedimiento

1. Definir tipo de proyecto: web (Next.js), mobile (Expo) o monorepo.
2. Instalar base con el package manager del proyecto (`pnpm` en web, `npm` en mobile).
3. Dependencias estándar según plataforma:
   - web: `tailwindcss`, `zustand`, `@tanstack/react-query`, `axios`, `next-themes` (opcional);
   - mobile: `nativewind` (opcional), `zustand`, `@tanstack/react-query`, `axios`, `@react-native-async-storage/async-storage`.
4. Crear estructura base:
   - web: `src/` con `app/`, `components/ui/`, `stores/`, `providers/`, `hooks/`, `lib/`;
   - mobile: `src/` con `screens/` por dominio, `components/`, `stores/`, `providers/`, `hooks/`, `lib/`.
5. Copiar el toolkit desde `docs/toolkit.md`:
   - `logger`;
   - cliente `api` (axios);
   - `successResponse` / `errorResponse` (solo Next.js);
   - `formatCurrency`.
6. Crear store de theme (Zustand) siguiendo `docs/theming.md` y `docs/state-management.md`:
   - `store.ts`, `actions.ts`, `types.ts`, `storage.ts` (persistencia);
   - `themePreference: 'light' | 'dark' | 'system'`.
7. Crear `ThemeProvider` + hook `useTheme()`; en web sincronizar `data-theme` en el DOM.
8. Configurar Tailwind con los tokens del theme:
   - web (Tailwind v4): variables CSS en `globals.css` + `@theme`;
   - mobile (NativeWind): escalas importadas de la fuente compartida, `darkMode: ["class"]`.
9. Configurar `app.json` mobile con `"userInterfaceStyle": "automatic"`.
10. Conectar Root Layout y providers centralizados (Query, Theme).
11. Añadir `AGENTS.md` local si el proyecto tiene reglas específicas.
12. Verificar cargo-check / typecheck / lint.

## No hacer

- Crear carpetas vacías por plantilla.
- Introducir librerías que no se usan.
- Duplicar paletas de colores en varios archivos.
- Hardcodear colores en componentes.
- Crear múltiples clientes axios equivalentes.