# react_dasboard_portfolio
Portafolio de productos utilizando bloques de informacion para dashboard y productos

## Crear una aplicacion con Vite

Para crear una aplicación React con Vite para publicar como sitio estático, ejecuta `npm create vite@latest mi-app -- --template react`, luego `cd mi-app` y `npm install`. Usa `npm run dev` para desarrollarla y `npm run build` para generar los archivos publicables en `dist/`; configura el alojamiento para servir `index.html` y gestionar las rutas de la aplicación.

Crear una carpeta React con Vite lista para publicar como sitio estático

Vite genera la estructura inicial del proyecto y, al compilarlo, crea la carpeta 1dist/1, que contiene los archivos que puedes publicar. Necesitas tener Node.js y npm instalados.

1. Crear el proyecto

En una terminal, ejecuta:

```bash
npm create vite@latest mi-app -- --template react
cd mi-app
npm install
```

Esto crea la carpeta `mi-app` con una plantilla básica de React y configura Vite.

2. Desarrollar y probar

Inicia el servidor local:

```bash
npm run dev
```

La terminal mostrará una dirección local para abrir la aplicación en el navegador. Edita los archivos de `src/` para construir la página; `index.html` es el documento principal.

3. Generar los archivos estáticos

Cuando esté lista para publicarse, ejecuta:

```bash
npm run build
```
Vite guardará el resultado de producción `en dist/`. Publica el contenido de esa carpeta en el servicio de alojamiento. Para comprobar la compilación localmente, puedes usar:

```bash
npm run preview
```
`preview` sirve para verificar el resultado en tu equipo; no es un servidor de producción.

4. Revisar el alojamiento y las rutas

- El alojamiento debe servir `index.html` al abrir la página.
- Si la app usa rutas de React, configura el alojamiento para que las solicitudes a esas rutas también devuelvan `index.html`; así se evitan errores 404 al recargar.
- Si publicas en una subruta, como `usuario.github.io/mi-app/` , configura `base: '/mi-app/'` en `vite.config.js`. Para un dominio raíz, normalmente se usa `/`.

### Consultar 1, Claude

Quisiera una propuesta del stack tecnologico que se debe utilizar para construir una pagina estatica que muestre un dash interactivo con datos obtenidos de un archivo JSON. La aplicacion debe funcionar en la carpeta dist/. La aplicacion debe mostrar un comportamiento interactivo al pulsar diferentes botones para mostrar datos almacenados en el archivo JSON, 

La opción más equilibrada es **Vite como herramienta de build, HTML/CSS/JavaScript (o TypeScript) para la lógica y Chart.js para los gráficos**. Vite genera la carpeta `dist/` con archivos estáticos listos para publicar y no requiere backend.

## Propuesta de stack

| Capa | Tecnología recomendada | Motivo |
|---|---|---|
| Build y servidor de desarrollo | **Vite** | Genera `dist/` automáticamente, recarga en caliente y copia la carpeta `public/` (donde va el JSON) sin configuración extra. |
| Lenguaje | **JavaScript** o **TypeScript** | TypeScript ayuda a tipar la estructura del JSON si el proyecto crece; para algo pequeño, JavaScript basta. |
| Estructura y estilos | **HTML5 + CSS** (opcionalmente Tailwind CSS) | Sin framework, el resultado es ligero y fácil de desplegar. Tailwind acelera el diseño del dashboard. |
| Gráficos | **Chart.js** | Ligero, bien documentado y suficiente para barras, líneas, tartas y donas. Si necesitas gráficos muy complejos, ECharts es la alternativa. |
| Datos | **Archivo JSON** en `public/data/datos.json` | Vite lo copia a `dist/data/` en el build y se carga con `fetch()`. |
| Hosting estático | **GitHub Pages**, Netlify o Vercel | Publican la carpeta `dist/` sin coste ni servidor propio. |

Si prefieres un enfoque basado en componentes, **React + Vite** es la alternativa natural. Para un dashboard con pocos botones y pocas vistas, la versión con JavaScript puro es más simple y tiene menos piezas que mantener.

## Estructura del proyecto

```
mi-dashboard/
├── public/
│   └── data/
│       └── datos.json      → se copia tal cual a dist/data/
├── src/
│   ├── main.js             → lógica: carga de datos, botones, gráficos
│   └── style.css
├── index.html
├── package.json
└── dist/                   → generada con "npm run build"
```

## Flujo de funcionamiento

### Carga de datos
Al iniciar, `main.js` lee el JSON con `fetch('data/datos.json')` y guarda los datos en memoria. Desde ahí se generan todas las vistas sin volver a pedir el archivo.

### Interacción con botones
Cada botón (por ejemplo, "Ventas", "Usuarios", "Año 2026") tiene un `data-` que identifica qué bloque del JSON mostrar. Al pulsarlo, el script filtra los datos, actualiza las tarjetas con los totales y redibuja el gráfico con `chart.update()`.

Ejemplo simplificado:

```javascript
let datos = {};
let grafico;

const contenedorBotones = document.querySelector('#botones');

async function cargarDatos() {
  const respuesta = await fetch('data/datos.json');
  datos = await respuesta.json();
  mostrarSeccion('ventas'); // vista inicial
}

function mostrarSeccion(clave) {
  const seccion = datos[clave];
  if (!seccion) return;

  // Actualizar tarjetas de resumen
  document.querySelector('#total').textContent = seccion.total;

  // Redibujar gráfico
  if (grafico) grafico.destroy();
  grafico = new Chart(document.querySelector('#grafico'), {
    type: 'bar',
    data: {
      labels: seccion.etiquetas,
      datasets: [{ label: clave, data: seccion.valores }]
    }
  });
}

// Delegación de eventos: un solo listener para todos los botones
contenedorBotones.addEventListener('click', (e) => {
  const boton = e.target.closest('button[data-seccion]');
  if (boton) mostrarSeccion(boton.dataset.seccion);
});

cargarDatos();
```

### Puntos a tener en cuenta

- **No abras `dist/index.html` con doble clic.** Los navegadores bloquean `fetch()` sobre `file://`. Para probarlo, usa un servidor local, por ejemplo `npx serve dist` o `python -m http.server` dentro de `dist/`.
- **Rutas relativas.** Usa `data/datos.json` en lugar de `/data/datos.json`. Si despliegas en GitHub Pages dentro de un subdirectorio, la ruta absoluta se rompería; Vite permite fijar `base` en `vite.config.js` para resolverlo.
- **Tamaño del JSON.** Si supera unos pocos megabytes, conviene dividirlo por secciones o cargar solo lo necesario al pulsar cada botón.
- **Validación.** Comprueba que el JSON existe y tiene la forma esperada antes de renderizar, y muestra un mensaje de error si la carga falla.

Para afinar la propuesta, ¿puedes contarme qué tipo de datos contiene el JSON (ventas, métricas, encuestas, etc.) y qué visualizaciones necesitas? Con eso puedo ajustar la estructura del archivo y los gráficos.

## Pregunta a Claude

Deseo utilizar .JSX para la elaboracion de componentes. En la sugerencia me recomienda utilizar JavaScript (o TypeScript) ,  puedo crear los componentes con jsx?

R/

**Sí, puedes crear los componentes con JSX.** JSX no es un lenguaje distinto: es una extensión de sintaxis de JavaScript que permite escribir HTML dentro del código. Como los navegadores no lo entienden directamente, necesita un transpilador, y Vite ya lo incluye. Por eso, en lugar de JavaScript puro, lo más práctico es usar **JSX con React**, un framework que organiza la interfaz en componentes reutilizables.

## Stack actualizado con JSX

| Capa | Tecnología | Motivo |
|---|---|---|
| Build | **Vite** | Procesa los archivos `.jsx`, genera `dist/` y copia `public/data/datos.json`. |
| Componentes | **React** con archivos **`.jsx`** | Cada parte del dashboard (botones, tarjetas, gráfico) es un componente con su propio archivo. |
| Lenguaje | **JavaScript** o **TypeScript** (`.tsx`) | JSX funciona con ambos. Con TypeScript, los componentes se escriben en `.tsx`. |
| Estado | **useState** y **useEffect** de React | Controlan la sección activa y la carga del JSON sin librerías adicionales. |
| Gráficos | **react-chartjs-2** (sobre Chart.js) | Envuelve Chart.js en componentes React, por ejemplo `<Bar />` o `<Line />`. |
| Estilos | CSS o **Tailwind CSS** | Ambos funcionan con componentes JSX. |
| Hosting | GitHub Pages, Netlify o Vercel | Publican la carpeta `dist/` como sitio estático. |

Si quieres una alternativa más ligera a React, **Preact** también usa JSX y tiene una API casi idéntica, con un tamaño mucho menor. Para un dashboard pequeño, cualquiera de las dos funciona bien; React tiene más documentación y ejemplos.

## Creación del proyecto

```bash
npm create vite@latest mi-dashboard -- --template react
cd mi-dashboard
npm install
npm install chart.js react-chartjs-2
npm run build   # genera dist/
```

Para usar TypeScript, cambia la plantilla a `react-ts`. Los archivos serán `.tsx` en lugar de `.jsx`.

## Estructura del proyecto

```
mi-dashboard/
├── public/
│   └── data/
│       └── datos.json          → se copia a dist/data/
├── src/
│   ├── components/
│   │   ├── BotonesSeccion.jsx  → botones de navegación
│   │   ├── TarjetaResumen.jsx  → totales y métricas
│   │   └── GraficoBarras.jsx   → gráfico de Chart.js
│   ├── App.jsx                 → estado principal y carga del JSON
│   ├── main.jsx                → punto de entrada
│   └── index.css
├── index.html
└── dist/                       → salida del build
```

## Ejemplo de componentes

Un componente de botones que recibe datos y notifica la selección mediante props:

```jsx
// src/components/BotonesSeccion.jsx
export default function BotonesSeccion({ secciones, activa, onSeleccionar }) {
  return (
    <nav>
      {secciones.map((s) => (
        <button
          key={s.clave}
          className={s.clave === activa ? 'activo' : ''}
          onClick={() => onSeleccionar(s.clave)}
        >
          {s.nombre}
        </button>
      ))}
    </nav>
  );
}
```

El componente principal conecta los datos con la interfaz:

```jsx
// src/App.jsx
import { useEffect, useState } from 'react';
import BotonesSeccion from './components/BotonesSeccion.jsx';
import TarjetaResumen from './components/TarjetaResumen.jsx';

export default function App() {
  const [datos, setDatos] = useState(null);
  const [activa, setActiva] = useState('ventas');

  useEffect(() => {
    fetch('data/datos.json')
      .then((r) => r.json())
      .then(setDatos);
  }, []);

  if (!datos) return <p>Cargando datos...</p>;

  const seccion = datos[activa];

  return (
    <main>
      <h1>Dashboard</h1>
      <BotonesSeccion
        secciones={Object.entries(datos).map(([clave, s]) => ({ clave, nombre: s.nombre }))}
        activa={activa}
        onSeleccionar={setActiva}
      />
      <TarjetaResumen total={seccion.total} />
    </main>
  );
}
```

Con esta estructura, al pulsar un botón se actualiza `activa`, React vuelve a renderizar los componentes afectados y el dashboard cambia sin recargar la página.

## Puntos a tener en cuenta

- **Los archivos `.jsx` se compilan en el build.** Lo que se publica en `dist/` es JavaScript estándar, así que el usuario final no necesita nada especial.
- **Mantén el `fetch` del JSON en un solo lugar** (normalmente `App.jsx`) y pasa los datos a los componentes por props. Así los componentes son más fáciles de probar y reutilizar.
- **Igual que antes, no abras `dist/index.html` con doble clic**, porque `fetch()` no funciona con `file://`. Usa `npm run preview` para probar el build.
- **En GitHub Pages dentro de un subdirectorio**, configura `base` en `vite.config.js`.

¿Quieres que prepare los componentes para los gráficos con `react-chartjs-2`, o prefieres empezar por la estructura de `datos.json` para ajustar los componentes a tus datos?




