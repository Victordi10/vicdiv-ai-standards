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

Evitar funciones, componentes y servicios que hagan demasiadas cosas.

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
