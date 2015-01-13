# Primeros Pasos

#### 1. Instala gulp globalmente:

```sh
$ npm install --global gulp
```

#### 2. Instala gulp en las "devDependencies" de tu proyecto:

```sh
$ npm install --save-dev gulp
```

#### 3. Crea un `gulpfile.js` en el directorio raíz de tu proyecto:

```js
var gulp = require('gulp');

gulp.task('default', function() {
  // escribe aquí el codigo de la tarea `default`
});
```

#### 4. Ejecuta gulp:

```sh
$ gulp
```

La tarea default se ejecutará y no hará nada.

Para ejecutar tareas individualmente, usa `gulp <tarea> <otra_tarea>`.

## ¿Y ahora qué?

Tienes un archivo gulp vacío y todo está instalado ¿Cómo empezar a usar gulp realmente? Lee las [recetas](recipes) o la [lista de artículos](README.md#articles) para más información.

## `.src`, `.watch`, `.dest`, CLI args - ¿Qué es todo esto?

Para los detalles específicos del API consulta [su documentación](API.md).

## Plugins Disponibles

La comunidad de gulp está creciendo con nuevos plugins a diario. Visita la [página oficial](http://gulpjs.com/plugins/) para una la lista completa.
