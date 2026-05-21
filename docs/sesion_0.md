# Sesión 0 — Antes de empezar

## ¿Qué vamos a hacer en esta sesión?

Vamos a dejar tu computadora lista para programar. Esto NO es parte de las 4 horas del workshop, es preparación previa. Tómate tu tiempo, hazlo en casa con calma.

Al final de esta sesión vas a tener instalado:

1. Node.js (el motor que ejecuta nuestro código JavaScript fuera del navegador).
2. Git (la herramienta para guardar versiones de tu código).
3. Visual Studio Code (el editor de código).
4. Una cuenta de GitHub (donde vive tu código en la nube).
5. Postman o Thunder Client (para probar APIs).

!!! warning "Importante"
    Si llegas a la primera sesión sin esto instalado, vas a perder tiempo y vas a frustrarte. Haz esta preparación con paciencia, idealmente un día antes del workshop.

---

## 1. Instalar Node.js

Node.js es un programa que te permite ejecutar código JavaScript en tu computadora (no solo en el navegador). NestJS y React lo necesitan para funcionar.

### Pasos

1. Abre tu navegador y ve a [https://nodejs.org](https://nodejs.org).
2. Verás dos botones grandes. Descarga la versión **LTS** (Long Term Support). En este momento debería ser Node.js 22.x LTS o superior.
3. Ejecuta el instalador descargado.
4. Acepta todas las opciones por defecto haciendo clic en "Next" o "Siguiente".
5. Cuando termine, **cierra cualquier terminal o consola que tengas abierta**.

### Verificar la instalación

Abre una terminal nueva:

- **Windows**: presiona `Windows + R`, escribe `cmd` y presiona Enter.
- **Mac**: abre Spotlight con `Cmd + Space`, escribe `Terminal` y presiona Enter.
- **Linux**: presiona `Ctrl + Alt + T`.

En la terminal escribe:

```bash
node --version
```

Debe responder algo como `v22.11.0`. Si dice "node no se reconoce como un comando interno o externo", **reinicia tu computadora** y vuelve a probar.

Ahora escribe:

```bash
npm --version
```

Debe responder algo como `10.9.0`. `npm` es el gestor de paquetes de Node.js, viene incluido en la misma instalación.

!!! danger "Si algo no funciona"
    - Reinicia la computadora. En el 80% de los casos esto resuelve el problema.
    - En Windows, asegúrate de instalar como Administrador.
    - No instales Node desde la tienda de Microsoft (Microsoft Store). Usa el instalador oficial de la página.

---

## 2. Instalar Git

Git es el sistema que usa todo el mundo para guardar versiones de su código y colaborar con otras personas.

### Pasos

1. Ve a [https://git-scm.com/downloads](https://git-scm.com/downloads).
2. Descarga la versión para tu sistema operativo.
3. Ejecuta el instalador.

!!! tip "Instalación en Windows"
    Te van a salir muchas pantallas con opciones técnicas. Para principiantes, deja **todas las opciones por defecto**. Solo haz clic en "Next" hasta el final.

### Verificar la instalación

En una terminal nueva:

```bash
git --version
```

Debe responder algo como `git version 2.45.0`.

### Configurar Git con tu nombre y correo

Esto es importante porque cada cambio que hagas en tu código quedará firmado con tu nombre. Usa los mismos datos que vas a usar en GitHub.

En la terminal, ejecuta estos dos comandos (uno a la vez, reemplazando con tus datos):

```bash
git config --global user.name "Tu Nombre"
```

```bash
git config --global user.email "tu-correo@ejemplo.com"
```

Para confirmar que se guardó:

```bash
git config --global user.name
git config --global user.email
```

Debe mostrarte tu nombre y tu correo.

---

## 3. Instalar Visual Studio Code

Visual Studio Code (o VS Code) es el editor de código gratuito más usado en el mundo.

### Pasos

1. Ve a [https://code.visualstudio.com/](https://code.visualstudio.com/).
2. Descarga la versión para tu sistema operativo.
3. Ejecuta el instalador. Acepta todas las opciones por defecto.

!!! tip "En Windows, marca esta casilla"
    Durante la instalación, asegúrate de marcar la opción **"Add to PATH"** o **"Agregar al PATH"**. Esto te permite abrir VS Code desde la terminal escribiendo `code .`.

### Extensiones recomendadas

Cuando abras VS Code por primera vez, instala estas extensiones (Ctrl+Shift+X o Cmd+Shift+X):

1. **ESLint** — te avisa de errores de código mientras escribes.
2. **Prettier** — formatea tu código automáticamente.
3. **Thunder Client** — para probar APIs sin salir de VS Code (alternativa a Postman).
4. **GitLens** — mejora la integración con Git.
5. **ES7+ React/Redux/React-Native snippets** — atajos útiles para escribir React rápido.

### Verificar

Abre una terminal y escribe:

```bash
code --version
```

Si responde con un número de versión, todo bien. Si no, reinicia la computadora.

---

## 4. Crear cuenta en GitHub

GitHub es la red social de los desarrolladores. Es donde vas a guardar tu código en la nube y donde la mayoría de empresas revisan los perfiles de los candidatos.

### Pasos

1. Ve a [https://github.com](https://github.com).
2. Haz clic en **"Sign up"** (Registrarse).
3. Usa el mismo correo que configuraste en Git.
4. Elige un nombre de usuario profesional. **No uses cosas como `xXdarkSlayerXx99`**. Tu usuario será parte de la URL de tus proyectos. Algo como `juanperez-dev` o `juanperez23` está bien.
5. Confirma tu correo electrónico.

!!! info "Por qué un username profesional importa"
    Tu perfil de GitHub será probablemente lo primero que un reclutador vea cuando aplique a un trabajo de desarrollo. La URL `github.com/juanperez-dev` se ve mucho mejor en un currículum que `github.com/elKaiserMago2002`.

### Autenticación para subir código

Para que tu computadora pueda subir código a GitHub, necesitas configurar autenticación. La forma más sencilla en este workshop es usar **GitHub CLI**.

Ve a [https://cli.github.com/](https://cli.github.com/) y descarga GitHub CLI. Después de instalarlo, en una terminal:

```bash
gh auth login
```

Te va a hacer varias preguntas, responde así:

- "What account do you want to log into?" → **GitHub.com**
- "What is your preferred protocol for Git operations?" → **HTTPS**
- "Authenticate Git with your GitHub credentials?" → **Y** (sí)
- "How would you like to authenticate GitHub CLI?" → **Login with a web browser**

Te va a mostrar un código de 8 caracteres (algo como `XXXX-XXXX`). Cópialo, abre el link que aparece, pégalo y autoriza.

Cuando termine, verifica con:

```bash
gh auth status
```

Debe decir "Logged in to github.com".

---

## 5. Instalar herramienta para probar APIs

Necesitas algo para enviar peticiones HTTP a tu backend. Tienes dos opciones, **elige una**:

### Opción A: Thunder Client (recomendado para principiantes)

Es una extensión de VS Code, no necesitas instalar nada aparte.

1. Abre VS Code.
2. Ve a Extensiones (`Ctrl+Shift+X` o `Cmd+Shift+X`).
3. Busca "Thunder Client".
4. Haz clic en "Install".

Verás un nuevo ícono de relámpago en la barra lateral izquierda.

### Opción B: Postman (más completo, requiere instalar app aparte)

1. Ve a [https://www.postman.com/downloads/](https://www.postman.com/downloads/).
2. Descarga e instala.
3. Crea una cuenta gratuita.

Para este workshop, **Thunder Client es suficiente** y más rápido.

---

## 6. Checklist final

Antes de la primera sesión, asegúrate de que **todos estos comandos funcionan** en tu terminal:

```bash
node --version
npm --version
git --version
git config --global user.name
git config --global user.email
code --version
gh auth status
```

Si alguno falla, **resuélvelo antes del workshop**. Cuando todo esté listo, ve a la [Sesión 1 — Setup y Git](sesion_1.md).

---

## Problemas frecuentes

??? question "Mi terminal dice 'node no es un comando reconocido'"
    Reinicia la computadora. Si sigue fallando, desinstala Node y vuelve a instalarlo desde la página oficial. No uses la Microsoft Store.

??? question "VS Code no abre con `code .` desde la terminal"
    En Windows, ejecuta el instalador de VS Code de nuevo y marca la casilla "Add to PATH".
    
    En Mac, abre VS Code, presiona `Cmd+Shift+P`, escribe "Shell Command" y elige "Install 'code' command in PATH".

??? question "Tengo Windows muy viejo / Mac muy viejo, ¿funciona?"
    Necesitas Windows 10 o superior, o macOS 11 (Big Sur) o superior. En sistemas más viejos algunas herramientas no se instalarán.

??? question "No quiero crear cuenta en GitHub, ¿puedo seguir el workshop?"
    No. La sesión 1 y la sesión 4 dependen de GitHub. Es 100% gratis, hazlo.
