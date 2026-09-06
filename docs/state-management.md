# State Management Standards

## Zustand

Zustand es el estándar para estado global.

## Estructura

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

`storage.ts` es opcional.

## Responsabilidades

### store.ts

Estado y configuración del store.

### actions.ts

Acciones/mutaciones del store.

### types.ts

Tipos relacionados.

### storage.ts

Persistencia, solo cuando sea necesaria.

## Regla de estado

```text
Estado local → React state
Estado global → Zustand
Server state → TanStack Query
```

No mezclar estas responsabilidades sin razón.

## Persistencia

Persistir únicamente los datos que realmente necesiten sobrevivir a la recarga o cierre de la aplicación.
