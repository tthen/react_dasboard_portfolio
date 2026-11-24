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






