# Sesión 3 — Interfaz de Usuario (Frontend con React)

## ¿Qué vamos a hacer en esta sesión?

**Duración**: 1 hora
**Lo que vas a lograr**: Una interfaz web en React que muestra las tareas, permite crear nuevas y marcarlas como completadas. Consume el API que construiste en la Sesión 2.

Al terminar, vas a abrir `http://localhost:5173` en tu navegador y vas a ver TU aplicación de tareas funcionando.

---

## Parte 1 — ¿Qué es React y qué es Vite? (5 min)

### React

React es una biblioteca de JavaScript creada por Meta (Facebook) para construir interfaces de usuario. La idea central es dividir la UI en **componentes**: piezas reutilizables que combinan HTML, CSS y JavaScript en un solo archivo.

Un componente es básicamente una función que devuelve cómo se ve algo en pantalla.

### Vite

Vite es una **herramienta de desarrollo** que arranca tu proyecto de React súper rápido. Cuando guardas un cambio en el código, en menos de 100 milisegundos lo ves reflejado en el navegador. Esto se llama **Hot Module Replacement (HMR)**.

Antes de Vite, todo el mundo usaba `create-react-app` (CRA), pero era lento. Hoy el estándar es Vite.

### ¿Es lo mismo React que Vite?

No.

- **React** es la librería para construir la UI.
- **Vite** es la herramienta que compila y sirve tu código de React durante desarrollo.

Es como decir: React es el lenguaje, Vite es el editor con autocompletado y compilación instantánea.

---

## Parte 2 — Crear el proyecto frontend con Vite (10 min)

Asegúrate de estar en la raíz del repositorio (la carpeta `workshop-tareas`, NO dentro de `backend/`).

```bash
cd ~/proyectos/workshop-tareas
```

(En Windows ajusta la ruta).

### Crear el proyecto

```bash
npm create vite@latest frontend -- --template react-ts
```

Te explico el comando:

- `npm create vite@latest`: descarga y ejecuta el creador de proyectos de Vite, en su última versión.
- `frontend`: el nombre de la carpeta a crear.
- `--template react-ts`: plantilla de React con TypeScript.

!!! tip "¿Por qué TypeScript?"
    Te ayuda a no equivocarte con tipos de datos. Por ejemplo, si llamas a una función con un número cuando esperaba un string, TypeScript te avisa antes de ejecutar el código. En proyectos serios, casi nadie usa JavaScript puro hoy.

Te puede pedir confirmación para instalar `create-vite`. Responde **y** (yes) y Enter.

### Instalar las dependencias

```bash
cd frontend
npm install
```

Esto tarda 1-2 minutos. Descarga React, ReactDOM, Vite y todas sus dependencias.

### Probar que el proyecto corre

```bash
npm run dev
```

Verás algo así en la terminal:

```
  VITE v6.0.0  ready in 320 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

Abre [http://localhost:5173](http://localhost:5173) en tu navegador. Verás la página de bienvenida de Vite + React, con un contador que aumenta cuando haces clic.

!!! success "Tienes dos servidores corriendo a la vez"
    Tu backend en `localhost:3000` (terminal 1) y tu frontend en `localhost:5173` (terminal 2). Ambos deben estar corriendo al mismo tiempo para que la app funcione completa.

### Estructura que generó Vite

En VS Code, expande la carpeta `frontend/`:

```
frontend/
├── src/
│   ├── App.tsx           ← Componente principal
│   ├── App.css           ← Estilos del componente principal
│   ├── main.tsx          ← Punto de entrada (monta React en el HTML)
│   ├── index.css         ← Estilos globales
│   └── assets/           ← Imágenes, fuentes, etc
├── index.html            ← El único HTML real
├── package.json
└── vite.config.ts
```

---

## Parte 3 — Anatomía de un componente de React (10 min)

Abre `src/App.tsx`. Verás algo así:

```typescript
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import viteLogo from '/vite.svg'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      <div>
        <a href="https://vite.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
      </div>
    </>
  )
}

export default App
```

Vamos a entender cada parte:

### 1. Imports

```typescript
import { useState } from 'react'
```

Trae el hook `useState` desde la librería React. Los **hooks** son funciones especiales que te dan superpoderes dentro de un componente.

### 2. Definición del componente

```typescript
function App() {
  // ...
}
```

Un componente es una función con nombre en **PascalCase** (primera letra mayúscula). Esto es obligatorio en React.

### 3. Estado con useState

```typescript
const [count, setCount] = useState(0)
```

Esto es lo más importante de React. Significa:

- Crea una variable `count` con valor inicial 0.
- Crea una función `setCount` para cambiar ese valor.
- Cuando llames `setCount`, React **vuelve a ejecutar el componente** y actualiza la pantalla.

Esto es lo que hace React **reactivo**: tu UI siempre refleja el estado actual.

### 4. JSX (el HTML dentro del JS)

```typescript
return (
  <h1>Vite + React</h1>
)
```

Lo que parece HTML dentro del JavaScript se llama **JSX**. No es HTML, es una sintaxis especial de React que se compila a JavaScript. Tiene algunas diferencias:

- En lugar de `class=`, usas `className=`.
- En lugar de `onclick=`, usas `onClick={...}`.
- Las variables se interpolan con `{}`.

### 5. Event handlers

```typescript
<button onClick={() => setCount(count + 1)}>
```

Cuando haces clic, ejecuta la función. La función llama `setCount(count + 1)`, lo que hace que React re-renderice el componente con el nuevo valor.

---

## Parte 4 — Limpiar y construir nuestra app (5 min)

Vamos a borrar el contenido por defecto y empezar limpio.

### Vaciar App.tsx

Reemplaza TODO el contenido de `src/App.tsx` por:

```typescript
import './App.css'

function App() {
  return (
    <div className="container">
      <h1>Mis Tareas</h1>
      <p>Aquí va a ir la app.</p>
    </div>
  )
}

export default App
```

### Limpiar App.css

Reemplaza TODO el contenido de `src/App.css` por:

```css
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  font-family: system-ui, sans-serif;
}

h1 {
  color: #1e40af;
  text-align: center;
  margin-bottom: 2rem;
}

.form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

.form input {
  flex: 1;
  padding: 0.6rem 0.8rem;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  font-size: 1rem;
}

.form button {
  padding: 0.6rem 1.2rem;
  background: #1e40af;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
}

.form button:hover {
  background: #1e3a8a;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.75rem;
  border: 1px solid #e2e8f0;
  border-radius: 6px;
  margin-bottom: 0.5rem;
  background: #f8fafc;
}

.task-item.completed .task-title {
  text-decoration: line-through;
  color: #94a3b8;
}

.task-title {
  flex: 1;
  font-size: 1rem;
}

.empty {
  text-align: center;
  color: #64748b;
  font-style: italic;
  margin-top: 2rem;
}
```

Guarda y verifica que el navegador refleja los cambios (debes ver "Mis Tareas" como título).

---

## Parte 5 — Conectar con el backend (20 min)

Ahora viene la parte importante: hacer que el frontend hable con el API que construiste en la Sesión 2.

### Crear el archivo de configuración del API

En `src/`, crea un archivo `api.ts`:

```typescript
const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000'

export interface Task {
  id: string
  title: string
  completed: boolean
  createdAt: string
}

export async function fetchTasks(): Promise<Task[]> {
  const response = await fetch(`${API_URL}/tasks`)
  if (!response.ok) {
    throw new Error('Error al cargar tareas')
  }
  return response.json()
}

export async function createTask(title: string): Promise<Task> {
  const response = await fetch(`${API_URL}/tasks`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title }),
  })
  if (!response.ok) {
    throw new Error('Error al crear tarea')
  }
  return response.json()
}

export async function toggleTask(id: string): Promise<Task> {
  const response = await fetch(`${API_URL}/tasks/${id}/toggle`, {
    method: 'PATCH',
  })
  if (!response.ok) {
    throw new Error('Error al actualizar tarea')
  }
  return response.json()
}
```

**Lo que estamos haciendo**:

- `import.meta.env.VITE_API_URL`: lee una variable de entorno. Si no existe, usa `localhost:3000`. Esto será clave en la Sesión 4 cuando despleguemos.
- `fetchTasks()`: hace `GET /tasks` y devuelve un array.
- `createTask()`: hace `POST /tasks` con un JSON en el body.
- `toggleTask()`: hace `PATCH /tasks/:id/toggle`.

!!! info "¿Por qué separar las llamadas al API?"
    Tener toda la lógica de API en un archivo aparte es una buena práctica. Si mañana cambia tu URL, una sola línea se modifica. Si mañana decides usar `axios` en lugar de `fetch`, también es un solo archivo. Tu componente queda limpio.

### Modificar App.tsx para usar el API

Reemplaza TODO `src/App.tsx` por:

```typescript
import { useState, useEffect } from 'react'
import { Task, fetchTasks, createTask, toggleTask } from './api'
import './App.css'

function App() {
  const [tasks, setTasks] = useState<Task[]>([])
  const [newTitle, setNewTitle] = useState('')
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    loadTasks()
  }, [])

  async function loadTasks() {
    try {
      setLoading(true)
      const data = await fetchTasks()
      setTasks(data)
      setError(null)
    } catch (err) {
      setError('No se pudieron cargar las tareas. ¿Está corriendo el backend?')
    } finally {
      setLoading(false)
    }
  }

  async function handleCreate() {
    if (!newTitle.trim()) return
    try {
      const created = await createTask(newTitle)
      setTasks([...tasks, created])
      setNewTitle('')
    } catch (err) {
      setError('Error al crear la tarea')
    }
  }

  async function handleToggle(id: string) {
    try {
      const updated = await toggleTask(id)
      setTasks(tasks.map((t) => (t.id === id ? updated : t)))
    } catch (err) {
      setError('Error al actualizar la tarea')
    }
  }

  return (
    <div className="container">
      <h1>Mis Tareas</h1>

      <div className="form">
        <input
          type="text"
          value={newTitle}
          onChange={(e) => setNewTitle(e.target.value)}
          placeholder="¿Qué tienes que hacer?"
          onKeyDown={(e) => e.key === 'Enter' && handleCreate()}
        />
        <button onClick={handleCreate}>Agregar</button>
      </div>

      {error && <p style={{ color: 'red' }}>{error}</p>}

      {loading ? (
        <p className="empty">Cargando...</p>
      ) : tasks.length === 0 ? (
        <p className="empty">No tienes tareas. ¡Crea la primera!</p>
      ) : (
        <ul className="task-list">
          {tasks.map((task) => (
            <li
              key={task.id}
              className={`task-item ${task.completed ? 'completed' : ''}`}
            >
              <input
                type="checkbox"
                checked={task.completed}
                onChange={() => handleToggle(task.id)}
              />
              <span className="task-title">{task.title}</span>
            </li>
          ))}
        </ul>
      )}
    </div>
  )
}

export default App
```

### Explicación detallada

#### 1. Estados que maneja el componente

```typescript
const [tasks, setTasks] = useState<Task[]>([])
const [newTitle, setNewTitle] = useState('')
const [loading, setLoading] = useState(true)
const [error, setError] = useState<string | null>(null)
```

- `tasks`: array con las tareas actuales.
- `newTitle`: lo que el usuario está escribiendo en el input.
- `loading`: si está esperando la respuesta del API.
- `error`: mensaje de error si algo falla.

#### 2. useEffect

```typescript
useEffect(() => {
  loadTasks()
}, [])
```

`useEffect` ejecuta código cuando algo cambia. El segundo argumento (`[]`, array vacío) significa "solo ejecuta esto una vez, cuando el componente se monta por primera vez".

En este caso, cuando se carga la app, automáticamente pedimos las tareas al backend.

!!! warning "Error común"
    Si pones `loadTasks()` directamente en el cuerpo del componente (sin `useEffect`), se va a ejecutar en cada render. Y como `setTasks` causa un nuevo render, entras en un loop infinito. Por eso `useEffect` existe.

#### 3. Manejo del input

```typescript
<input
  value={newTitle}
  onChange={(e) => setNewTitle(e.target.value)}
/>
```

Esto se llama **controlled component**: React es la fuente de verdad del valor del input. Cada vez que el usuario escribe, `onChange` se dispara y actualizamos el estado.

#### 4. Listas con .map()

```typescript
{tasks.map((task) => (
  <li key={task.id}>...</li>
))}
```

Para renderizar listas, usamos `.map()`. La prop `key` es obligatoria y debe ser única (por eso usamos `task.id`).

!!! danger "No uses el índice como key"
    Verás tutoriales que hacen `key={index}`. Funciona en casos triviales, pero da bugs raros cuando reordenas la lista. Siempre usa un id único.

---

## Parte 6 — Probar la app completa (5 min)

### Asegúrate de que ambos servidores corren

1. **Terminal 1** (backend):

   ```bash
   cd backend
   npm run start:dev
   ```

2. **Terminal 2** (frontend):

   ```bash
   cd frontend
   npm run dev
   ```

### En el navegador

Abre [http://localhost:5173](http://localhost:5173). Debes ver:

- Título "Mis Tareas".
- Un input y botón "Agregar".
- El mensaje "No tienes tareas. ¡Crea la primera!" si tu base está vacía, o las tareas que creaste en la Sesión 2 con Thunder Client.

### Probar el flujo

1. Escribe "Comprar pan" en el input.
2. Haz clic en "Agregar" (o presiona Enter).
3. La tarea aparece abajo.
4. Haz clic en el checkbox. El texto se tacha.
5. Haz clic de nuevo. Se destacha.

!!! success "Tienes una app fullstack funcionando"
    React está hablando con NestJS. NestJS está guardando los datos en memoria. Estás haciendo HTTP entre dos procesos diferentes en tu computadora. Esto es lo mismo que hacen todas las aplicaciones web del mundo, solo que las suyas viven en internet.

### Probar el caso de error

Detén el backend con `Ctrl+C` en la terminal 1. Refresca el navegador. Debes ver el mensaje de error: "No se pudieron cargar las tareas. ¿Está corriendo el backend?"

Vuelve a arrancar el backend y refresca: todo vuelve a funcionar.

---

## Parte 7 — Commit y push (5 min)

Detén ambos servidores. Vuelve a la raíz del proyecto:

```bash
cd ..
```

Verifica:

```bash
git status
```

Vas a ver muchos archivos nuevos (toda la carpeta `frontend/`).

Antes de hacer commit, verifica que el `.gitignore` ignora `node_modules`. Abre el `.gitignore` de la raíz; debe incluir `node_modules/`. Si no, agrégalo.

!!! danger "Nunca subas node_modules"
    `node_modules` puede pesar gigas y contiene miles de archivos. Cuando otra persona clona el repo, ejecuta `npm install` y se le recrea localmente. Subirlo es un error grave.

Verifica el tamaño del cambio:

```bash
git status
```

Si ves `frontend/node_modules/` listado, hay un problema con el `.gitignore`. Crea un `.gitignore` dentro de `frontend/`:

```
node_modules
dist
.env.local
.env
```

Y dentro de `backend/`:

```
node_modules
dist
.env
```

Ahora sí:

```bash
git add .
git commit -m "feat: agregar frontend con React y conexion al backend"
git push
```

---

## Resumen de lo que aprendiste

- Qué es un componente de React y cómo se estructura.
- `useState`: cómo manejar el estado.
- `useEffect`: cómo ejecutar código al montar el componente.
- Cómo manejar inputs controlados.
- Cómo renderizar listas con `.map()` y la importancia de `key`.
- Cómo hacer llamadas HTTP con `fetch`.
- Cómo separar la lógica del API en un archivo aparte.
- Cómo usar variables de entorno con `import.meta.env`.

---

## Problemas frecuentes

??? question "El navegador dice 'CORS policy: No Access-Control-Allow-Origin'"
    El backend no tiene CORS habilitado, o la URL del frontend no está en la lista. Vuelve a la Sesión 2 y revisa que `main.ts` incluya `http://localhost:5173`.

??? question "Mis tareas no se ven, queda en 'Cargando...'"
    Abre las DevTools del navegador (`F12`), ve a la pestaña "Console" o "Network". Vas a ver el error específico. Lo más común: el backend no está corriendo.

??? question "Cuando reinicio el backend, las tareas desaparecen"
    Eso es correcto. Las tareas viven en memoria. Cuando el backend se reinicia, el array se vacía. En un proyecto real usarías una base de datos. Lo veremos en un workshop más avanzado.

??? question "El input no me deja escribir"
    Probablemente olvidaste el `onChange` o el `value`. Un input controlado SIEMPRE necesita ambos.

??? question "Aparece una pantalla blanca y nada se ve"
    Abre las DevTools (`F12`), ve a "Console", lee el error en rojo. El 99% de las veces te dice exactamente qué archivo y qué línea tiene el problema.

---

## Antes de pasar a la Sesión 4

Asegúrate de que:

- [x] Tu app corre en `http://localhost:5173`.
- [x] Puedes ver, crear y completar tareas.
- [x] El código está en GitHub (revisa en el navegador que veas la carpeta `frontend/`).
- [x] No subiste `node_modules` (revisa en GitHub que no esté).

Cuando estés listo, ve a la [Sesión 4 — Deployment a producción](sesion_4.md).
