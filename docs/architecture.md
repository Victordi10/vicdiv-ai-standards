# Architecture Standards

## 1. Modelo arquitectónico

La organización es **modular por dominio**, con capas internas solo cuando aportan una responsabilidad real.

No es Clean Architecture ni Hexagonal Architecture. No existe una carpeta `features/`.

## 2. Dónde vive cada cosa

El dominio se organiza dentro de carpetas fijas. No se crean módulos sueltos en la raíz del repo.

### Web (Next.js)

`app/` solo tiene páginas, layouts y el módulo de API. La UI vive en `components/`, ordenada por dominio.

```text
src/
├── app/
│   ├── (auth)/login/page.tsx
│   ├── (admin)/admin/ordenes/page.tsx
│   ├── layout.tsx
│   └── api/clients/
│       ├── route.ts
│       ├── client.controller.ts
│       ├── client.service.ts
│       ├── client.repository.ts
│       ├── dto/
│       ├── entities/
│       └── interfaces/
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

- `page.tsx` importa la UI desde `components/<dominio>/`.
- La UI de un dominio no vive en `app/` (`_components` de página).
- Los providers viven en `providers/`.
- Los stores viven en `stores/` (plural).
- Los hooks de datos viven en `hooks/`.
- El cliente HTTP del navegador vive en `lib/`.

### Backend Next

El equivalente de un módulo Nest (`src/client/`) es `src/app/api/clients/`. El detalle de capas está en `docs/backend.md`.

### NestJS

Cuando el backend es un servicio aparte (web, admin y mobile), cada dominio es una carpeta en `src/`:

```text
src/
├── client/
│   ├── client.module.ts
│   ├── client.controller.ts
│   ├── client.service.ts
│   ├── dto/
│   ├── entities/
│   └── interfaces/
└── common/
```

GraphQL solo en ese backend, con `resolver` si el módulo ya lo usa. Ahí no hay pantallas.

### Mobile (Expo)

```text
src/
├── screens/<dominio>/
├── stack/<dominio>/
├── components/<dominio>/
├── navigation/
├── apiconsumer/graphql/domains/
├── stores/<dominio>/
├── providers/
├── hooks/
├── lib/
└── theme/
```

Package manager: `pnpm` en web, `npm` en mobile.

## 3. Estructuras que no se usan

En un proyecto Next.js, el árbol de Nest (`src/<dominio>/` al lado de `app/`) no se usa. Esto es un error:

```text
src/
├── app/
├── client/              # no: el módulo va en src/app/api/clients/
├── brand-session/       # no
├── onboarding/ui/       # no: la UI va en src/components/onboarding/
├── agent/               # no: va en src/app/api/agent/
└── shop/                # no: va en src/app/api/shop/
```

`src/<dominio>/` solo existe en un backend NestJS. En Next, controller, service, repository, `dto/`, `entities/` e `interfaces/` viven en `src/app/api/<dominio>/`. La UI vive en `src/components/<dominio>/`. Los hooks viven en `src/hooks/`. El cliente HTTP del navegador vive en `src/lib/`.

Tampoco se usa:

- `features/` como lugar del front o del back.
- `src/api/` aparte de `src/app/api/`.
- UI dentro de `app/` (`_components`).
- Providers dentro de `components/`.
- Carpeta `store/` en singular.
- Hooks de datos o cliente HTTP dentro del módulo de API.

## 4. Capas

Backend:

```text
route.ts o controller Nest
↓
DTO / validation
↓
service
↓
repository
↓
model
↓
database
```

Frontend:

```text
page.tsx o screen
↓
components/<dominio>
↓
hooks (TanStack Query) o stores (Zustand)
↓
lib (cliente HTTP)
↓
API
```

No todos los dominios requieren todas las capas. `repository.ts` solo existe si hay persistencia.

## 5. Arquitecturas soportadas

### Monolito

Todo puede vivir en una aplicación Next.js. El front sigue en `components/` y el back en `app/api/<dominio>/`.

### Monolito modular

Los mismos árboles. Los límites son las carpetas de dominio, no un paquete nuevo por cada caso.

### Monorepo

```text
apps/
packages/
```

Cada app conserva el árbol de su tipo (web, mobile o Nest). `packages/` solo tiene código realmente compartido. Cada app puede tener un `AGENTS.md` propio.

### Frontend + backend separados

El frontend usa el árbol web. El backend Nest usa el árbol de la sección NestJS.

### Backend compartido por múltiples clientes

Preferir NestJS cuando el backend sirva web, mobile, admin u otros clientes.

### Arquitectura distribuida / microservicios

Cada servicio puede tener su propio `AGENTS.md`. No asumir que todos usan el mismo framework o la misma base de datos.

## 6. Regla de decisión

La arquitectura no anticipa complejidad hipotética.

Preferir la solución más simple que cubra la necesidad actual y permita evolucionar.
