# AGENTS.md

## 0. Propósito

Este archivo define las reglas generales de ingeniería para trabajar en este repositorio.

La prioridad es producir software mantenible, consistente, reutilizable y fácil de extender, evitando tanto la duplicación como la abstracción innecesaria.

Las reglas específicas están en `docs/` y los procedimientos repetibles en `skills/`.

> Principio rector: **primero reutilizar; después simplificar/refactorizar; crear abstracciones nuevas solo cuando exista una necesidad real y clara.**

---

# 1. Información del proyecto

> Esta sección es específica de cada proyecto. Al reutilizar este estándar, editar principalmente esta sección.

- **Nombre:** [NOMBRE DEL PROYECTO]
- **Descripción:** [DESCRIPCIÓN BREVE]
- **Tipo:** [WEB / MOBILE / API / SAAS / MONOREPO / OTRO]
- **Arquitectura:** [MONOLITO / MONOLITO MODULAR / MONOREPO / FRONTEND + BACKEND / DISTRIBUIDA / MICROSERVICIOS]
- **Frontend:** [NEXT.JS / REACT / REACT NATIVE / EXPO / OTRO]
- **Backend:** [NEXT.JS API / NESTJS / OTRO]
- **Base de datos:** [MONGODB / MYSQL / OTRA]
- **ODM/ORM:** [MONGOOSE / PRISMA / OTRO]
- **Autenticación:** [DESCRIBIR]
- **Estado global:** Zustand
- **Server state:** TanStack Query
- **Cliente HTTP:** Axios
- **Estilos Web:** Tailwind CSS
- **Estilos Mobile:** [MIXTO / TAILWIND / STYLE OBJECTS / OTRO]
- **Testing:** Jest + tests unitarios/integración; E2E cuando corresponda
- **Package manager:** pnpm (web) / npm (mobile)
- **Node:** [VERSIÓN]
- **Otros:** [INFORMACIÓN RELEVANTE]

## Comandos

- Install: `[COMANDO]`
- Dev: `[COMANDO]`
- Build: `[COMANDO]`
- Test: `[COMANDO]`
- Test integration: `[COMANDO]`
- Lint: `[COMANDO]`
- Typecheck: `[COMANDO]`

---

# 2. Principios de ingeniería

1. Buscar primero una solución existente.
2. Reutilizar antes de crear.
3. Extender una abstracción existente antes de crear otra equivalente.
4. Refactorizar código directamente relacionado con la tarea cuando sea necesario para alinearlo con el estándar.
5. No hacer refactors masivos no relacionados con la tarea.
6. Mantener las responsabilidades separadas.
7. La lógica de negocio no debe vivir en componentes UI, routes o controllers.
8. Evitar duplicación de lógica y reglas.
9. Preferir composición y reutilización antes que copiar y pegar.
10. Mantener la solución lo más simple posible.
11. No crear capas o abstracciones por anticipación.
12. Antes de finalizar, revisar si la solución introdujo duplicación o complejidad innecesaria.

---

# 3. Política de cambios destructivos

No realizar acciones destructivas sin autorización explícita.

Incluye, entre otras:

- eliminar archivos o directorios;
- eliminar módulos, features o componentes;
- borrar datos;
- eliminar colecciones/tablas;
- migraciones destructivas;
- modificar datos de producción;
- reset/clean/rebase destructivo de Git;
- eliminar tests o documentación;
- cambios masivos no solicitados.

"Limpiar", "simplificar" o "refactorizar" no significa que exista permiso para borrar comportamiento.

Si una tarea parece requerir una eliminación, explicar qué se eliminaría antes de hacerlo.

---

# 4. Arquitectura

La arquitectura estándar es **modular / feature-based con capas internas pragmáticas**.

No imponer Clean Architecture o Hexagonal Architecture de manera dogmática.

La organización debe responder al dominio y a las responsabilidades reales.

Arquitecturas soportadas:

- monolito;
- monolito modular;
- monorepo;
- frontend + backend separados;
- backend independiente para múltiples clientes;
- arquitectura distribuida;
- microservicios.

La filosofía se mantiene aunque cambie la estructura física.

Si el repositorio contiene varios servicios o aplicaciones, cada unidad puede tener su propio `AGENTS.md` con reglas más específicas. Las reglas locales complementan las globales.

En monorepo, preferir:

```text
apps/
packages/
```

Cada aplicación conserva su estructura y `packages/` contiene únicamente código realmente compartido.

Consultar `docs/architecture.md`.

---

# 5. Frontend Web

Cuando el proyecto utilice Next.js:

- usar App Router;
- preferir Server Components;
- usar `"use client"` solo cuando sea necesario;
- usar Tailwind CSS;
- usar Zustand para estado global;
- usar TanStack Query para server state;
- usar Axios para HTTP cuando corresponda;
- organizar componentes por dominio;
- usar `components/ui/` para UI genérica;
- mantener Root Layout y providers globales centralizados.

No utilizar Zustand como sustituto de TanStack Query para datos cuya fuente de verdad sea el servidor.

---

# 6. Zustand

Los stores se organizan por dominio o responsabilidad.

Estructura preferida:

```text
stores/
├── ui/
│   ├── store.ts
│   ├── actions.ts
│   ├── types.ts
│   └── storage.ts
├── auth/
│   ├── store.ts
│   ├── actions.ts
│   ├── types.ts
│   └── storage.ts
└── products/
    ├── store.ts
    ├── actions.ts
    └── types.ts
```

`storage.ts` solo existe si realmente se necesita persistencia.

Separar obligatoriamente:

- estado (`store.ts`);
- acciones (`actions.ts`);
- tipos (`types.ts`).

El estado global debe pasar por Zustand. El estado estrictamente local puede usar React state.

Consultar `docs/state-management.md`.

---

# 7. Componentes y UI

Antes de crear un componente nuevo:

1. buscar uno existente;
2. revisar `components/ui/`;
3. revisar componentes del dominio;
4. revisar hooks, `lib` y utilidades;
5. solo después crear una abstracción nueva.

Los componentes básicos pueden ser propios para mantener consistencia.

No es necesario reinventar componentes complejos cuando una librería madura aporta valor real, por ejemplo tablas avanzadas, editores, calendarios o visualizaciones.

Consultar `docs/component-standards.md`.

---

# 8. Root Layout y Providers

Los providers transversales deben vivir en `providers/` cuando la arquitectura lo permita y conectarse desde el Root Layout.

Ejemplos:

- Query provider;
- Theme provider;
- auth provider;
- Toast;
- modal global;
- loading global.

No colocar lógica específica de un dominio en el Root Layout.

---

# 9. Backend con Next.js

Cuando Next.js sea suficiente:

```text
route
  ↓
controller
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

Reglas:

- `route.ts` integra HTTP y delega;
- controller delgado;
- DTO/Zod valida entrada;
- service contiene lógica de negocio;
- repository es la frontera de acceso a DB;
- model representa persistencia;
- no acceder directamente a Mongoose desde controller/service si existe repository;
- no colocar lógica de negocio en `route.ts`.

Consultar `docs/backend.md`.

---

# 10. NestJS

Usar NestJS especialmente cuando el backend sea compartido por varios clientes, por ejemplo:

```text
Web cliente
Web administrador
App móvil
Otros clientes
      ↓
   NestJS API
```

También cuando la complejidad del backend requiera un servicio independiente.

GraphQL no es parte obligatoria de este estándar.

La arquitectura conceptual es:

```text
Controller
↓
DTO + Validation
↓
Service
↓
Repository
↓
Schema/Model
↓
Database
```

En NestJS, el DTO define la entrada y las pipes/validación se ejecutan en la frontera antes de llegar a la lógica de negocio. El controller sigue siendo delgado.

---

# 11. Base de datos

Estándar principal cuando se utiliza MongoDB:

- MongoDB;
- Mongoose;
- Repository Pattern.

Regla:

```text
Service → Repository → Mongoose Model → MongoDB
```

No:

```text
Controller → Mongoose
Component → Database
Service → Mongoose directo
```

Crear índices explícitos cuando sean necesarios.

Antes de implementar consultas complejas revisar filtros, índices, proyecciones, paginación, `populate`, agregaciones y volumen de datos.

La skill `db-connect` permite conexiones autorizadas de desarrollo/diagnóstico, pero no autoriza operaciones destructivas.

Consultar `docs/database.md`.

---

# 12. React Native / Expo

Estándar:

- React Native;
- Expo;
- Zustand;
- organización por dominio;
- screens por dominio;
- stacks por dominio;
- providers en `providers/`;
- componentes reutilizables.

Tailwind puede ser mixto o no utilizarse. Priorizar claridad y rendimiento. Los estilos nativos y `gap` son válidos cuando resulten adecuados.

---

# 13. Código compartido y monorepo

Cuando web, mobile y backend compartan código:

```text
apps/
├── web/
├── mobile/
└── api/

packages/
├── types/
├── validation/
├── utils/
├── config/
└── ui/
```

Compartir solo cuando tenga sentido real.

No crear paquetes artificiales para evitar unas pocas líneas duplicadas.

---

# 14. Naming

Código:

- variables y funciones: `camelCase`;
- componentes, clases y tipos: `PascalCase`;
- nombres descriptivos;
- usar siempre TypeScript en vez de JavaScript: escribir el código fuente en `.ts` / `.tsx` y no crear archivos `.js` / `.jsx` nuevos con lógica de aplicación;
- evitar `any` salvo excepción justificada.

Base de datos:

- preferir `kebab-case` según la convención del proyecto;
- no renombrar estructuras existentes innecesariamente;
- mantener consistencia con el esquema existente.

---

# 15. Testing

Estándar:

- unit tests;
- integration tests;
- E2E cuando el flujo lo justifique.

Stack preferido: Jest y Testing Library cuando aplique.

Ejecutar únicamente las validaciones que tengan sentido para la modificación.

---

# 16. Git y releases

Estandarizar cuando el proyecto lo requiera:

- branches;
- commits;
- Conventional Commits;
- pull requests;
- changelog.

No borrar commits, branches, tags, changelogs o documentación sin autorización.

No modificar historia de Git destructivamente sin autorización explícita.

---

# 17. Documentación

Actualizar `docs/` cuando cambien de forma relevante:

- arquitectura;
- contratos de API;
- modelos;
- integraciones;
- procesos;
- decisiones estructurales.

No documentar trivialidades.

---

# 18. Flujo antes de implementar

1. Entender la arquitectura real.
2. Revisar el dominio afectado.
3. Buscar implementaciones similares.
4. Buscar abstracciones reutilizables.
5. Leer documentación relevante.
6. Identificar skills aplicables.
7. Implementar respetando las capas existentes.
8. Refactorizar lo necesario y relacionado.
9. Ejecutar validaciones relevantes.
10. Actualizar documentación si corresponde.
11. Revisar que no existan eliminaciones o cambios destructivos no autorizados.
12. Resumir cambios y decisiones importantes.

---

# 19. Resolución de ambigüedad

Para decisiones pequeñas que no afecten arquitectura, seguridad, compatibilidad o datos, elegir la opción más consistente con el código existente.

Para decisiones que puedan afectar arquitectura, datos, seguridad, compatibilidad, costos o pérdida de información, no asumir autorización.

---

# 20. Referencias

- Arquitectura: `docs/architecture.md`
- Frontend: `docs/frontend.md`
- Backend: `docs/backend.md`
- Estado: `docs/state-management.md`
- DB: `docs/database.md`
- Componentes: `docs/component-standards.md`
- Código: `docs/coding-standards.md`
- Testing: `docs/testing.md`
- Git: `docs/git-standards.md`
- Toolkit: `docs/toolkit.md`
- Theming: `docs/theming.md`

Skills:

- Feature: `skills/create-feature/SKILL.md`
- Endpoint: `skills/create-api-endpoint/SKILL.md`
- Zustand: `skills/create-zustand-store/SKILL.md`
- React component: `skills/create-react-component/SKILL.md`
- DB: `skills/db-connect/SKILL.md`
- Code review: `skills/review-code/SKILL.md`
- Setup de proyecto: `skills/setup-project/SKILL.md`
