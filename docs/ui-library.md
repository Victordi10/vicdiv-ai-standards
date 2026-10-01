# UI Library Standards

Los componentes UI reutilizables vienen de la librería `vicdev-ui-lib` en lugar de reimplementarse en cada proyecto.

## Qué es

`vicdev-ui-lib` es una biblioteca de componentes React con sistema de tema centralizado por variables CSS. Soporta Tailwind CSS v3 y v4.

Versión publicada en npm: **`vicdev-ui-lib@1.4.0`**. Incluye la entrada CSS `vicdev-ui-lib/tailwind.css`, el subpath de runtime `vicdev-ui-lib/theme` y los presets de marca.

Componentes disponibles:

| Grupo | Componentes |
| --- | --- |
| Feedback | `Toast` |
| Formularios | `FormField`, `SearchBar`, `MultiSelect`, `ToggleSwitch`, `MultiSelectCheckbox`, `FileUpload` |
| Superficies | `Card`, `CardHeader`, `CardTitle`, `CardDescription`, `CardContent`, `CardFooter`, `CardMedia` |
| Loaders | `Loader`, `LoaderScreen` |
| Modales | `Modal`, `DeleteConfirmModal` |
| Datos | `DataTable`, `CurrencyDisplay` |
| Archivos | `FileLink`, `FileViewerModal` |
| Layout | `AppShell`, `LayoutProvider`, `AppHeader`, `SidebarNav`, `MobileNav`, `BreadcrumbNav`, `AppFooter` |
| Identidad | `Logo` |
| Botones | `ActionButton` |

## Instalación

La librería puede instalarse desde npm registry o desde la ruta local (como fuente en desarrollo).

### Desde npm registry

```bash
pnpm add vicdev-ui-lib
```

### Desde ruta local

```bash
pnpm add file:../vicdev-ui-react-lib
```

Preferir la ruta local durante desarrollo de la librería o cuando la versión publicada no incluya los cambios necesarios.

### Dependencias peer

```bash
pnpm add react react-dom
pnpm add tailwindcss         # v3 o v4
pnpm add lucide-react        # iconos
pnpm add framer-motion       # animaciones (modales, toggles)
pnpm add primereact primeicons   # solo si se usa DataTable
```

## Provider y configuración

`VicdevUIProvider` centraliza tema, z-index, animaciones y defaults de componentes. `ThemeProvider` es opcional e inyecta el CSS del tema (`injectCSS`).

```tsx
import { VicdevUIProvider, createTheme, presets } from 'vicdev-ui-lib';

const theme = createTheme(presets.lino);
// o colores propios:
// createTheme({ primary: '#...', light: { background: '#...' }, dark: { background: '#...' } });

<VicdevUIProvider
  config={{
    theme,
    modalZIndex: 1000,
    animationDuration: 180,
    components: {
      button: { defaultVariant: 'primary', defaultSize: 'md' },
      toast: { position: 'top-right' },
    },
  }}
>
  {children}
</VicdevUIProvider>
```

## Integración con el store de theme

El store de theme (Zustand, ver `docs/theming.md` y `docs/state-management.md`) es la fuente de la preferencia y resuelve el tema:

1. El store persiste `themePreference: 'light' | 'dark' | 'system'`.
2. `ThemeProvider` del proyecto resuelve el tema real y expone `isDark`/`isLight`.
3. La preferencia se aplica al `documentElement`: la librería usa clases `.light`/`.dark` (no `data-theme`).
4. Los componentes de la librería leen las variables CSS del host; no necesitan pasar el tema por props.

```tsx
// alternar el tema aplicado por la librería
document.documentElement.classList.toggle('dark', isDark);
```

Con `next-themes`, usar `attribute="class"` para alternar `.dark` correctamente.

## Presets

Los presets son argumentos de `createTheme`: colores de claro en la raíz, más `light` y `dark`. No hace falta copiar hexadecimales.

```tsx
import { createTheme, presets, presetModeCssVars } from 'vicdev-ui-lib';

const theme = createTheme(presets.automerco);
const darkVars = presetModeCssVars(presets.automerco, 'dark');
```

`presetModeCssVars(preset, mode)` devuelve las variables CSS del modo (`--background`, `--primary`, …) para un `style` inline o un bloque `:root` / `.dark`.

| Clave | Uso |
| --- | --- |
| `mineral`, `ocean`, `plum` | Laboratorio |
| `monochrome` | Gris neutro, sin color de marca |
| `vino` | Burdeos sobre papel humo y negro |
| `piedra`, `niebla`, `lino` | Plantillas neutras (gris cálido, gris frío, papel cálido) |
| `automerco` | Naranja `#F84715` y amarillo `#F4BF3B`, tomados de Automerco |
| `jorge` | Púrpura `#150D3E`, amarillo `#FCC50D` y naranja `#ED4C21` |
| `darkfocus` | Rojo `#ee2b2b` / `#f04242`, tomado de Darkfocus |

Viva no tiene preset: el repositorio consultado no incluye paleta de interfaz.

Si el proyecto define su propia marca, `src/constants/theme/theme.ts` sigue siendo la fuente de verdad. El preset solo es el punto de partida.

## Tailwind

### Tailwind v4 (CSS-first)

Variables CSS en `globals.css` e import de la entrada que publica la librería. Esa entrada escanea el bundle, mapea los tokens y trae el CSS de DataTable:

```css
@import "tailwindcss";
@import "vicdev-ui-lib/tailwind.css";
```

Sin ese import, Tailwind v4 no escanea `node_modules` y las clases del paquete (`bg-card`, `border-sidebar-border`, `bg-linear-to-r`, …) no se generan. El `@source` manual al bundle queda solo como respaldo si el host ya define su propio `@theme inline`.

En un host ya instalado, el binario deja esos imports listos. No instala dependencias. Si el proyecto sigue en Tailwind v3, no crea `tailwind.config.js` y remite a `getTailwindConfig()`. Dentro de `vicdev-ui-lib` el comando se detiene.

```bash
pnpm exec vicdev-setup-tailwind
```

### Tailwind v3

La librería expone `getTailwindConfig()`:

`vicdev-ui-lib/theme` resuelve a JavaScript en runtime (`getTailwindConfig`). En v3 hay que listar el bundle en `content`:

```js
// tailwind.config.js
const { getTailwindConfig } = require('vicdev-ui-lib/theme');

module.exports = {
  content: [
    './src/**/*.{js,ts,jsx,tsx}',
    './node_modules/vicdev-ui-lib/dist/vicdev-ui-lib.es.js',
  ],
  theme: { extend: getTailwindConfig() },
};
```

## Reglas de uso

- Buscar el componente en `vicdev-ui-lib` antes de crear uno nuevo.
- Los componentes usan tokens del tema (`bg-primary`, `text-muted-foreground`), nunca colores hardcodeados.
- Componer clases con `cn()` y respetar las convenciones de `docs/component-standards.md`.
- La librería es web (React). No existe aún versión React Native: cuando exista se documentará aquí.