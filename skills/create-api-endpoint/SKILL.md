# Skill: Create API Endpoint

## Next.js

Crear el módulo en `src/app/api/<dominio>/`. No crear `src/<dominio>/` (eso es Nest), ni `src/api/`, ni `features/`.

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

`route.ts` llama al controller y responde con `successResponse` / `errorResponse`. El front no importa service ni repository.

## NestJS

Crear el módulo en `src/<dominio>/` (`module`, `controller`, `service`, `dto/`, `entities/`, `interfaces/`). `resolver.ts` solo si ese backend ya usa GraphQL.

```text
request
↓
pipes/DTO validation
↓
controller
↓
service
↓
repository
↓
model/schema
```

## Procedimiento

1. Revisar endpoint similar.
2. Revisar schemas/DTO reutilizables.
3. Revisar service.
4. Revisar repository.
5. Reutilizar antes de crear.
6. Validar input.
7. Mantener controller delgado.
8. Mantener lógica de negocio en service.
9. Mantener DB en repository.
10. Revisar autenticación/autorización.
11. Revisar manejo de errores (usar `successResponse`/`errorResponse` y `logger`, ver `docs/toolkit.md`).
12. Revisar performance de la query.
13. Añadir tests pertinentes.
14. Actualizar documentación del contrato cuando corresponda.
