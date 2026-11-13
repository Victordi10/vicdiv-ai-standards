# Performance Checklist (React Native / Expo)

Medir antes de optimizar. Para profiling detallado cargar `react-native-best-practices`.

## Listas

- [ ] `FlashList` con `estimatedItemSize` (o `FlatList` con `getItemLayout` si altura fija).
- [ ] `renderItem` extraído a componente con `React.memo`; props estables.
- [ ] `keyExtractor` con id estable.
- [ ] Sin funciones inline ni objetos nuevos pasados a items en cada render (`useCallback`/`useMemo` solo donde el profiler lo justifique).
- [ ] Paginación (no cargar todo de una vez).
- [ ] Items sin `ScrollView` anidado del mismo eje.

## Renders

- [ ] Selectores de Zustand granulares: `useStore((s) => s.campo)`, nunca el store entero.
- [ ] TanStack Query con `select` para derivar datos y `staleTime` razonable (evita refetch innecesario).
- [ ] Estado local en el componente más bajo posible; no subirlo "por si acaso".
- [ ] Context solo para valores que cambian poco (tema, sesión); no para estado frecuente.
- [ ] Si el proyecto usa React Compiler, no memoizar a mano.

## Imágenes

- [ ] `expo-image` con `contentFit`, `placeholder` (blurhash) y cache.
- [ ] Tamaño solicitado acorde al render (no descargar 4000 px para un avatar).
- [ ] Formatos WebP/AVIF cuando el backend lo permita.

## Animaciones

- [ ] Reanimated (`useSharedValue`, `useAnimatedStyle`) para todo lo continuo o por gesto.
- [ ] Gestos con `react-native-gesture-handler`.
- [ ] Solo propiedades baratas: `transform` y `opacity`.

## Hilo JS y arranque

- [ ] Trabajo pesado tras la transición: `InteractionManager.runAfterInteractions` o `useDeferredValue`.
- [ ] Sin `console.log` en producción en rutas calientes.
- [ ] Imports directos, sin barrel files grandes en el camino de arranque.
- [ ] Pantallas poco usadas cargadas de forma diferida.
- [ ] Hermes activo (por defecto en Expo moderno).

## Memoria

- [ ] Limpiar listeners, timers y suscripciones en el cleanup de `useEffect`.
- [ ] Cancelar peticiones al desmontar (Axios `AbortController` / TanStack Query lo gestiona).
- [ ] Sin cierres que retengan datos grandes.

## Red

- [ ] Una sola instancia de Axios con interceptors; reintentos acotados.
- [ ] Prefetch de la siguiente pantalla probable (`queryClient.prefetchQuery`).
- [ ] Persistir caché de Query (AsyncStorage/MMKV) si la pantalla debe abrir al instante.
