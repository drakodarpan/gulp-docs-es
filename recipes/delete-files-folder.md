# Eliminar archivos y carpetas

Puede que quieras eliminar archivos y carpetas en tu proceso. Ya que eliminar archivos no necesita del contenido de los archivos, no hay razón para usar gulp. Esta es una oportunidad excelente para simplemente usar un modulo de node

Utilicemos el módulo [`del`](https://github.com/sindresorhus/del) para este ejemplo ya que soporta múltiples archivos y [globbing](https://github.com/sindresorhus/multimatch#globbing-patterns):

```sh
$ npm install --save-dev gulp del
```

Imagina la siguiente estructura de archivos:

```
.
├── dist
│   ├── report.csv
│   ├── desktop
│   └── mobile
│       ├── app.js
│       ├── deploy.json
│       └── index.html
└── src
```

En el gulpfile queremos limpiar los contenidos de la carpeta `mobile` folder antes de ejecutar nuestro proceso:

```js
var gulp = require('gulp');
var del = require('del');

gulp.task('clean:mobile', function (cb) {
  del([
    'dist/report.csv',
    // usemos globbing para marcar todo dentro de la carpeta `mobile`
    'dist/mobile/**',
    // no queremos limpiar este archivo, así que lo negamos
    '!dist/mobile/deploy.json'
  ], cb);
});

gulp.task('default', ['clean:mobile']);
```

## Elimina archivos

Puede que quieras eliminar algunos archivos después de procesarlos.

Usaremos [vinyl-paths](https://github.com/sindresorhus/vinyl-paths) para obtener la ruta del archivo fácilmente en el stream y pasarla al método `del`.

```sh
$ npm install --save-dev gulp del vinyl-paths
```

Imagina la siguiente estructura de archivos:

```
.
├── tmp
│   ├── rainbow.js
│   └── unicorn.js
└── dist
```

```js
var gulp = require('gulp');
var stripDebug = require('gulp-strip-debug'); // solo como un ejemplo
var del = require('del');
var vinylPaths = require('vinyl-paths');

gulp.task('clean:tmp', function () {
  return gulp.src('tmp/*')
    .pipe(stripDebug())
    .pipe(gulp.dest('dist'))
    .pipe(vinylPaths(del));
});

gulp.task('default', ['clean:tmp']);
```

Haz esto sólo si ya estas usando otros plugins, de lo contrario sólo usar el módulo directamente como `gulp.src` es costoso.
