# Database Standards

## Estándar principal

- MongoDB.
- Mongoose.
- Repository Pattern.

## Flujo

```text
Service
↓
Repository
↓
Mongoose Model
↓
MongoDB
```

## Regla de acceso

El repository es la frontera normal para acceso directo a DB desde la aplicación.

Evitar:

```text
Controller → Mongoose
Service → Mongoose directamente
Component → Database
```

## Model

El model/schema describe persistencia. No debe convertirse en un lugar improvisado para toda la lógica de negocio.

## Índices

Definir índices explícitamente cuando los patrones de acceso los justifiquen.

## Performance

Antes de una consulta compleja revisar:

- filtros;
- índices;
- proyección;
- paginación;
- volumen;
- `populate`;
- agregaciones;
- N+1;
- costo de escritura.

La performance debe evaluarse en función del patrón de acceso real, no por optimización ornamental.

## Seguridad

Nunca exponer secretos ni modificar producción sin autorización.
