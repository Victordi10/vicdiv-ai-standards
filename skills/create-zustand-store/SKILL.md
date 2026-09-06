# Skill: Create Zustand Store

## Procedimiento

1. Identificar dominio.
2. Buscar store existente.
3. Extenderlo si cubre la necesidad.
4. Crear `store.ts`.
5. Crear `actions.ts`.
6. Crear `types.ts`.
7. Crear `storage.ts` solo si existe persistencia real.
8. Verificar que no sea server state.
9. Revisar selectores.
10. Añadir persistencia únicamente para lo necesario.
11. Añadir tests cuando exista lógica relevante.

## Estructura

```text
stores/
└── domain/
    ├── store.ts
    ├── actions.ts
    ├── types.ts
    └── storage.ts
```
