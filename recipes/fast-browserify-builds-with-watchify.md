# Procesos browserify rápidos con watchify

A medida que un projecto [browserify](http://github.com/substack/node-browserify) comienza a expandirse, su tiempo de proceso, poco a poco, va aumentando lentamente con el tiempo. Aunque puede empezar por 1 segundo, es posible estar esperando 30 segundos para que el proyecto termine de procesarse en casos en los que este es particularmente grande.

Esto es por lo que [substack](http://github.com/substack) escribió [watchify](http://github.com/substack/watchify), un procesador browserify persistente que observa cómo cambian los archivos y *solo procesa lo que necesita*. De esta forma, el primer procesado puede que siga tardando 30 segundos, pero los siguientes pueden tardar menos de 100ms lo que es un gran mejora.

Watchify no tiene un gulp plugin, pero no lo necesita: puedes usar [vinyl-source-stream](http://github.com/hughsk/vinyl-source-stream) para canalizar el stream a línea de proceso gulp.

``` javascript
var gulp = require('gulp');
var gutil = require('gulp-util');
var sourcemaps = require('gulp-sourcemaps');
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');
var watchify = require('watchify');
var browserify = require('browserify');

var bundler = watchify(browserify('./src/index.js', watchify.args));
// añade aquí cualquier otra opción de browserify o transformadas
bundler.transform('brfs');

gulp.task('js', bundle); // y así puedes ejecutar `gulp js` para construir el archivo
bundler.on('update', bundle); // cuando cualquier dependencia cambie, ejecuta el bundler
bundler.on('log', gutil.log); // escribir los logs en el terminal

function bundle() {
  return bundler.bundle()
  // logear errores en caso de que ocurran
  .on('error', gutil.log.bind(gutil, 'Error Browserify'))
  .pipe(source('bundle.js'))
  // opcional, elimina si no quieres sourcemaps
  .pipe(buffer())
  .pipe(sourcemaps.init({loadMaps: true})) // carga el sourcemap del archivo browserify
  .pipe(sourcemaps.write('./')) // escribe el archivo .map
  //
  .pipe(gulp.dest('./dist'));
}
```
