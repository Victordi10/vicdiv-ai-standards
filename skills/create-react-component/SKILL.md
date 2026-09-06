# Skill: Create React Component

## Procedimiento

1. Buscar componente existente.
2. Buscar componente UI equivalente.
3. Revisar dominio.
4. Revisar hooks, stores y `lib`.
5. Revisar librerías existentes.
6. Determinar si es genérico o específico del dominio.
7. Colocarlo en `components/ui` si es transversal.
8. Colocarlo en el dominio si es específico.
9. Mantener responsabilidad clara.
10. Evitar props condicionales excesivas.
11. Preferir librería madura para complejidad alta cuando tenga sentido.
12. Añadir tests relevantes.
13. Validar lint/typecheck.

En Next.js, preferir Server Components y usar `"use client"` solo cuando sea necesario.
