# UX Patterns (React Native / Expo)

## 1. Teclado

- Preferir `react-native-keyboard-controller`: `KeyboardAwareScrollView` hace scroll automático hasta el input enfocado y funciona igual en iOS y Android.
- Fallback sin dependencia: `KeyboardAvoidingView` con `behavior="padding"` en iOS y `"height"` en Android (probar en ambos).
- En el `ScrollView` del formulario:
  - `keyboardShouldPersistTaps="handled"` (los botones responden con el teclado abierto);
  - `keyboardDismissMode="interactive"` en iOS u `"on-drag"` en Android.
- Botón principal fijo sobre el teclado: usar `KeyboardStickyView` (keyboard-controller) o `KeyboardAvoidingView`.

```tsx
<KeyboardAwareScrollView
  bottomOffset={24}
  keyboardShouldPersistTaps="handled"
  contentContainerStyle={{ padding: spacing.md, gap: spacing.md }}
>
  {/* inputs */}
</KeyboardAwareScrollView>
```

## 2. Formularios

- `returnKeyType="next"` + `onSubmitEditing` que enfoca el siguiente input (`ref`); `"done"` en el último.
- `inputMode` / `keyboardType` correctos (`email-address`, `numeric`, `phone-pad`).
- `autoComplete`, `textContentType` y `autoCapitalize="none"` en email/password/OTP.
- Validar al salir del campo (`onBlur`) y al enviar, no en cada tecla.
- Deshabilitar el submit y mostrar spinner mientras la mutación está pendiente (evita doble envío).
- Error visible junto al campo, con `accessibilityLiveRegion="polite"`.

## 3. Pull to refresh

`isFetching` de TanStack Query es `true` también en refetch de fondo y provoca parpadeo. Usar estado manual:

```tsx
const [refreshing, setRefreshing] = useState(false);

const onRefresh = useCallback(async () => {
  setRefreshing(true);
  try {
    await refetch();
  } finally {
    setRefreshing(false);
  }
}, [refetch]);

<FlashList
  refreshControl={<RefreshControl refreshing={refreshing} onRefresh={onRefresh} />}
/>
```

## 4. Listas

- Scroll infinito: `useInfiniteQuery` + `onEndReached` con `onEndReachedThreshold={0.5}` y `ListFooterComponent` con spinner solo si `isFetchingNextPage`.
- `ListEmptyComponent` con mensaje y acción; skeletons mientras carga la primera página.
- `keyExtractor` estable (id del dominio, nunca el índice).
- Botón "volver arriba" cuando el scroll supera ~2 pantallas (`scrollToOffset`).
- Mantener posición de scroll al volver (no desmontar la lista al navegar; stack navigator ya la conserva).

## 5. Feedback inmediato

- `Pressable` con estilo `pressed` (opacidad o escala) en todo elemento táctil.
- Optimistic updates para likes, toggles, favoritos y checks; rollback + toast si falla.
- Háptico (`expo-haptics`) en acciones clave: éxito, error, selección. Sin abusar.
- Toasts para resultados no bloqueantes; modales solo para decisiones.

## 6. Estados de pantalla

| Estado | Mostrar |
|--------|---------|
| Loading inicial | Skeleton con la forma del contenido |
| Refetch | Contenido previo + RefreshControl |
| Empty | Ilustración/texto + acción primaria |
| Error | Mensaje claro + botón "Reintentar" |
| Offline | Banner persistente (`@react-native-community/netinfo`) con datos en caché |

## 7. Navegación y transiciones

- Pantalla visible al instante: renderizar el esqueleto y diferir lo pesado.
- Gesto de volver nativo habilitado; confirmar solo si hay cambios sin guardar.
- Splash (`expo-splash-screen`) retenido hasta tener fuentes, tema y sesión listos.
- Animar entradas con `entering`/`exiting` de Reanimated, breves (150–300 ms).

## 8. Safe area y plataforma

- `react-native-safe-area-context` (`SafeAreaView` o `useSafeAreaInsets`) en toda pantalla; no hardcodear alturas de notch.
- `StatusBar` acorde al tema (claro/oscuro).
- Respetar tamaño de fuente del sistema (no desactivar `allowFontScaling` salvo casos justificados).

## 9. Accesibilidad

- `accessibilityRole`, `accessibilityLabel` en botones solo-icono.
- Contraste suficiente en ambos temas.
- Estados `disabled` / `selected` expuestos en `accessibilityState`.
