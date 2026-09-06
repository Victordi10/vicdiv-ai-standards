# Frontend Standards

## Next.js

- App Router.
- Server Components por defecto.
- `"use client"` únicamente cuando sea necesario.
- Tailwind CSS en web.
- Zustand para estado global.
- TanStack Query para server state.
- Axios para HTTP cuando corresponda.

## Estructura orientativa

```text
src/
├── app/
├── components/
│   ├── ui/
│   ├── auth/
│   ├── users/
│   └── products/
├── stores/
├── providers/
├── hooks/
└── lib/
```

La estructura exacta puede variar por proyecto.

## Server Components

Preferir Server Components. Un Client Component debe existir por una razón concreta: interacción, hooks, Zustand, browser APIs, estado interactivo, etc.

## Server state

Usar TanStack Query para fetching, cache, invalidación y sincronización de datos remotos.

No convertir Zustand en cache de API.

## Axios

Centralizar configuración común de Axios cuando el proyecto lo requiera. Evitar múltiples clientes equivalentes.

## UI

Los componentes comunes pueden vivir en `components/ui/`. Los componentes específicos permanecen asociados a su dominio.
