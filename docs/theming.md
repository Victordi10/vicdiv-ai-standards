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
- En web, sincroniza `data-theme` (o `class`) en `document.documentElement` y sobre `html`.
- En mobile, expone `colors` vía contexto/hook (`useTheme()`).

### Web (Next.js)

- Aplicar el atributo de tema al `documentElement` antes del primer render para evitar flashes.
- Opcional: `next-themes` como mecanismo base, pero el estado canónico vive en el store de Zustand (no al revés).

### Mobile (React Native / Expo)

- `app.json` debe tener `"userInterfaceStyle": "automatic"` para que el modo `system` detecte el tema nativo. Con `"light"` forzado, `useColorScheme()` siempre devuelve `light`.

## 3. Tailwind

Los tokens se exponen a Tailwind desde la fuente de verdad.

### Web — Next.js + Tailwind v4 (CSS-first)

- Variables CSS en `globals.css` divididas en bloques `:root` (claro) y `[data-theme="dark"]` (oscuro), más un fallback `@media (prefers-color-scheme: dark)`.
- Bloque `@theme` mapea las variables a utilidades Tailwind:

```css
@import "tailwindcss";

:root {
  --background: #ffffff;
  --foreground: #1c1717;
  --card: #fafafa;
  /* ... */
}

[data-theme="dark"] {
  --background: #07080d;
  --foreground: #f8fafc;
  --card: #10131a;
  /* ... */
}

@theme {
  --color-background: var(--background);
  --color-card: var(--card);
  /* ... */
}
```

Uso en componentes: `bg-background`, `bg-card`, `text-foreground`, `text-muted-foreground`, `border-border`, `ring-ring`, `sidebar-*`.

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