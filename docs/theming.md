# Theming Standards

Tema centralizado para web y mobile. La estructura es canónica; los valores de marca se definen por proyecto (cada app puede tener sus propios `BRAND_COLORS`).

## 1. Fuente de verdad única

Todo color vive en un único archivo: `src/constants/theme/theme.ts`.

Objetos:

- `BRAND_COLORS` — constantes de marca, no cambian con el tema (`primary`, `secondary`, `green`, `darkGray`, `lightGray`, etc.).
- `LIGHT_THEME` y `DARK_THEME` — dos objetos con las **mismas claves** y valores distintos: `background`, `foreground`, `card`, `cardFg`, `popover`, `muted`, `mutedFg`, jerarquía de texto (`textPrimary|Secondary|Tertiary|Muted|Inverse|Link|LinkHover|Placeholder`), inputs (`border`, `input`, `ring`, `accent`, `accentFg`) y sidebar (`sidebar*`).
- `STATUS_COLORS` — constantes de feedback: `destructive`, `error`, `success`, `warning`, `info`.
- Helpers: `THEME.getThemeColors(theme)`, `getBackgroundColor(theme)`, `getCardColor(theme)`, etc.

Reglas:

- este archivo es la **única** fuente de verdad;
- no duplicar paletas divergentes en otro sitio (por ejemplo `index.css`, webviews o widgets);
- los componentes consumen colores desde el theme, no hardcodean hex.

## 2. Estado del theme (Zustand + Provider + storage)

El theme es estado global gestionado con Zustand y persistido en storage. Estructura:

```text
stores/
└── theme/
    ├── store.ts
    ├── actions.ts
    ├── types.ts
    └── storage.ts
```

Comportamiento:

- `themePreference: 'light' | 'dark' | 'system'`;
- persistencia: `localStorage` en web, `AsyncStorage` en mobile;
- `useTheme()` expone: `theme`, `colors`, `isDark`, `isLight`, `setTheme(pref)`, `themePreference`;
- el modo `system` resuelve el tema real desde el sistema.

### Provider

- `ThemeProvider` consume el store, resuelve el tema y expone `colors` a toda la app.
- En web, con `vicdev-ui-lib`, sincroniza la clase `.dark` en `document.documentElement`. Un host sin la librería puede usar `data-theme`.
- En mobile, expone `colors` vía contexto/hook (`useTheme()`).

### Integración con la UI library

Versión publicada: `vicdev-ui-lib@1.4.0`. Detalle de instalación, presets y Tailwind: `docs/ui-library.md`.

- `vicdev-ui-lib` lee las variables CSS del host: los componentes no reciben el tema por props.
- El host importa la entrada de la librería para que Tailwind v4 genere las clases del paquete:

```css
@import "tailwindcss";
@import "vicdev-ui-lib/tailwind.css";
```

- El modo de la librería es la clase `.dark` en `documentElement` (también emite `:root.light, .light`). No usa `data-theme`.
- Opcionalmente `VicdevUIProvider`/`ThemeProvider` de la librería inyecta el CSS desde `createTheme`.
- Un proyecto puede partir de un preset (`createTheme(presets.automerco)`, `presets.jorge`, `presets.darkfocus`, `presets.lino`, …) en lugar de copiar hexadecimales. `presetModeCssVars(preset, 'dark')` devuelve las variables del modo activo.
- Cuando la app define su propia marca, `src/constants/theme/theme.ts` sigue siendo la fuente de verdad. El preset no la reemplaza.
- Con `next-themes`, usar `attribute="class"`.

### Web (Next.js)

- Aplicar el atributo de tema al `documentElement` antes del primer render para evitar flashes.
- Opcional: `next-themes` como mecanismo base, pero el estado canónico vive en el store de Zustand (no al revés).

### Mobile (React Native / Expo)

- `app.json` debe tener `"userInterfaceStyle": "automatic"` para que el modo `system` detecte el tema nativo. Con `"light"` forzado, `useColorScheme()` siempre devuelve `light`.

## 3. Tailwind

Los tokens se exponen a Tailwind desde la fuente de verdad.

### Web — Next.js + Tailwind v4 (CSS-first)

Con `vicdev-ui-lib`, el host importa `vicdev-ui-lib/tailwind.css`. Esa entrada ya trae `@theme inline`, la variante `.dark` y el escaneo del bundle. El modo oscuro se aplica con la clase `.dark`, no con `data-theme`.

```css
@import "tailwindcss";
@import "vicdev-ui-lib/tailwind.css";

:root {
  --background: #ffffff;
  --foreground: #1c1717;
  --card: #fafafa;
  /* ... o las variables de presetModeCssVars(preset, 'light') */
}

.dark {
  --background: #0b0909;
  --foreground: #faf9f9;
  --card: #161212;
  /* ... o las variables de presetModeCssVars(preset, 'dark') */
}
```

```tsx
document.documentElement.classList.toggle('dark', isDark);
```

`pnpm exec vicdev-setup-tailwind` inserta esos dos `@import` en el CSS global del host. No instala paquetes.

Uso en componentes: `bg-background`, `bg-card`, `text-foreground`, `text-muted-foreground`, `border-border`, `ring-ring`, `sidebar-*`.

Un host que no use la librería puede seguir con `[data-theme="dark"]` y su propio `@theme`. Si conviven ambos, la librería solo reacciona a `.dark`.

### Mobile — React Native / NativeWind

- Si se usa NativeWind, `tailwind.config.js` importa las escalas desde la fuente compartida (`color-scales.js` + `colors.ts`), no hardcodea colores.
- `darkMode: ["class"]`, `content` cubre `App.*` y `src/**/*`.
- En RN el `darkMode` class no cambia automáticamente: la app resuelve el tema por JS (store/provider) y aplica las clases correspondientes.

## 4. Jerarquía de texto

Usar clases semanticas de texto, no colores crudos:

```text
content-primary   → texto principal (títulos, headings)
content-secondary → body text, descripciones
content-tertiary  → captions, labels pequeños
content-muted     → deshabilitados, placeholders
content-inverse   → texto sobre fondos oscuros/primarios
content-link      → enlaces
```

## 5. Casos de error a evitar

- Duplicar la paleta en `index.css` con valores distintos a `theme.ts` → divergencia.
- Componentes de librería (MUI, PrimeReact, etc.) cargando solo su tema claro y no adaptándose en dark.
- Colores hardcodeados en componentes (hex sueltos) en vez de tokens.
- `userInterfaceStyle` forzado a `light` en mobile rompiendo el modo `system`.