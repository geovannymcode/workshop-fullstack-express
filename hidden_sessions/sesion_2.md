# Sesión 2 — Construcción del API (Backend con NestJS)

## ¿Qué vamos a hacer en esta sesión?

**Duración**: 1 hora
**Lo que vas a lograr**: Un API REST funcional con 2 endpoints (`GET` y `POST`) corriendo en tu computadora. Vas a poder probarlo con Thunder Client y ver respuestas JSON reales.

Al terminar, tu carpeta `backend/` estará lista, con código que:

- Devuelve la lista de tareas cuando alguien le pregunte.
- Crea una tarea nueva cuando alguien le mande los datos.
- Funciona con CORS habilitado para que el frontend pueda hablarle.

---

## Parte 1 — ¿Qué es NestJS y por qué lo usamos? (5 min)

**NestJS** es un framework para construir aplicaciones de backend en Node.js. Está escrito en TypeScript y tiene una estructura muy clara, parecida a Angular o Spring (en el mundo Java).

### ¿Por qué NestJS y no Express puro?

Express es excelente, pero te deja organizar el código como quieras. Para un proyecto pequeño eso es genial. Para un proyecto que crece, se vuelve un desorden.

NestJS te impone una estructura desde el día uno:

- **Módulos**: agrupan funcionalidad relacionada. Por ejemplo, todo lo de "Tareas" vive en un módulo.
- **Controladores**: reciben las peticiones HTTP y deciden qué hacer.
- **Servicios**: contienen la lógica de negocio (la parte importante).

Esta separación se llama **arquitectura en capas** y la vas a ver en proyectos serios de cualquier empresa.

### Anatomía de una petición

Cuando alguien hace `GET /tasks`:

```
Petición HTTP → Controlador (recibe) → Servicio (procesa) → Devuelve datos al Controlador → Respuesta HTTP
```

Esta separación parece innecesaria para algo tan simple. Es como tener un asistente que recibe el teléfono, pasa el mensaje a otro asistente que sabe lo que hacer, y este responde. Pero cuando el proyecto crece (y vas a meter base de datos, autenticación, validaciones), te lo agradeces.

---

## Parte 2 — Instalar NestJS y crear el proyecto (10 min)

### Instalar el CLI de NestJS

El CLI (Command Line Interface) de NestJS te crea proyectos con toda la estructura lista.

En la terminal de VS Code, dentro de la carpeta `workshop-tareas`:

```bash
npm install -g @nestjs/cli
```

- `npm install` descarga e instala un paquete de Node.
- `-g` significa "global", se instala para todo tu sistema, no solo este proyecto.
- `@nestjs/cli` es el nombre del paquete.

Verifica que se instaló:

```bash
nest --version
```

Debe responder algo como `11.0.0` (o superior).

!!! danger "Si dice 'nest no es un comando reconocido'"
    En algunos sistemas, los paquetes globales de npm no se agregan automáticamente al PATH. Cierra la terminal, abre una nueva y vuelve a probar. Si sigue fallando, ejecuta:
    
    ```bash
    npm config get prefix
    ```
    
    Te dará una ruta. Agrega esa ruta (más `/bin` en Mac/Linux, o nada en Windows) al PATH de tu sistema.

### Crear el proyecto backend

Asegúrate de estar dentro de la carpeta `workshop-tareas`:

```bash
pwd
```

(En Windows usa `cd` sin argumentos para ver dónde estás).

Crea el proyecto NestJS:

```bash
nest new backend
```

Te va a preguntar qué gestor de paquetes usar. Selecciona **npm** (es el más universal).

NestJS va a:

1. Crear una carpeta `backend/`.
2. Instalar todas las dependencias (esto tarda 1-3 minutos).
3. Hacer un commit inicial dentro de esa carpeta (lo vamos a deshacer porque no lo queremos).

### Eliminar el repo de Git que NestJS creó dentro

NestJS crea automáticamente un `.git` dentro de `backend/`. Esto es problemático porque ya tenemos uno en la raíz. Vamos a borrarlo:

**En Linux/Mac**:

```bash
rm -rf backend/.git
```

**En Windows (PowerShell)**:

```powershell
Remove-Item -Recurse -Force backend\.git
```

**En Windows (CMD)**:

```cmd
rmdir /s /q backend\.git
```

### Probar que el proyecto corre

Entra a la carpeta del backend y arráncalo:

```bash
cd backend
npm run start:dev
```

Después de unos segundos verás algo así:

```
[Nest] 12345  - 21/05/2026, 10:00:00     LOG [NestFactory] Starting Nest application...
[Nest] 12345  - 21/05/2026, 10:00:00     LOG [InstanceLoader] AppModule dependencies initialized
[Nest] 12345  - 21/05/2026, 10:00:00     LOG [RoutesResolver] AppController {/}: 
[Nest] 12345  - 21/05/2026, 10:00:00     LOG [RouterExplorer] Mapped {/, GET} route 
[Nest] 12345  - 21/05/2026, 10:00:00     LOG [NestApplication] Nest application successfully started
```

Abre tu navegador y ve a [http://localhost:3000](http://localhost:3000). Debe responder con `Hello World!`.

!!! success "Tu backend está vivo"
    Acabas de levantar tu primer servidor con NestJS. Ese mensaje "Hello World!" lo manda un controlador que el CLI generó automáticamente. Vamos a verlo.

### Estructura que generó NestJS

En VS Code, expande la carpeta `backend/`. Lo importante está en `src/`:

```
backend/
├── src/
│   ├── app.controller.ts        ← El controlador "Hello World"
│   ├── app.controller.spec.ts   ← Test del controlador
│   ├── app.module.ts            ← Módulo raíz
│   ├── app.service.ts           ← El servicio que devuelve "Hello World!"
│   └── main.ts                  ← Punto de entrada de la app
├── package.json
└── ...
```

Abre `src/app.controller.ts`. Verás algo así:

```typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
```

Esto es un **controlador**:

- `@Controller()` le dice a NestJS "esto es un controlador".
- `@Get()` indica que el método `getHello()` responde a peticiones `GET /`.
- El controlador llama a un servicio (`appService.getHello()`).

Abre `src/app.service.ts`:

```typescript
import { Injectable } from '@nestjs/common';

@Injectable() 
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
```

Esto es un **servicio**. Tiene la lógica. En este caso simplemente devuelve un string.

Esta separación de Controlador y Servicio es el patrón básico de NestJS.

---

## Parte 3 — Crear el módulo de Tareas (15 min)

Vamos a generar un módulo, controlador y servicio para "Tareas".

Detén el servidor con `Ctrl+C` en la terminal. Luego ejecuta:

```bash
nest generate module tasks
```

Esto crea `src/tasks/tasks.module.ts`.

```bash
nest generate controller tasks --no-spec
```

Esto crea `src/tasks/tasks.controller.ts`. La bandera `--no-spec` evita crear el archivo de tests (para mantenerlo simple).

```bash
nest generate service tasks --no-spec
```

Esto crea `src/tasks/tasks.service.ts`.

### Ver lo que se generó

Tu estructura ahora debe verse así:

```
src/
├── app.controller.ts
├── app.controller.spec.ts
├── app.module.ts
├── app.service.ts
├── main.ts
└── tasks/
    ├── tasks.controller.ts
    ├── tasks.module.ts
    └── tasks.service.ts
```

Y NestJS automáticamente importó el `TasksModule` en `app.module.ts`. Ábrelo para confirmar:

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TasksModule } from './tasks/tasks.module';

@Module({
  imports: [TasksModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

### Definir el modelo de una Tarea

Antes de escribir lógica, definamos cómo se ve una tarea. Crea el archivo `src/tasks/task.model.ts`:

```typescript
export interface Task {
  id: string;
  title: string;
  completed: boolean;
  createdAt: Date;
}
```

Una interfaz en TypeScript es un contrato: dice "una Task tiene estas propiedades". No genera código en tiempo de ejecución, solo ayuda al editor a darte autocompletado y a detectar errores.

### Escribir el servicio (la lógica)

Abre `src/tasks/tasks.service.ts` y reemplaza TODO el contenido por:

```typescript
import { Injectable, NotFoundException } from '@nestjs/common';
import { Task } from './task.model';

@Injectable()
export class TasksService {
  private tasks: Task[] = [];

  findAll(): Task[] {
    return this.tasks;
  }

  create(title: string): Task {
    if (!title || title.trim().length === 0) {
      throw new Error('El título es obligatorio');
    }

    const newTask: Task = {
      id: this.generateId(),
      title: title.trim(),
      completed: false,
      createdAt: new Date(),
    };

    this.tasks.push(newTask);
    return newTask;
  }

  toggleComplete(id: string): Task {
    const task = this.tasks.find((t) => t.id === id);
    if (!task) {
      throw new NotFoundException(`Tarea con id ${id} no encontrada`);
    }
    task.completed = !task.completed;
    return task;
  }

  private generateId(): string {
    return Math.random().toString(36).substring(2, 11);
  }
}
```

**Lo que hace este servicio**:

- `tasks: Task[]` es un array que vive en memoria mientras el servidor esté corriendo. Cuando reinicies el servidor, se borra todo. En producción usarías una base de datos.
- `findAll()` devuelve todas las tareas.
- `create()` valida que venga un título y crea una tarea nueva.
- `toggleComplete()` cambia el estado completado/no completado.
- `generateId()` genera un id aleatorio (en producción usarías UUID o el ID que te da la base de datos).

### Escribir el controlador (la puerta de entrada HTTP)

Abre `src/tasks/tasks.controller.ts` y reemplaza TODO el contenido por:

```typescript
import {
  Body,
  Controller,
  Get,
  Patch,
  Param,
  Post,
  HttpCode,
  HttpStatus,
} from '@nestjs/common';
import { TasksService } from './tasks.service';
import { Task } from './task.model';

@Controller('tasks')
export class TasksController {
  constructor(private readonly tasksService: TasksService) {}

  @Get()
  findAll(): Task[] {
    return this.tasksService.findAll();
  }

  @Post()
  @HttpCode(HttpStatus.CREATED)
  create(@Body() body: { title: string }): Task {
    return this.tasksService.create(body.title);
  }

  @Patch(':id/toggle')
  toggleComplete(@Param('id') id: string): Task {
    return this.tasksService.toggleComplete(id);
  }
}
```

**Lo que hace cada parte**:

- `@Controller('tasks')` significa que todas las rutas de este controlador empiezan con `/tasks`.
- `@Get()` responde a `GET /tasks` → devuelve todas las tareas.
- `@Post()` responde a `POST /tasks` → crea una tarea. `@HttpCode(201)` devuelve "Created" en lugar del 200 por defecto.
- `@Patch(':id/toggle')` responde a `PATCH /tasks/abc123/toggle` → marca/desmarca una tarea.
- `@Body()` extrae el JSON del cuerpo de la petición.
- `@Param('id')` extrae el `id` de la URL.

### Verificar que el módulo registra el servicio

Abre `src/tasks/tasks.module.ts`. Debe verse así:

```typescript
import { Module } from '@nestjs/common';
import { TasksController } from './tasks.controller';
import { TasksService } from './tasks.service';

@Module({
  controllers: [TasksController],
  providers: [TasksService],
})
export class TasksModule {}
```

Si te falta `TasksService` en `providers`, agrégalo. Sin eso, NestJS no sabrá cómo inyectarlo en el controlador.

---

## Parte 4 — Probar el API con Thunder Client (15 min)

Vuelve a arrancar el servidor:

```bash
npm run start:dev
```

`start:dev` activa el modo "watch": cada vez que guardes un archivo, el servidor se reinicia automáticamente.

### Abrir Thunder Client

En VS Code, haz clic en el ícono de relámpago en la barra lateral izquierda (Thunder Client). Si no lo ves, instálalo desde Extensiones.

### Probar GET /tasks

1. Haz clic en **"New Request"**.
2. Asegúrate de que el método sea **GET**.
3. En la URL escribe: `http://localhost:3000/tasks`.
4. Haz clic en **"Send"**.

Vas a recibir:

```json
[]
```

Un array vacío. ¡Es correcto! No hemos creado tareas todavía.

### Probar POST /tasks

1. Crea un nuevo request (botón "+" o "New Request").
2. Cambia el método a **POST**.
3. URL: `http://localhost:3000/tasks`.
4. Ve a la pestaña **"Body"** y selecciona **JSON**.
5. Pega esto:

   ```json
   {
     "title": "Aprender NestJS"
   }
   ```

6. Haz clic en **"Send"**.

Debes recibir algo como:

```json
{
  "id": "k3l5m9n2p",
  "title": "Aprender NestJS",
  "completed": false,
  "createdAt": "2026-05-21T15:30:00.000Z"
}
```

Status code: **201 Created**.

### Probar GET /tasks de nuevo

Vuelve a tu primer request (GET) y haz "Send" otra vez. Ahora debes ver:

```json
[
  {
    "id": "k3l5m9n2p",
    "title": "Aprender NestJS",
    "completed": false,
    "createdAt": "2026-05-21T15:30:00.000Z"
  }
]
```

### Probar PATCH /tasks/:id/toggle

1. Copia el `id` que te devolvió el POST.
2. Crea un nuevo request, método **PATCH**.
3. URL: `http://localhost:3000/tasks/k3l5m9n2p/toggle` (reemplaza el id por el tuyo).
4. Send.

Debe responder con la tarea con `completed: true`.

### Probar el caso de error

Crea un POST con body vacío `{}`:

```json
{}
```

Debes recibir un error 500 con el mensaje "El título es obligatorio".

!!! tip "En producción usarías validación apropiada"
    NestJS tiene una librería llamada `class-validator` que valida el body automáticamente y devuelve errores 400 con mensajes claros. Lo veremos en un workshop más avanzado.

---

## Parte 5 — Habilitar CORS (10 min)

Cuando tu frontend (en `http://localhost:5173`) intente llamar a tu backend (en `http://localhost:3000`), el navegador va a **bloquear la petición**. Esto es por seguridad: se llama **CORS** (Cross-Origin Resource Sharing).

Para que el frontend pueda hablar con el backend, tenemos que decirle al backend "está bien, acepta peticiones de otros dominios".

### Modificar main.ts

Abre `src/main.ts`. Verás algo así:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

Modifícalo a:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.enableCors({
    origin: [
      'http://localhost:5173',
      'http://localhost:3000',
    ],
    methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  });

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

Guarda. El servidor se reinicia solo.

**Lo que hicimos**:

- `enableCors()` le dice a NestJS "acepta peticiones de estos orígenes".
- `5173` es el puerto por defecto de Vite (el motor de React).
- Cuando despleguemos a producción (Sesión 4), vamos a agregar la URL de Vercel también.

!!! warning "Anti-patrón frecuente"
    Muchos tutoriales te enseñan a poner `origin: '*'` (cualquier origen). En desarrollo está bien, pero en producción es un agujero de seguridad. Siempre lista los orígenes específicos.

---

## Parte 6 — Cambiar el puerto y agregar variable de entorno (5 min)

En la línea `app.listen(process.env.PORT ?? 3000)`, esta lógica significa:

- Si existe una variable de entorno `PORT`, usar ese puerto.
- Si no, usar el 3000.

Esto es importante porque cuando despleguemos en Render, Render nos va a asignar un puerto aleatorio a través de `process.env.PORT`. Si tu app no respeta esa variable, no funciona.

### Crear archivo .env (opcional pero recomendado)

En la carpeta `backend/`, crea un archivo llamado `.env`:

```
PORT=3000
```

Por ahora no lo usamos en el código (Node.js no lee `.env` automáticamente, hay que instalar una librería), pero lo dejamos para la próxima sesión.

!!! danger "NO subas archivos .env a GitHub"
    Los archivos `.env` suelen contener secretos (passwords, API keys). Confirma que tu `.gitignore` incluye `.env`. El `.gitignore` que GitHub generó para Node ya debería tenerlo, pero verifícalo.

---

## Parte 7 — Hacer commit del backend (5 min)

Detén el servidor con `Ctrl+C`. Vuelve a la raíz del proyecto:

```bash
cd ..
```

Verifica qué cambió:

```bash
git status
```

Vas a ver MUCHOS archivos nuevos (toda la carpeta `backend/`). Eso está bien.

Agrega todo y haz commit:

```bash
git add .
git commit -m "feat: agregar backend con NestJS y endpoints de tareas"
git push
```

Verifica en GitHub: debes ver la carpeta `backend/` ahí.

---

## Resumen de lo que aprendiste

- Diferencia entre **controlador** (recibe HTTP) y **servicio** (lógica).
- Cómo crear un módulo, controlador y servicio con `nest generate`.
- Cómo definir endpoints con decoradores: `@Get()`, `@Post()`, `@Patch()`.
- Cómo recibir datos del cliente con `@Body()` y `@Param()`.
- Qué es CORS y cómo habilitarlo.
- Cómo probar APIs con Thunder Client.

---

## Problemas frecuentes

??? question "Mi servidor no inicia, dice 'EADDRINUSE'"
    Significa que el puerto 3000 ya está en uso. Cierra cualquier otra aplicación que esté usando ese puerto, o cambia el puerto en `main.ts`.

??? question "Thunder Client dice 'Network Error' o 'fetch failed'"
    Verifica que el servidor esté corriendo (debes ver los logs de Nest en la terminal). Si no, ejecuta `npm run start:dev` de nuevo.

??? question "El POST me devuelve 500 con 'Cannot read property...'"
    Probablemente no enviaste el body en formato JSON, o no marcaste "Body → JSON" en Thunder Client.

??? question "Cambié código pero el servidor no se actualiza"
    Asegúrate de haber arrancado con `npm run start:dev` (no `npm run start`). El sufijo `:dev` activa el modo watch.

??? question "Me sale un error de TypeScript que no entiendo"
    Léelo con calma. TypeScript es estricto a propósito, te avisa de errores antes de que el código se rompa en producción. Casi siempre te dice exactamente qué línea y qué problema hay.

---

## Antes de pasar a la Sesión 3

Asegúrate de que:

- [x] Tu backend corre con `npm run start:dev`.
- [x] `GET /tasks` te devuelve un array.
- [x] `POST /tasks` con `{"title": "..."}` te crea una tarea.
- [x] CORS está habilitado para `localhost:5173`.
- [x] Tu código está en GitHub.

Cuando estés listo, ve a la [Sesión 3 — Frontend con React](sesion_3.md).
