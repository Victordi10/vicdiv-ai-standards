# Backend Standards

## Next.js API

El backend de Next copia la forma de un módulo Nest y vive junto a la ruta. El equivalente de `src/client/` en Nest es `src/app/api/clients/`. En un proyecto Next.js no se crea `src/client/`, `src/agent/` ni `src/shop/`.

```text
src/app/api/clients/
├── route.ts
├── client.controller.ts
├── client.service.ts
├── client.repository.ts
├── dto/
├── entities/
└── interfaces/
```

| Nest | Next |
|---|---|
| `client.module.ts` | no se crea |
| `client.controller.ts` | `client.controller.ts` |
| `client.resolver.ts` | `route.ts` (solo HTTP) |
| `client.service.ts` | `client.service.ts` |
| `dto/`, `entities/`, `interfaces/` | las mismas carpetas, en el mismo módulo |

Flujo:

```text
route.ts
↓
controller
↓
DTO / Zod validation
↓
service
↓
repository
↓
model
```

- `route.ts` llama al controller y responde con `successResponse` / `errorResponse`.
- El controller no tiene lógica de negocio.
- El service sí.
- El repository habla con la base. `repository.ts` solo se crea si hay persistencia.
- El front no importa el service ni el repository.
- Si una pantalla necesita un tipo, importa `dto` o `interfaces` de ese módulo. Esos archivos no importan Mongoose ni secretos.
- No crear `src/api/` aparte de `src/app/api/`.
- No crear carpeta `modules/` ni `features/` para el backend.

### Route

Integra HTTP y delega.

### Controller

Recibe datos ya validados, coordina el caso de uso y devuelve la respuesta. No contiene lógica de negocio.

### DTO / validation

Zod es el estándar en Next.js. Los schemas deben reutilizarse cuando eso reduzca duplicación sin volverlos ambiguos.

### Service

Contiene lógica de negocio y reglas del caso de uso.

### Repository

Aísla el acceso a persistencia.

## NestJS

Cuando el backend es independiente y sirve múltiples frontends o el dominio justifica un backend dedicado:

```text
src/<dominio>/
├── <dominio>.module.ts
├── <dominio>.controller.ts
├── <dominio>.service.ts
├── dto/
├── entities/
└── interfaces/
```

`resolver.ts` solo si el módulo ya usa GraphQL. GraphQL no se añade a un backend Next.

Flujo conceptual:

```text
Request
↓
Guards / Pipes / DTO validation
↓
Controller
↓
Service
↓
Repository
↓
Database
```

El DTO no es una capa que "ejecuta antes del controller" como archivo independiente; la validación del DTO ocurre en la frontera de entrada mediante pipes/validadores antes de que la lógica del controller procese el dato.

GraphQL no es obligatorio. REST es completamente válido.

## Controllers

Deben ser delgados.

No colocar lógica de negocio, queries complejas o acceso directo a base de datos.

## Respuestas HTTP

En Next.js, responder únicamente con `successResponse` y `errorResponse` (`docs/toolkit.md`). No construir `NextResponse.json` a mano en cada ruta.

## Manejo de errores

- Usar `async/await` y `try/catch` siempre. No encadenar `.then/.catch`.
- Toda operación que pueda fallar va en `try/catch`.
- En `catch`: registrar con `logger.error` y manejar el error (devolver `errorResponse` o rethrow). No tragarse errores.
- La lógica de negocio avisa con `logger` según gravedad (`info`/`warn`/`error`); `debug` en desarrollo.
