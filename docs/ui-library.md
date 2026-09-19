# UI Library Standards

Los componentes UI reutilizables vienen de la librería `vicdev-ui-lib` en lugar de reimplementarse en cada proyecto.

## Qué es

`vicdev-ui-lib` es una biblioteca de componentes React con sistema de tema centralizado por variables CSS. Soporta Tailwind CSS v3 y v4.

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
import { VicdevUIProvider, createTheme } from 'vicdev-ui-lib';

const theme = createTheme({
  primary: '#...',
  light: { background: '#...', foreground: '#...' },
  dark: { background: '#...', foreground: '#...' },
});

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

## Tailwind

### Tailwind v4 (CSS-first)

Variables CSS en `globals.css` + bloque `@theme inline` que mapea las variables a utilidades. El ejemplo completo de la librería está en `tailwind.v4.css.example`.

### Tailwind v3

La librería expone `getTailwindConfig()`:

```js
// tailwind.config.js
const { getTailwindConfig } = require('vicdev-ui-lib/theme');

module.exports = {
  content: ['./src/**/*.{js,ts,jsx,tsx}'],
  theme: { extend: getTailwindConfig() },
};
```

## Reglas de uso

- Buscar el componente en `vicdev-ui-lib` antes de crear uno nuevo.
- Los componentes usan tokens del tema (`bg-primary`, `text-muted-foreground`), nunca colores hardcodeados.
- Componer clases con `cn()` y respetar las convenciones de `docs/component-standards.md`.
- La librería es web (React). No existe aún versión React Native: cuando exista se documentará aquí.