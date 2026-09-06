# Skill: Database Connection

## Objetivo

Conectarse a MongoDB/Mongoose para desarrollo, diagnóstico y análisis autorizados.

## Procedimiento

1. Revisar `AGENTS.md`.
2. Revisar `docs/database.md`.
3. Identificar el entorno correcto.
4. Usar la configuración/credenciales existentes sin imprimir secretos.
5. Confirmar que la tarea es de lectura/diagnóstico o que la modificación está expresamente autorizada.
6. Preferir repositories para probar comportamiento de aplicación.
7. Para consultas directas de diagnóstico, hacer el mínimo de lecturas necesarias.
8. Revisar índices y performance cuando corresponda.
9. Liberar conexiones correctamente.

## Prohibido sin autorización explícita

- borrar colecciones;
- updates masivos;
- deletes masivos;
- migraciones destructivas;
- tocar producción;
- modificar datos sensibles de forma irreversible.

Esta skill concede capacidad técnica de conexión, no autorización para destruir o modificar datos.
