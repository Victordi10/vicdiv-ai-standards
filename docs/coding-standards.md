# Coding Standards

## Naming

- Variables/funciones: `camelCase`.
- Componentes/clases/tipos: `PascalCase`.
- Base de datos: preferencia `kebab-case` según el proyecto.

## Reutilización

Buscar antes de crear:

- helpers;
- hooks;
- services;
- components;
- stores;
- repositories;
- validators.

## Simplicidad

Preferir código sencillo y explícito cuando una abstracción no tenga beneficio claro.

## Responsabilidad

Principio de responsabilidad única (SRP): una clase, función, componente o service hace una sola cosa. El archivo se mantiene corto porque cada unidad tiene una sola responsabilidad. Si acumula casos distintos, se parte por esa responsabilidad (otro service, helper o componente).

Métrica de cohesión (~200 líneas): una clase rara vez supera unas 200 líneas. Un archivo de miles de líneas es un God Object y no se sigue extendiendo: el cambio nuevo sale a otro archivo con su propia responsabilidad.

La métrica no es un límite de CI. Superar ~200 líneas no autoriza un refactor masivo de archivos que la tarea no toca, ni partir un archivo cohesivo solo para bajar el número. Aplica el principio 5 de `AGENTS.md`.

## Tipos

Usar siempre TypeScript en vez de JavaScript.

- Todo el código fuente se escribe en TypeScript (`.ts` / `.tsx`).
- No crear archivos `.js` / `.jsx` nuevos que contengan lógica de aplicación.
- Aprovechar los tipos para expresar contratos, autocompletado y mantenibilidad.

Evitar `any` salvo casos justificados.

## Validación

Validar en las fronteras del sistema. En Next.js usar Zod. En NestJS usar DTOs y validación apropiada.

## Asincronía y errores

- Usar `async/await` siempre. No encadenar `.then/.catch` salvo necesidad puntual.
- Operaciones que puedan fallar van en `try/catch`.
- Registrar errores con `logger` (`docs/toolkit.md`); nunca tragarse un error sin registrarlo y manejar/rethrow.
- No usar `console` directo.

## Herramientas

- Package manager: `pnpm` en web; `npm` en mobile.
- Estilos: Tailwind por defecto.
- Tema: usar el theme centralizado (`docs/theming.md`), nunca colores hardcodeados.

## Comentarios

Comentar el porqué, no el qué obvio.

## Refactor

Se permite mejorar código directamente relacionado con la tarea cuando:

- elimina duplicación;
- corrige una estructura problemática;
- mejora consistencia;
- mantiene el comportamiento esperado.

No hacer refactor global por gusto estético.
