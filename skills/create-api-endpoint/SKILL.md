# Skill: Create API Endpoint

## Next.js

```text
route.ts
↓
controller
↓
DTO/Zod
↓
service
↓
repository
↓
model
```

## NestJS

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
11. Revisar manejo de errores.
12. Revisar performance de la query.
13. Añadir tests pertinentes.
14. Actualizar documentación del contrato cuando corresponda.
