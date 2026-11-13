# Deployment Standards

Reglas para desplegar aplicaciones en un servidor propio (VPS) con Node.js, pm2 y nginx. Aplican igual cuando despliega una persona o un agente de IA.

El procedimiento paso a paso está en `skills/deploy-app/SKILL.md`. El script y la plantilla de configuración están en `skills/deploy-app/assets/`.

---

## 1. Principios

1. **GitHub es la fuente de verdad.** El servidor ejecuta exactamente lo que está en la rama de deploy del remoto, nunca código que solo existe en el servidor.
2. **El servidor nunca mergea.** Solo avanza en *fast-forward* (`git merge --ff-only`). Los merges se hacen en GitHub mediante pull request.
3. **Una rama de deploy por app**, normalmente `main`. Las ramas feature no se despliegan directamente en producción.
4. **Un solo comando de deploy para todas las apps.** Lo que cambia entre apps vive en un archivo de configuración, no en la memoria de quien despliega.
5. **Todo deploy es reversible.** Si fallan la compilación, la recarga o el chequeo de salud, se vuelve automáticamente al commit anterior.
6. **Fallar fuerte, no en silencio.** Una variable de entorno obligatoria que falta debe detener el arranque o el deploy, no apagar una función sin avisar.
7. **Cada deploy queda registrado:** fecha, app, commit anterior, commit nuevo y resultado.

---

## 2. Estructura del servidor

```text
~/apps/<grupo>/<app>/      clones de los repos (uno por app)
~/bin/deploy               script de deploy (común a todas las apps)
~/deploy/apps/<app>.conf   configuración de deploy de cada app
~/logs/deploy.log          historial de deploys
~/backups/                 dumps y copias antes de cambios de datos
```

- nginx escucha en `:80` con un `server {}` por subdominio y hace proxy a `127.0.0.1:<puerto>`. Si hay Cloudflare delante, Cloudflare termina TLS.
- Las apps escuchan solo en `127.0.0.1`, nunca en `0.0.0.0`.
- pm2 administra los procesos. `pm2 save` después de agregar o quitar una app, y `pm2 startup` configurado para sobrevivir reinicios.

---

## 3. Requisitos de cada app desplegable

Antes de configurar el deploy de una app, la app debe cumplir:

- **Ruta de salud** que responda 200 sin autenticación cuando la app está lista: `GET /health` en APIs. En una web sin ruta de salud se puede usar una página pública liviana.
- **`ecosystem.config.cjs`** en el repo con el nombre del proceso pm2, el puerto y el `cwd`.
- **Lista blanca de variables:** si `ecosystem.config.cjs` filtra variables (`ENV_KEYS`), toda variable nueva que lea el código debe agregarse ahí **en el mismo commit** que la usa. Si no, pm2 no la pasa y la función queda apagada sin error.
- **`.env.example`** actualizado con cada variable nueva: nombre, sin valor real, y un comentario de para qué sirve.
- **Lockfile versionado** (`pnpm-lock.yaml` o `package-lock.json`). El deploy instala con `--frozen-lockfile` / `npm ci`.
- **Script `build`** en `package.json` que deje la app lista para `pm2`.

---

## 4. Variables de entorno y secretos

- Los `.env` viven solo en el servidor, con permisos `600`. Nunca se versionan. Si un `.env` aparece versionado, sacarlo del repo y rotar los secretos que contenía.
- Antes de editar un `.env`, copiarlo como `.env.bak-<fecha>`.
- Nunca imprimir secretos en logs, chats, commits ni memoria de agentes. Al mostrar una URI, enmascarar las credenciales (`mongodb+srv://***@host/db`).
- Las variables `NEXT_PUBLIC_*` se incrustan **al compilar**: cambiarlas exige recompilar, no solo reiniciar.
- Un mismo secreto compartido entre servicios (por ejemplo, un token de eventos) se genera una sola vez y se copia igual en todos.
- Los secretos generados usan `openssl rand -hex 32` o equivalente.

---

## 5. Flujo de deploy

```text
PR mergeado a main en GitHub
  → deploy <app> --dry-run     (qué commits entran, chequeos)
  → deploy <app>               (ff, install, build, reload, health)
  → verificación funcional     (probar el cambio real)
```

El script `deploy`:

1. Bloquea deploys simultáneos.
2. Se niega a continuar si el servidor no está en la rama de deploy, si hay archivos versionados modificados a mano o si el servidor tiene commits que no están en el remoto.
3. Lista los commits que entran.
4. Instala dependencias solo si cambió `package.json` o el lockfile.
5. Detiene el deploy si el código nuevo lee una variable que la lista blanca de `ecosystem.config.cjs` no deja pasar; avisa si la variable no está en `.env`.
6. Compila con un límite de memoria (`BUILD_MEMORY_MB`).
7. Recarga pm2 y espera a que la ruta de salud responda.
8. Si algo falla, vuelve al commit anterior, recompila y recarga.
9. Registra el resultado en `~/logs/deploy.log`.

Orden entre servicios: desplegar primero quien **recibe** un contrato nuevo (API, receptor de eventos) y después quien lo **usa** (cliente, admin, emisor de eventos).

---

## 6. Cambios de datos

Un deploy que mueve o transforma datos (migración entre bases, índices únicos nuevos, renombre de colecciones) no se hace con `deploy`. Requiere:

1. Autorización explícita.
2. Backup previo de origen y destino en `~/backups/`.
3. Detener los procesos que escriben esos datos durante la ventana.
4. Verificación por conteos o consultas después de copiar.
5. Plan de vuelta atrás escrito antes de empezar.

Antes de desplegar código que crea un índice único, verificar que no existan duplicados: si existen, MongoDB no crea el índice y no avisa.

---

## 7. pm2 o Docker

- **pm2** administra procesos: los mantiene vivos, los reinicia, junta logs y los levanta tras un reboot.
- **Docker** empaqueta la app con su runtime y dependencias en una imagen reproducible.

Reglas de decisión:

- Servidor chico (≤ 2 GB de RAM) con varias apps Node: **pm2**. Docker suma consumo por contenedor y las imágenes ocupan disco.
- Se recomienda Docker cuando hay varios servidores, cuando las imágenes se construyen fuera del servidor (CI) o cuando las apps necesitan runtimes distintos.
- Servicios de infraestructura (bases de datos, colas) sí van en Docker aunque las apps usen pm2, limitados en memoria y expuestos solo en `127.0.0.1`.

---

## 8. Niveles de madurez

| Nivel | Qué se tiene | Siguiente paso |
|---|---|---|
| 0 | Deploy manual por SSH, distinto en cada app | Rama de deploy única por app |
| 1 | Rama única + `deploy <app>` con rollback | Compilar en CI (GitHub Actions) y copiar el artefacto al servidor |
| 2 | Build en CI, el servidor solo recarga | Deploy automático al mergear en `main` |
| 3 | Imágenes Docker publicadas en un registry | Orquestación (compose/k8s) cuando haya varios servidores |

Compilar en el servidor de producción consume la RAM de las apps en ejecución. Pasar al nivel 2 apenas compilar empiece a afectar a las apps vivas.

---

## 9. Reglas para agentes de IA

- Un agente puede ejecutar `deploy <app> --dry-run`, leer logs y consultar el estado de pm2 sin autorización adicional cuando ya tiene acceso autorizado al servidor.
- Desplegar (`deploy <app>`), editar `.env`, reiniciar servicios ajenos a la app o tocar datos requiere que el usuario lo haya pedido para esa app.
- Nunca mergear en el servidor, nunca hacer `git reset`/`push --force` manual, nunca saltear el chequeo de salud.
- Si `deploy` se niega (rama distinta, commits solo en el servidor, archivos modificados), reportar el motivo y la decisión necesaria; no forzar.
- Reportar siempre: commit anterior → commit nuevo, resultado del chequeo de salud y lo que **no** se verificó (por ejemplo, el flujo funcional real).
