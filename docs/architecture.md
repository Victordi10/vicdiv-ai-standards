# Architecture Standards

## 1. Modelo arquitectónico

La preferencia es una arquitectura **modular / feature-based con capas internas pragmáticas**.

No es Clean Architecture estricta ni Hexagonal Architecture estricta. Las capas se crean cuando aportan una responsabilidad útil.

## 2. Dominio

Preferir organización por dominio/feature:

```text
users/
products/
orders/
auth/
```

Los dominios pueden compartir código cuando exista una relación real. No es obligatorio aislarlos artificialmente.

## 3. Capas típicas

Backend:

```text
Route
↓
Controller
↓
DTO / Validation
↓
Service
↓
Repository
↓
Model
↓
Database
```

Frontend:

```text
Page / Screen
↓
Domain Components
↓
Store / Query
↓
Service
↓
API
```

No todos los dominios requieren todas las capas.

## 4. Arquitecturas soportadas

### Monolito

Todo puede vivir en una aplicación, pero la modularidad por dominio debe mantenerse.

### Monolito modular

Aplicar límites de módulo más estrictos.

### Monorepo

```text
apps/
packages/
```

Cada app puede tener un `AGENTS.md` propio.

### Frontend + backend separados

El frontend y backend pueden tener sus propios estándares locales, heredando las reglas generales.

### Backend compartido por múltiples clientes

Preferir NestJS cuando el backend sirva web, mobile, admin u otros clientes.

### Arquitectura distribuida / microservicios

Cada servicio puede tener su propio `AGENTS.md`. No asumir que todos los servicios usan exactamente el mismo framework o base de datos.

## 5. Regla de decisión

La arquitectura no debe anticipar complejidad hipotética.

Preferir la solución más simple que satisfaga las necesidades actuales y permita evolución razonable.
