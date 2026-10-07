# Skill: Create React Component

## Procedimiento

1. Buscar componente existente.
2. Buscar componente equivalente en `vicdev-ui-lib` (ver `docs/ui-library.md`).
3. Revisar dominio.
4. Revisar hooks, stores y `lib`.
5. Revisar librerías existentes.
6. Determinar si es genérico o específico del dominio.
7. Colocarlo en `components/ui` si es transversal.
8. Colocarlo en `components/<dominio>/` si es específico. En mobile, la pantalla va en `screens/<dominio>/`.
9. Mantener responsabilidad clara.
10. Evitar props condicionales excesivas.
11. Preferir librería madura para complejidad alta cuando tenga sentido.
12. Añadir tests relevantes.
13. Validar lint/typecheck.

Si el componente ya existe en `vicdev-ui-lib`, usarlo o extenderlo en lugar de crear una alternativa.
Los componentes de la librería usan tokens del tema (`bg-primary`, `text-muted-foreground`), nunca colores hardcodeados.

En Next.js, preferir Server Components y usar `"use client"` solo cuando sea necesario.
