# Sesión 1 — Setup y Control de Versiones (Git)

## ¿Qué vamos a hacer en esta sesión?

**Duración**: 1 hora
**Lo que vas a lograr**: Tendrás un repositorio en GitHub con el proyecto base del workshop, y entenderás cómo Git guarda los cambios de tu código.

Al terminar esta sesión, tu carpeta del proyecto va a estar conectada con un repositorio remoto en GitHub. Esto significa que cualquier cambio que hagas localmente lo podrás subir a la nube con un comando.

---

## Parte 1 — ¿Qué es Git y por qué lo usamos? (10 min)

Imagínate que estás escribiendo un trabajo de la universidad. Probablemente tienes archivos así:

```
tarea_final.docx
tarea_final_v2.docx
tarea_final_v2_correcciones.docx
tarea_final_FINAL.docx
tarea_final_FINAL_FINAL.docx
tarea_final_ESTA_SI_ES_LA_BUENA.docx
```

Esto es **un sistema manual y horrible de control de versiones**. Funciona para una tarea, pero imagínate hacer eso en un proyecto con 500 archivos y 5 personas trabajando al mismo tiempo. Caos.

**Git** es la solución profesional a este problema. Te permite:

1. Guardar "fotos" (commits) de tu código en momentos específicos.
2. Volver a cualquier foto anterior si rompes algo.
3. Tener varias versiones en paralelo (ramas) sin afectar la principal.
4. Trabajar con otras personas sin pisarse el código.

**GitHub** es un servicio en internet que guarda tus repositorios de Git en la nube. Git es la herramienta, GitHub es donde la guardas en línea (hay otras opciones como GitLab y Bitbucket, pero GitHub es el más popular).

### Los 3 conceptos esenciales

Antes de tocar nada, necesitas entender 3 palabras:

- **Repositorio (repo)**: es la carpeta de tu proyecto donde Git está rastreando cambios.
- **Commit**: es una "foto" guardada de tu código en un momento dado. Cada commit tiene un mensaje describiendo qué cambió.
- **Push**: es subir tus commits desde tu computadora hacia GitHub.

---

## Parte 2 — La arquitectura que vamos a construir (10 min)

Antes de tocar código, entiende qué estamos construyendo. Toda aplicación web moderna tiene dos partes:

### Frontend (lo que el usuario ve)

Es lo que se ejecuta en el navegador del usuario. Cuando entras a Instagram y ves fotos, eso es el frontend. Está hecho con HTML, CSS y JavaScript. En este workshop usamos **React**.

### Backend (lo que el usuario NO ve)

Es lo que se ejecuta en un servidor en algún data center. Cuando le das "like" a una foto en Instagram, hay un servidor en algún lugar guardando ese "like" en una base de datos. Eso es el backend. En este workshop usamos **NestJS**.

### ¿Cómo se hablan?

El frontend y el backend se comunican a través de un **API REST**. Es básicamente:

- El frontend pregunta: "dame todas las tareas" (esto es un `GET /tasks`).
- El backend responde con datos en formato JSON.
- El frontend pregunta: "crea una tarea con este texto" (esto es un `POST /tasks`).
- El backend la guarda y responde "ok, creada".

Diagrama mental:

```
[Usuario] ←→ [React (Frontend)] ←HTTP→ [NestJS (Backend)] ←→ [Base de datos]
```

En este workshop, para mantenerlo simple, **NO vamos a usar base de datos real**. Vamos a guardar las tareas en memoria (un array dentro del backend). En la vida real usarías PostgreSQL, MySQL o MongoDB.

---

## Parte 3 — Crear el repositorio en GitHub (10 min)

Vamos a crear el repositorio primero en GitHub, y luego lo clonaremos a tu computadora. Es el flujo más sencillo.

### Pasos

1. Abre [https://github.com](https://github.com) y asegúrate de haber iniciado sesión.

2. En la esquina superior derecha, haz clic en el botón **`+`** y selecciona **"New repository"**.

3. Llena el formulario así:

   - **Repository name**: `workshop-tareas` (o el nombre que quieras, pero usa solo minúsculas y guiones).
   - **Description**: `Mi primera app fullstack con NestJS y React`.
   - **Public** (deja seleccionado público, así Render y Vercel pueden acceder gratis).
   - **Marca la casilla "Add a README file"**.
   - **Add .gitignore**: selecciona **Node** del dropdown.
   - **License**: selecciona **MIT License**.

4. Haz clic en **"Create repository"**.

5. Ahora estás en la página de tu repo recién creado. Vas a ver una URL en la barra de tu navegador así:

   ```
   https://github.com/TU-USUARIO/workshop-tareas
   ```

   Cópiala, la necesitamos para el siguiente paso.

!!! tip "¿Por qué público?"
    Render y Vercel en sus capas gratuitas te dejan desplegar repositorios privados también, pero los públicos son más simples para empezar. Además, un repo público se ve bien en tu portafolio.

---

## Parte 4 — Clonar el repo a tu computadora (10 min)

"Clonar" significa descargar el repositorio de GitHub a tu computadora, con la conexión activa para subir cambios después.

### Pasos

1. Abre VS Code.

2. Abre la terminal integrada de VS Code: menú **Terminal → New Terminal** (o `Ctrl+ñ` en Windows, `Ctrl+\`` en Mac).

3. Antes de clonar, **decide dónde quieres guardar tus proyectos**. Yo recomiendo crear una carpeta `proyectos` en tu carpeta de usuario. En la terminal:

   ```bash
   cd ~
   mkdir proyectos
   cd proyectos
   ```

   - `cd ~` te lleva a tu carpeta de usuario (`/home/tu-usuario` en Linux/Mac, `C:\Users\tu-usuario` en Windows).
   - `mkdir proyectos` crea una carpeta llamada `proyectos`.
   - `cd proyectos` entra a esa carpeta.

4. Ahora clona el repo (reemplaza `TU-USUARIO` con tu usuario real):

   ```bash
   git clone https://github.com/TU-USUARIO/workshop-tareas.git
   ```

5. Entra a la carpeta del proyecto:

   ```bash
   cd workshop-tareas
   ```

6. Abre el proyecto en VS Code:

   ```bash
   code .
   ```

   El punto significa "la carpeta actual". VS Code abre todo el contenido.

### Verificar que estás dentro de un repo de Git

En la terminal de VS Code, ejecuta:

```bash
git status
```

Debe responder algo como:

```
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Si dice "fatal: not a git repository", **no estás en la carpeta correcta**. Usa `cd` para navegar a la carpeta `workshop-tareas`.

!!! danger "Error frecuente"
    Si al clonar te pide usuario y contraseña, escribe tu usuario de GitHub. Pero en la contraseña **NO va tu contraseña de GitHub** (eso ya no funciona). Usa el GitHub CLI que configuraste en la Sesión 0 (`gh auth login`), o crea un Personal Access Token. Si seguiste la Sesión 0, esto ya debería estar resuelto.

---

## Parte 5 — Tu primer commit (15 min)

Vamos a hacer un cambio pequeño y subirlo a GitHub para entender el flujo completo.

### El flujo de Git en 3 pasos

1. **Modificas** archivos en tu computadora.
2. **Le dices a Git qué cambios incluir** en el próximo commit (esto es `git add`).
3. **Creas el commit** con un mensaje describiendo qué cambió (`git commit`).
4. **Subes el commit** a GitHub (`git push`).

### Hacer un cambio

En VS Code, abre el archivo `README.md` que está en la raíz del proyecto. Verás algo como:

```markdown
# workshop-tareas

Mi primera app fullstack con NestJS y React
```

Vamos a modificarlo. Cámbialo a algo así:

```markdown
# workshop-tareas

Mi primera app fullstack con NestJS y React.

## ¿Qué hace?

Es una aplicación de lista de tareas. Permite crear tareas, verlas y marcarlas como completadas.

## Tecnologías

- Backend: NestJS
- Frontend: React + Vite
- Deploy: Render (backend) + Vercel (frontend)

## Autor

Tu Nombre - [@tu-usuario](https://github.com/tu-usuario)
```

Guarda el archivo con `Ctrl+S` o `Cmd+S`.

### Ver qué cambió

En la terminal:

```bash
git status
```

Ahora debe decir algo así:

```
On branch main
Your branch is up to date with 'origin/main'.

Changes not committed yet:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" or "git commit -a")
```

Git detectó que cambiaste `README.md`. Para ver exactamente qué cambió:

```bash
git diff
```

Verás las líneas en rojo (lo que quitaste) y en verde (lo que añadiste).

### Preparar el cambio para el commit

```bash
git add README.md
```

Esto le dice a Git: "incluye este archivo en el próximo commit". Si quisieras incluir TODOS los archivos modificados, usarías:

```bash
git add .
```

(el punto significa "todo lo que cambió en esta carpeta y sus subcarpetas").

### Crear el commit

```bash
git commit -m "docs: actualizar README con descripcion del proyecto"
```

- `-m` significa "message" y va seguido del mensaje del commit entre comillas.
- El mensaje debe describir QUÉ cambió, en presente, en una línea.

!!! tip "Buenos mensajes de commit"
    Hay convenciones como **Conventional Commits**. Algunos prefijos comunes:

    - `feat:` cuando agregas una nueva característica.
    - `fix:` cuando arreglas un bug.
    - `docs:` cambios solo en documentación.
    - `chore:` tareas de mantenimiento (actualizar dependencias, etc).
    - `refactor:` reorganizar código sin cambiar funcionalidad.

### Subir el commit a GitHub

```bash
git push
```

Debe responder algo como:

```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
...
To https://github.com/TU-USUARIO/workshop-tareas.git
   abc1234..def5678  main -> main
```

### Verificar en GitHub

Vuelve a tu navegador y refresca la página del repositorio en GitHub. Vas a ver tu `README.md` actualizado, y en la pestaña **"Commits"** verás tu commit listado con el mensaje que escribiste.

!!! success "Felicidades"
    Acabas de hacer tu primer commit y push. Este es el flujo que vas a usar cientos de veces como desarrollador.

---

## Parte 6 — Estructura final del proyecto (5 min)

Para esta sesión, deja la estructura así de simple:

```
workshop-tareas/
├── .gitignore
├── LICENSE
└── README.md
```

En la **Sesión 2** vamos a crear una subcarpeta `backend/` para NestJS, y en la **Sesión 3** una `frontend/` para React. Las dos van a vivir en el mismo repositorio (esto se llama **monorepo**).

---

## Resumen de lo que aprendiste

| Comando | Para qué sirve |
|---------|---------------|
| `git clone <url>` | Descargar un repo de GitHub a tu computadora |
| `git status` | Ver qué archivos cambiaron |
| `git diff` | Ver línea por línea qué cambió |
| `git add <archivo>` | Preparar un archivo para el próximo commit |
| `git add .` | Preparar todos los archivos modificados |
| `git commit -m "mensaje"` | Crear un commit con un mensaje |
| `git push` | Subir tus commits a GitHub |
| `git pull` | Bajar cambios de GitHub a tu computadora |

---

## Problemas frecuentes

??? question "Me sale 'Permission denied (publickey)' al hacer push"
    Significa que la autenticación no está configurada. Vuelve a la Sesión 0 y ejecuta `gh auth login`.

??? question "Hice un commit con un mensaje feo, ¿lo puedo cambiar?"
    Si **todavía no hiciste push**, sí:
    
    ```bash
    git commit --amend -m "nuevo mensaje"
    ```
    
    Si ya hiciste push, déjalo así. Cambiar la historia ya subida puede causar problemas.

??? question "Modifiqué un archivo por accidente, ¿cómo lo deshago?"
    Si **no le hiciste `git add`** todavía:
    
    ```bash
    git restore nombre-del-archivo
    ```

??? question "Mi push falla con 'rejected, non-fast-forward'"
    Significa que en GitHub hay cambios que tú no tienes en local. Ejecuta:
    
    ```bash
    git pull
    ```
    
    Luego intenta el push de nuevo.

---

## Antes de pasar a la Sesión 2

Asegúrate de que:

- [x] Clonaste tu repo a tu computadora.
- [x] Modificaste el README y lo viste actualizado en GitHub.
- [x] Entendiste qué hace cada uno de los comandos del resumen.

Cuando estés listo, ve a la [Sesión 2 — Backend con NestJS](sesion_2.md).
