# Sesión 4 — Paso a Producción (Deployment)

## ¿Qué vamos a hacer en esta sesión?

**Duración**: 1 hora
**Lo que vas a lograr**: Tu aplicación va a vivir en internet con dos URLs públicas que cualquier persona en el mundo puede abrir.

Al terminar:

- Tu backend va a estar en **Render**: `https://tu-app-tareas-api.onrender.com`
- Tu frontend va a estar en **Vercel**: `https://tu-app-tareas.vercel.app`
- Ambos van a estar conectados a tu repositorio de GitHub. Cada vez que hagas `git push`, se actualizan automáticamente.

---

## Parte 1 — ¿Qué es "desplegar a producción"? (5 min)

Hasta ahora tu app vive en TU computadora. Si la apagas, se acaba. Si tu amigo en otra ciudad quiere usarla, no puede.

**Desplegar a producción** significa subir tu código a un servidor que esté siempre prendido y conectado a internet. Cuando alguien escribe la URL en su navegador, el servidor responde con tu app.

### Render vs Vercel vs otros

Hay muchas opciones (AWS, Google Cloud, DigitalOcean, Heroku, Fly.io, etc). Para principiantes hay 2 que destacan por su capa gratuita:

- **Render**: bueno para backends (Node.js, NestJS, Express, Python, Go...). Tiene SSL, deploy automático desde GitHub.
- **Vercel**: bueno para frontends (React, Next.js, Vue, Svelte). Inventado por los creadores de Next.js. CDN global, deploy ultrarrápido.

Usaremos los dos.

!!! info "Nota sobre la capa gratuita de Render"
    En el plan gratuito de Render, tu backend "duerme" después de 15 minutos sin uso. La primera petición después de dormir tarda unos 30-60 segundos en responder (mientras se "despierta"). Para un workshop está perfecto, pero para producción real usarías un plan pagado o un servicio sin sleep.

---

## Parte 2 — Preparar el backend para producción (15 min)

Antes de subir nada, necesitamos hacer 3 cambios al backend.

### Cambio 1: Configurar el script de start para producción

Abre `backend/package.json`. Busca la sección `"scripts"`. Debe verse así:

```json
"scripts": {
  "build": "nest build",
  "format": "prettier --write \"src/**/*.ts\" \"test/**/*.ts\"",
  "start": "nest start",
  "start:dev": "nest start --watch",
  "start:debug": "nest start --debug --watch",
  "start:prod": "node dist/main",
  "lint": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix",
  "test": "jest"
}
```

Lo que nos importa para producción:

- `npm run build`: compila el código TypeScript a JavaScript (deja todo en la carpeta `dist/`).
- `npm run start:prod`: ejecuta el código ya compilado.

Estos dos comandos los va a usar Render automáticamente.

### Cambio 2: CORS dinámico con variable de entorno

En desarrollo, el frontend está en `localhost:5173`. En producción va a estar en algo como `https://tu-app-tareas.vercel.app`. Necesitamos que CORS acepte ambos.

Abre `backend/src/main.ts` y reemplaza por:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const allowedOrigins = process.env.ALLOWED_ORIGINS
    ? process.env.ALLOWED_ORIGINS.split(',')
    : ['http://localhost:5173', 'http://localhost:3000'];

  app.enableCors({
    origin: allowedOrigins,
    methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  });

  const port = process.env.PORT ?? 3000;
  await app.listen(port);
  console.log(`Backend corriendo en puerto ${port}`);
}
bootstrap();
```

**Lo que hicimos**:

- Leemos `ALLOWED_ORIGINS` de las variables de entorno.
- Si existe, separamos por coma. Permite configurar varias URLs en Render.
- Si no existe (en desarrollo local), usamos los de localhost.

### Cambio 3: Verificar que el build funciona localmente

En la terminal, dentro de `backend/`:

```bash
npm run build
```

Debe terminar sin errores. Vas a ver una nueva carpeta `dist/` con los archivos JavaScript compilados.

Prueba que el build corre:

```bash
npm run start:prod
```

Debe arrancar igual que `npm run start:dev`. Detén con `Ctrl+C`.

!!! danger "Si el build falla con errores de TypeScript"
    Lee el error. TypeScript es estricto. Si te dice "Property 'X' is missing in type 'Y'", probablemente olvidaste devolver alguna propiedad en algún método. Corrígelo antes de seguir.

### Commit de los cambios

```bash
cd ..
git add .
git commit -m "feat: configurar CORS dinamico para produccion"
git push
```

---

## Parte 3 — Desplegar el backend en Render (15 min)

### Crear cuenta en Render

1. Ve a [https://render.com](https://render.com).
2. Haz clic en **"Get Started"** o **"Sign Up"**.
3. Regístrate con **GitHub** (te ahorra crear contraseña nueva y conecta tu cuenta).
4. Acepta los permisos.

### Crear un nuevo Web Service

1. En el dashboard de Render, haz clic en **"+ Add new"** → **"Web Service"**.
2. Te va a pedir conectar un repositorio. Selecciona **"Connect a repository"**.
3. Busca **`workshop-tareas`** y haz clic en **"Connect"**.

!!! tip "Si no ves tu repo en la lista"
    Probablemente Render no tiene permiso para verlo. Haz clic en **"Configure GitHub App"**, autoriza Render para el repositorio específico, y vuelve.

### Configurar el servicio

Vas a llenar un formulario. Pon estos valores:

| Campo | Valor |
|-------|-------|
| **Name** | `workshop-tareas-api` (será parte de la URL) |
| **Region** | Cualquiera (Oregon es el por defecto) |
| **Branch** | `main` |
| **Root Directory** | `backend` |
| **Runtime** | `Node` |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm run start:prod` |
| **Plan** | **Free** (importante) |

!!! warning "Root Directory es crítico"
    Tu repo tiene `backend/` y `frontend/`. Si no especificas que el código está en `backend/`, Render se va a confundir.

### Agregar variables de entorno

Aún en el mismo formulario, busca la sección **"Environment Variables"**. Agrega esta:

- **Key**: `ALLOWED_ORIGINS`
- **Value**: por ahora pon `http://localhost:5173` (vamos a actualizarlo cuando tengamos la URL de Vercel).

No agregues `PORT`, Render lo asigna automáticamente.

### Crear el servicio

Haz clic en **"Create Web Service"** al final del formulario.

Render va a:

1. Clonar tu repo.
2. Instalar dependencias (`npm install`).
3. Compilar (`npm run build`).
4. Arrancar (`npm run start:prod`).

Esto tarda **3-5 minutos**. Vas a ver logs en vivo. Si todo sale bien, al final verás:

```
==> Your service is live 🎉
==> Available at your primary URL https://workshop-tareas-api.onrender.com
```

!!! danger "Si el deploy falla"
    Lee los logs. Los errores más comunes son:
    
    - **"npm install failed"**: alguna dependencia tiene problemas. Borra `package-lock.json` localmente, vuelve a hacer `npm install`, commit y push.
    - **"Cannot find module"**: probablemente un import roto. Búscalo en los logs y corrígelo.
    - **"Port already in use"**: olvidaste usar `process.env.PORT` en `main.ts`. Vuelve a revisar.

### Probar el backend en producción

Copia la URL que te dio Render (algo como `https://workshop-tareas-api.onrender.com`).

Abre Thunder Client y haz un GET a:

```
https://workshop-tareas-api.onrender.com/tasks
```

La primera petición puede tardar 30-60 segundos (Render despierta el servicio). Después es instantáneo.

Debes recibir `[]` (array vacío, porque es una instancia nueva).

Prueba POST:

```
POST https://workshop-tareas-api.onrender.com/tasks
Body: {"title": "Mi primera tarea en producción"}
```

Si te devuelve la tarea creada, ¡tu backend está vivo en internet!

---

## Parte 4 — Desplegar el frontend en Vercel (15 min)

### Antes de desplegar: configurar la URL del backend en el frontend

En `frontend/`, crea un archivo `.env.production`:

```
VITE_API_URL=https://workshop-tareas-api.onrender.com
```

(reemplaza con TU URL real de Render).

!!! info "Cómo Vite maneja las variables de entorno"
    Vite tiene archivos especiales:
    
    - `.env` → siempre cargado.
    - `.env.local` → cargado solo en local, ignorado por Git.
    - `.env.production` → cargado en producción.
    - `.env.development` → cargado en desarrollo.
    
    Y solo expone las variables que empiezan con `VITE_` (esto es para que no expongas secretos por accidente).

Verifica también que `frontend/.gitignore` NO ignore `.env.production` (sí debe ignorar `.env.local`).

Commit:

```bash
cd ..
git add .
git commit -m "config: agregar URL del backend en produccion"
git push
```

### Crear cuenta en Vercel

1. Ve a [https://vercel.com](https://vercel.com).
2. Haz clic en **"Sign Up"**.
3. Selecciona **"Continue with GitHub"**.
4. Autoriza Vercel.

### Importar el proyecto

1. En el dashboard de Vercel, haz clic en **"Add New..."** → **"Project"**.
2. Busca **`workshop-tareas`** y haz clic en **"Import"**.

### Configurar el proyecto

Vercel te muestra un formulario:

| Campo | Valor |
|-------|-------|
| **Project Name** | `workshop-tareas` (será parte de la URL) |
| **Framework Preset** | `Vite` (debería detectarlo solo) |
| **Root Directory** | Haz clic en **"Edit"** y selecciona `frontend` |
| **Build Command** | `npm run build` (auto-detectado) |
| **Output Directory** | `dist` (auto-detectado) |
| **Install Command** | `npm install` (auto-detectado) |

### Agregar variables de entorno

Expande la sección **"Environment Variables"** y agrega:

- **Key**: `VITE_API_URL`
- **Value**: `https://workshop-tareas-api.onrender.com` (tu URL real de Render).

### Deploy

Haz clic en **"Deploy"**.

Vercel:

1. Clona tu repo.
2. Instala dependencias.
3. Ejecuta `npm run build`.
4. Sube los archivos estáticos a su CDN global.

Tarda **1-2 minutos**. Mucho más rápido que Render porque el frontend es solo archivos estáticos.

Al terminar verás "🎉 Congratulations!" y la URL de tu app, algo como:

```
https://workshop-tareas-tu-usuario.vercel.app
```

---

## Parte 5 — Conectar todo (10 min)

Si abres tu app en la URL de Vercel, **probablemente NO funcione todavía**. Vas a ver "No se pudieron cargar las tareas" o un error de CORS.

Esto es porque tu backend en Render NO tiene autorizada la URL de Vercel. Vamos a arreglarlo.

### Actualizar ALLOWED_ORIGINS en Render

1. Ve al dashboard de Render.
2. Entra a tu servicio `workshop-tareas-api`.
3. En el menú lateral, haz clic en **"Environment"**.
4. Edita la variable `ALLOWED_ORIGINS`:
   - Cambia el valor a: `http://localhost:5173,https://workshop-tareas-tu-usuario.vercel.app`
   - Sin espacios entre las URLs, separadas solo por coma.
5. Haz clic en **"Save Changes"**.

Render automáticamente re-despliega el servicio. Espera 1-2 minutos.

### Probar la app en producción

Abre tu URL de Vercel en el navegador. Crea una tarea. Marca otra como completada.

!!! success "Estás en producción"
    Tu aplicación está en internet. Cualquier persona con la URL puede usarla. Cópiala y mándala a un amigo.

### Probar el flujo de actualización automática

Vamos a comprobar que cuando hagas un cambio en tu código, automáticamente se despliega.

Abre `frontend/src/App.tsx`. Cambia el título:

```typescript
<h1>Mis Tareas - Workshop Fullstack</h1>
```

Commit y push:

```bash
git add .
git commit -m "ui: actualizar titulo principal"
git push
```

Ve al dashboard de Vercel. Vas a ver un nuevo deploy en progreso. En 1-2 minutos, refresca tu URL de Vercel: vas a ver el título actualizado.

**Esto se llama CI/CD (Continuous Integration / Continuous Deployment)**. Es uno de los conceptos más importantes del desarrollo moderno.

---

## Parte 6 — Custom domain (opcional, 5 min)

Si tienes un dominio (por ejemplo `tusitio.com`), puedes apuntarlo a Vercel.

1. En Vercel, ve a tu proyecto → **Settings** → **Domains**.
2. Agrega tu dominio.
3. Vercel te dice qué registros DNS configurar en tu proveedor (Namecheap, GoDaddy, etc).
4. Después de unos minutos (a veces horas), tu app responde en tu dominio personalizado.

Esto NO es necesario para el workshop, solo es información.

---

## Resumen final de lo que aprendiste en todo el workshop

| Concepto | Sesión |
|----------|--------|
| Git: clone, commit, push, pull | Sesión 1 |
| GitHub: crear repos, clonar | Sesión 1 |
| NestJS: módulos, controladores, servicios | Sesión 2 |
| REST APIs: GET, POST, PATCH | Sesión 2 |
| CORS y por qué existe | Sesión 2 y 4 |
| React: componentes, useState, useEffect | Sesión 3 |
| Consumo de APIs con fetch | Sesión 3 |
| Variables de entorno (Vite) | Sesión 3 y 4 |
| Deployment de backend (Render) | Sesión 4 |
| Deployment de frontend (Vercel) | Sesión 4 |
| CI/CD automático con GitHub | Sesión 4 |

Esto es básicamente el **flujo completo de cualquier proyecto web moderno**. Lo demás (bases de datos, autenticación, tests, monitoreo) son capas que se agregan sobre esta base.

---

## ¿Y ahora qué? Próximos pasos sugeridos

Si terminaste el workshop con tiempo, o quieres seguir aprendiendo en casa, estos son los siguientes temas naturales:

### 1. Persistencia con base de datos

Reemplaza el array en memoria por una base de datos real. Sugerencia: **PostgreSQL** con **TypeORM** o **Prisma**. Render te da PostgreSQL gratis.

### 2. Validación de datos

Instala `class-validator` y `class-transformer` en NestJS. Crea DTOs (Data Transfer Objects) y deja que NestJS valide automáticamente los bodies de entrada.

### 3. Autenticación

Agrega login con JWT. Las tareas serían por usuario en lugar de globales.

### 4. Mejor UI

Aprende **Tailwind CSS** o **shadcn/ui**. Tu app se va a ver 10 veces más profesional.

### 5. Tests

Aprende a escribir tests con **Jest** (NestJS los trae) y **Vitest** (para el frontend).

### 6. TypeScript avanzado

Tipos genéricos, utility types (Partial, Pick, Omit), discriminated unions.

---

## Problemas frecuentes en deployment

??? question "Render dice 'Build failed' con un error de tipos"
    En desarrollo TypeScript a veces deja pasar cosas que en producción falla. Ejecuta `npm run build` localmente ANTES de pushear para detectar errores.

??? question "Vercel deploya pero la app dice 'Cannot read property of undefined'"
    Casi siempre es que `VITE_API_URL` no está configurada en Vercel. Ve a Settings → Environment Variables.

??? question "El backend en Render funciona pero el frontend en Vercel da CORS"
    `ALLOWED_ORIGINS` en Render no incluye la URL exacta de Vercel. Cópiala exactamente (con `https://` y sin slash al final).

??? question "Cada vez que entro a la app tarda 30 segundos"
    Es Render. En el plan gratis, el servicio "duerme" después de 15 minutos sin uso. La primera petición lo despierta. Para evitarlo necesitas el plan pagado o usar un servicio como Fly.io o Railway con plan gratuito sin sleep.

??? question "Hice push pero Vercel/Render no se actualizan"
    Ve al dashboard. Probablemente el deploy falló (mira los logs). Si el commit no aparece, verifica que pusheaste a la rama `main`.

??? question "Quiero borrar todo y volver a empezar"
    En Render y Vercel puedes eliminar los servicios desde Settings → Delete Project. Tu repo de GitHub queda intacto.

---

## Checklist final

- [x] Tu backend está corriendo en una URL pública de Render.
- [x] Tu frontend está corriendo en una URL pública de Vercel.
- [x] Puedes crear, ver y completar tareas desde la URL de producción.
- [x] Cada `git push` redespliega automáticamente.

---

## Felicidades

Acabas de completar tu primera aplicación fullstack desplegada en internet. Esto es algo que muchos desarrolladores tardan meses en lograr por sí mismos.

**Comparte tu URL con la comunidad**: ponla en tu CV, en LinkedIn, en tu portafolio. No es solo "una app de tareas", es la evidencia de que sabes cómo funciona un proyecto web real de principio a fin.

Si quieres seguir aprendiendo, revisa la página de [Referencias](referencias.md) con recursos para profundizar.
