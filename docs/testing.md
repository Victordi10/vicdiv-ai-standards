# Testing Standards

## Niveles

1. Unit tests.
2. Integration tests.
3. E2E cuando el flujo tenga suficiente criticidad o complejidad.

## Stack

Jest es el estándar principal. Testing Library puede utilizarse para componentes y comportamiento de UI.

## Regla de ejecución

Ejecutar únicamente las pruebas y validaciones relevantes a la modificación.

Ejemplos:

```text
UI simple
→ lint + typecheck + tests relevantes

Service
→ lint + typecheck + unit/integration relevantes

Flujo crítico
→ además E2E cuando corresponda
```

No eliminar tests para hacer pasar una tarea. Una falla debe entenderse y resolverse o documentarse.
