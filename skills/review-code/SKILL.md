# Skill: Review Code

## Objetivo

Encontrar problemas reales, no realizar críticas estéticas sin impacto.

## Revisión

1. Arquitectura.
2. Separación de responsabilidades.
3. Reutilización/duplicación.
4. Uso de TypeScript en vez de JavaScript (sin archivos `.js`/`.jsx` con lógica de aplicación).
5. Estado.
6. Validación.
7. Manejo de errores (`try/catch`, `async/await`, sin errores tragados).
8. Toolkit (uso de `logger`, `successResponse`/`errorResponse`, cliente `api`).
9. Theming (tokens, no colores hardcodeados; tema centralizado).
10. DB.
11. Seguridad.
12. Performance.
13. Tests.
14. Documentación relevante.

## Prioridad

### Crítico

Seguridad, pérdida/corrupción de datos, errores graves.

### Alto

Violaciones arquitectónicas importantes, bugs probables, acceso incorrecto a DB.

### Medio

Duplicación, mantenibilidad, performance mejorable.

### Bajo

Detalles cosméticos.

No recomendar refactors gigantes por preferencia personal.
No eliminar código sin autorización.
