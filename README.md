# Victor AI Engineering Standards v1.0

Estándar reutilizable para desarrollo asistido por IA.

## Capas

```text
AGENTS.md
→ reglas generales + información del proyecto + mapa

docs/
→ conocimiento detallado

skills/
→ procedimientos repetibles
```

## Uso

Copiar `AGENTS.md`, `docs/` y `skills/` al repositorio.

Editar en primer lugar la sección `Información del proyecto`.

Si el proyecto tiene una arquitectura diferente, no rehacer las reglas globales: agregar o sobrescribir las reglas específicas en el `AGENTS.md` del subproyecto/servicio cuando sea necesario.

## Principio rector

```text
Primero reutilizar.
Después simplificar/refactorizar.
Crear una abstracción nueva solo cuando exista una necesidad real.
```

## Arquitectura

Preferencia:

```text
Modular / Feature-based
+
capas internas pragmáticas
```

El estándar también soporta monolitos, monolitos modulares, monorepos, frontend/backend separados y arquitecturas distribuidas.

## Seguridad

Las acciones destructivas requieren autorización explícita.
