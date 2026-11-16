---
name: use-ui-library
description: "Trigger: vicdev-ui-lib, FormField, NumberField, TextField, input, formulario, campo numérico, componentes UI web. Use vicdev-ui-lib correctly and filter inputs by data type."
license: Apache-2.0
metadata:
  author: "victordev"
  version: "1.0"
---

## Activation Contract

Cargar al construir o revisar pantallas, formularios o inputs web (React / Next.js) que usen `vicdev-ui-lib`.

Para crear un componente nuevo, cargar `create-react-component`.

## Hard Rules

- Buscar el componente en `vicdev-ui-lib` antes de crear uno propio.
- Nunca `<input type="number">` nativo ni `FormField type="number"` sin `allow` explícito para enteros.
- Todo input numérico, de teléfono o de documento filtra al escribir (`allow` o `sanitize`) y valida al enviar con Zod.
- El estado guarda el texto filtrado; convertir a número solo al enviar (`parseQuantity`, `Number`).
- Estado y tono por props (`state`, `tone`, `variant`); `className` solo para layout. Nunca colores hardcodeados.
- Antes de usar el kit (`TextField`, `NumberField`, `Button`, `Dialog`…) o `FormField allow`, comprobar que la versión instalada lo exporte. `1.4.0` publicado no los incluye.

## Decision Gates

| Dato | Componente |
|------|------------|
| Entero (cantidad, documento, código) | `NumberField` (default `allow="digits"`) |
| Decimal | `NumberField allow="decimal" inputMode="decimal"` |
| Cantidad o dinero formateado | `NumberField allow="quantity"` + `parseQuantity` |
| Teléfono | `TextField type="tel" allow="digits" inputMode="tel"` |
| Email | `TextField type="email" autoComplete="email"` |
| Contraseña | `PasswordField` |
| Fecha | `DateField` / `CalendarField` |
| Regla propia (negativos, máscara) | `sanitize={(raw) => ...}` |
| Kit no disponible en la versión instalada | `FormField` con `allow`; si tampoco existe, filtrar en `onChange` y avisar |

## Execution Steps

1. Revisar la versión de `vicdev-ui-lib` en `package.json` y qué exporta (`node_modules/vicdev-ui-lib/dist/index.d.ts`).
2. Mapear cada campo a su tipo de dato con la tabla.
3. Componer el formulario con los componentes elegidos; schema Zod para el envío.
4. Probar a mano: escribir y pegar letras, `e`, `+`, `-` en los campos numéricos.
5. Ejecutar lint y typecheck.

## Output Contract

Devolver: campos y componente elegido para cada uno, filtros aplicados, schema de validación, y cualquier componente que faltó en la versión instalada.

## References

- `docs/ui-library.md` — instalación, provider, catálogo e inputs por tipo de dato.
- `docs/component-standards.md`, `docs/theming.md`
