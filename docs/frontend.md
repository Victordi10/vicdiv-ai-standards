# Frontend Standards

## Next.js

- Next.js **16** (mínimo `16.3`), App Router, React 19.
- **Turbopack** es el bundler de `next dev` y `next build`. Los scripts van sin flag: `next dev` y `next build` (Turbopack es el default desde Next 16).
- No añadir una clave `webpack` en `next.config.ts` para resolver la librería; si se necesita resolución custom, va en `turbopack`.
- Server Components por defecto.
- `"use client"` únicamente cuando sea necesario.
- Tailwind CSS en web (por defecto).
- Zustand para estado global.
- TanStack Query para server state.
- Axios para HTTP cuando corresponda.
- `vicdev-ui-lib` para componentes UI reutilizables (ver `docs/ui-library.md`).
- Package manager: `pnpm`.

## Estructura

Esta es la única estructura web. `app/` tiene páginas, layouts y `api/`. La UI va en `components/<dominio>/`. No crear `src/<dominio>/` al lado de `app/`: esa forma es de Nest, no de Next.

```text
src/
├── app/
│   ├── (auth)/login/page.tsx
│   ├── (admin)/admin/ordenes/page.tsx
│   ├── layout.tsx
│   └── api/clients/
├── components/
│   ├── ui/
│   ├── auth/
│   ├── catalog/
│   └── layout/
├── hooks/
├── lib/
├── providers/
├── stores/
│   ├── auth/
│   └── theme/
└── constants/theme/theme.ts
```

- `page.tsx` importa desde `components/<dominio>/`.
- No crear UI en `app/` (`_components`).
- No crear `features/` ni dominios en la raíz del repo.
- Providers en `providers/`. Stores en `stores/`.
- Hooks de datos en `hooks/`. Cliente HTTP en `lib/`.
- El módulo de API vive en `app/api/<dominio>/` (`docs/backend.md`).

## Server Components

Preferir Server Components. Un Client Component debe existir por una razón concreta: interacción, hooks, Zustand, browser APIs, estado interactivo, etc.

## Server state

Usar TanStack Query para fetching, cache, invalidación y sincronización de datos remotos.

No convertir Zustand en cache de API.

## Axios

Centralizar configuración común de Axios cuando el proyecto lo requiera. Evitar múltiples clientes equivalentes.

Usar el cliente canónico de `docs/toolkit.md` (baseURL desde el origin actual + interceptor de token).

## Logging

Todo log pasa por `logger` (`docs/toolkit.md`). No usar `console` directo. `debug` en desarrollo; la lógica de negocio usa niveles según gravedad.

## Theming

El tema es centralizado (`docs/theming.md`): fuente de verdad en `theme.ts`, estado en Zustand + Provider + storage, tokens mapeados a Tailwind. Los componentes usan tokens, no colores hardcodeados.

## UI

`components/ui/` es UI genérica que no está en `vicdev-ui-lib`. La UI de un dominio vive en `components/<dominio>/`.
