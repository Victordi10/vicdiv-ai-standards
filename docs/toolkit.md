# Toolkit Standards

Utilidades canónicas del marco de trabajo. Este código se replica en los proyectos nuevos para mantener una base consistente y copiable.

Ubicación orientativa en el proyecto:

```text
lib/
├── logger.ts
├── api.ts
├── http.ts
└── format.ts
```

## Logger

`console` no se usa directamente. Todo log pasa por `logger`.

Reglas de uso:

- la lógica de negocio usa niveles según gravedad (`info`, `warn`, `error`);
- `debug` se usa en desarrollo para verificar que las cosas funcionan;
- en `catch`, registrar siempre el error con `logger.error`;
- nunca tragarse un error sin registrarlo.

```ts
type LogLevel = 'info' | 'warn' | 'error' | 'debug';

const isDevelopment = process.env.NODE_ENV === 'development';

function formatMessage(level: LogLevel, ...args: unknown[]): string {
  const timestamp = new Date().toISOString();
  const prefix = `[${timestamp}] [${level.toUpperCase()}]`;
  return `${prefix} ${args.map((arg) =>
    typeof arg === 'object' ? JSON.stringify(arg, null, 2) : String(arg)
  ).join(' ')}`;
}

export const logger = {
  info: (...args: unknown[]) => {
    if (isDevelopment) {
      console.log(formatMessage('info', ...args));
    }
  },

  warn: (...args: unknown[]) => {
    console.warn(formatMessage('warn', ...args));
  },

  error: (...args: unknown[]) => {
    console.error(formatMessage('error', ...args));
  },

  debug: (...args: unknown[]) => {
    if (isDevelopment) {
      console.log(formatMessage('debug', ...args));
    }
  },

  log: (level: LogLevel, ...args: unknown[]) => {
    switch (level) {
      case 'info':
        logger.info(...args);
        break;
      case 'warn':
        logger.warn(...args);
        break;
      case 'error':
        logger.error(...args);
        break;
      case 'debug':
        logger.debug(...args);
        break;
    }
  },
};

export default logger;
```

## Cliente HTTP (Axios)

Un único cliente centralizado con configuración común. El interceptor adjunta el token de autenticación cuando existe.

Reglas de uso:

- no crear múltiples clientes equivalentes por recurso;
- el token se lee de `localStorage` en cliente; en servidor no se envía de forma automática.

```ts
import axios, { type InternalAxiosRequestConfig } from 'axios';

const getBaseURL = () => {
  if (typeof window !== 'undefined') {
    return window.location.origin;
  }
  return process.env.NEXT_PUBLIC_HOST || 'http://localhost:3000';
};

const api = axios.create({
  baseURL: getBaseURL(),
  headers: {
    'Content-Type': 'application/json',
  },
  withCredentials: true,
});

api.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    if (typeof window !== 'undefined') {
      const token = localStorage.getItem('token');
      if (token) {
        config.headers.Authorization = `Bearer ${token}`;
      }
    }
    return config;
  },
  (error) => Promise.reject(error)
);

export default api;
```

## Respuestas HTTP (Next.js)

En Next.js, `route.ts` y controllers responden únicamente con estos helpers. No se construye `NextResponse.json` a mano.

```ts
import { NextResponse } from 'next/server';

export function errorResponse(message: string, statusCode: number = 500) {
  return NextResponse.json(
    { success: false, message },
    {
      status: statusCode,
      headers: {
        'Content-Type': 'application/json; charset=utf-8',
      },
    }
  );
}

export function successResponse(
  message: string = 'Operación exitosa',
  data: unknown = {},
  statusCode: number = 200
) {
  return NextResponse.json(
    { success: true, message, data },
    {
      status: statusCode,
      headers: {
        'Content-Type': 'application/json; charset=utf-8',
      },
    }
  );
}
```

## Formato de moneda

```ts
export function formatCurrency(value: number): string {
  return new Intl.NumberFormat('es-CO', {
    style: 'currency',
    currency: 'COP',
    maximumFractionDigits: 0,
  }).format(value);
}
```

## Manejo de errores y asincronía

Estándar:

- usar `async/await` siempre, nunca `.then/.catch` encadenados salvo necesidad puntual;
- operaciones que pueden fallar van en `try/catch`;
- en `catch`: registrar con `logger.error` y manejar (devolver respuesta de error o rethrow) — no tragarse el error;
- la lógica de negocio avisa con `logger.log`/niveles; el detalle de depuración va en `debug`;
- los servicios devuelven resultados tipados; los errores esperables se resuelven en el controller/route con `errorResponse`.