# Component Standards

## Antes de crear

Buscar primero:

- `components/ui/`;
- componentes del dominio;
- hooks;
- stores;
- providers;
- `lib`;
- librerías ya instaladas.

## Componentes propios

Preferir componentes propios para piezas frecuentes y sencillas:

```text
Button
Input
Select
Modal
Drawer
Card
Badge
Spinner
```

## Librerías

Es válido utilizar librerías para componentes complejos donde reinventarlos no aporte valor:

- tablas avanzadas;
- editores;
- date pickers;
- visualizaciones;
- componentes accesibles complejos.

## Reutilización

No duplicar comportamiento por una diferencia menor. Preferir composición y configuración cuando resulte claro.

## Abstracción

Crear una nueva abstracción cuando:

- existe una responsabilidad clara;
- el comportamiento se repite de manera significativa;
- mantener copias separadas generaría inconsistencias;
- la abstracción reduce complejidad.

No crear abstracciones genéricas sin necesidad real.
