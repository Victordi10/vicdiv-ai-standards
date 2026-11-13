---
name: react-native-ux-performance
description: "Trigger: React Native, Expo, mobile screen, scroll, pull to refresh, keyboard, lista, formulario, rendimiento, UX. Apply UX and performance patterns when building or reviewing mobile screens."
license: Apache-2.0
metadata:
  author: "victordev"
  version: "1.0"
---

## Activation Contract

Cargar al crear, modificar o revisar una pantalla, lista, formulario o componente en `screens/`, `components/` o `stack/` de una app React Native / Expo.

Para profiling profundo (FPS, TTI, bundle, memory leaks, módulos nativos) cargar además `react-native-best-practices`.

## Hard Rules

- Toda pantalla con scroll maneja teclado, safe area y estados (loading, empty, error).
- Listas: `FlashList` o `FlatList`. Nunca `ScrollView` + `.map()` para datos dinámicos.
- Pull to refresh con `RefreshControl` ligado a un estado manual, no a `isFetching` de TanStack Query.
- Server state en TanStack Query; Zustand solo para estado de cliente. Respetar `docs/state-management.md`.
- Colores y espacios desde el tema (`docs/theming.md`), nunca hardcodeados.
- Animaciones con Reanimated en el UI thread; nunca animar con `setState`.
- Áreas táctiles mínimas de 44x44 pt (`hitSlop` si el visual es menor).
- No bloquear el hilo JS en el montaje: trabajo pesado diferido (`InteractionManager`, `useDeferredValue`).
- Si una librería sugerida no está en el `package.json` de la app, usar el fallback nativo (`FlatList`, `KeyboardAvoidingView`, `Image`) y preguntar antes de instalarla.

## Decision Gates

| Situación | Solución |
|-----------|----------|
| Formulario con inputs | `KeyboardAwareScrollView` (`react-native-keyboard-controller`); fallback `KeyboardAvoidingView` |
| Lista larga o infinita | `FlashList` + `useInfiniteQuery` + `onEndReached` |
| Refrescar datos | `RefreshControl` + `refetch()` manual |
| Mutación visible al instante | Optimistic update (`onMutate` + rollback en `onError`) |
| Imágenes remotas | `expo-image` con `placeholder` y cache |
| Acción importante o destructiva | Feedback háptico (`expo-haptics`) + confirmación |
| Re-renders sospechosos | Medir con React DevTools Profiler antes de memoizar |

## Execution Steps

1. Identificar el tipo de pantalla (lista, formulario, detalle, mixta).
2. Leer `references/ux-patterns.md` y aplicar los patrones del tipo.
3. Leer `references/performance-checklist.md` y validar el código.
4. Cubrir los tres estados: loading (skeleton), empty (mensaje + acción), error (reintentar).
5. Verificar iOS y Android: teclado, safe area, back button, notch.
6. Ejecutar lint y typecheck (`npm`).

## Output Contract

Devolver: archivos creados o modificados, patrones UX aplicados, checklist de performance verificado, y cualquier punto no verificable sin dispositivo real (señalarlo explícitamente).

## References

- `references/ux-patterns.md` — teclado, scroll, refresh, feedback, formularios, estados.
- `references/performance-checklist.md` — listas, renders, imágenes, animaciones, arranque.
- `docs/state-management.md`, `docs/theming.md`, `docs/component-standards.md`
