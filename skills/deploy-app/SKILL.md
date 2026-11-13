# Skill: Deploy App

## Objetivo

Desplegar una app en un servidor propio con pm2 de forma repetible, reversible y registrada, o incorporar una app nueva a ese flujo. Sirve igual para una persona o para un agente.

## Antes de empezar

1. Leer `docs/deployment.md`.
2. Confirmar acceso autorizado al servidor: destino, usuario y llave. Esta skill no concede acceso.
3. Confirmar que el usuario pidió desplegar **esa** app. `--dry-run` no requiere pedido adicional.

## A. Desplegar una app ya configurada

1. Verificar en local que el cambio está en la rama de deploy del remoto (normalmente `main`):
   ```bash
   git fetch origin && git log --oneline -3 origin/main
   ```
   Si el cambio está solo en una rama feature, pedir al usuario que lo mergee en GitHub. No desplegar ramas feature.
2. Revisar si el cambio trae variables de entorno nuevas. Si las trae, confirmar que están en `ecosystem.config.cjs` (cuando hay `ENV_KEYS`) y cargarlas en el `.env` del servidor **antes** del deploy, con backup del `.env`.
3. Simular:
   ```bash
   deploy <app> --dry-run
   ```
4. Si la simulación no reporta errores, desplegar:
   ```bash
   deploy <app>
   ```
5. Si cambió un contrato entre servicios, desplegar primero al receptor y después al emisor. Si cambia el esquema de un subgraph GraphQL, reiniciar el gateway (`POST_RELOAD` en el `.conf`).
6. Verificar el comportamiento real del cambio, no solo el chequeo de salud. Si no se puede verificar, decirlo.
7. Reportar: commit anterior → nuevo, resultado de salud, variables tocadas (solo nombres) y lo pendiente de verificar.

## B. Incorporar una app nueva al flujo

1. Confirmar que la app cumple los requisitos de `docs/deployment.md` §3: ruta de salud, `ecosystem.config.cjs`, lista blanca completa, lockfile y script `build`.
2. En el servidor, la app debe estar clonada en `~/apps/...`, en la rama de deploy, siguiendo a `origin/<rama>`, y **sin commits que no estén en el remoto**:
   ```bash
   git status -sb
   git log --oneline origin/main..HEAD   # debe salir vacío
   ```
   Si hay commits solo en el servidor, detenerse y pedir al usuario que los lleve a GitHub o los descarte.
3. Si es la primera vez en ese servidor, instalar el script:
   ```bash
   mkdir -p ~/bin ~/deploy/apps ~/logs
   cp skills/deploy-app/assets/deploy ~/bin/deploy && chmod 755 ~/bin/deploy
   ```
4. Crear `~/deploy/apps/<app>.conf` a partir de `assets/app.conf.example`.
5. Probar:
   ```bash
   deploy --list
   deploy <app> --dry-run
   deploy <app> --force       # recompila el mismo commit para validar build, reload y salud
   ```
6. Si la app es nueva en pm2: `pm2 start ecosystem.config.cjs && pm2 save`. Si es nueva en nginx: agregar el `server {}`, ejecutar `sudo nginx -t` y recargar.

## Si algo falla

- `deploy` vuelve solo al commit anterior. Leer el motivo en la salida y en `~/logs/deploy.log`.
- Logs de la app: `pm2 logs <pm2-name> --lines 50 --nostream`.
- No reintentar con cambios manuales en el servidor. Corregir en el repo, mergear y volver a desplegar.

## Prohibido sin autorización explícita

- mergear, hacer `reset` o `push --force` en el servidor;
- desplegar ramas feature en producción;
- editar `.env` sin backup o mostrar sus valores;
- reiniciar servicios que no son parte del deploy pedido;
- migrar, borrar o transformar datos (ver `docs/deployment.md` §6).

Esta skill concede un procedimiento, no autorización para desplegar.
