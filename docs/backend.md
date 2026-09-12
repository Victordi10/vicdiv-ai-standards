# Backend Standards

## Next.js API

Cuando Next.js incluya backend:

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
module/
├── dto/
├── entities/ o schemas/
├── enums/
├── repositories/
├── controller.ts
├── module.ts
└── service.ts
```

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
