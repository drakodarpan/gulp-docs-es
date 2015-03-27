# Browserify + Uglify2 con sourcemaps

[Browserify](http://github.com/substack/node-browserify) se ha convertido en una herramienta importante e imprescindible, pero es necesario crear una interfaz para que funcione con gulp. A continuación una simple receta para usar Browserify con transformaciones y full sourcemaps que reproducen los archivos originales individualmente.

``` javascript
'use strict';

var browserify = require('browserify');
var gulp = require('gulp');
var transform = require('vinyl-transform');
var uglify = require('gulp-uglify');
var sourcemaps = require('gulp-sourcemaps');

gulp.task('javascript', function () {
  // transformar un stream normal de node a un gulp stream (buffer vinyl stream)
  var browserified = transform(function(filename) {
    var b = browserify({entries: filename, debug: true});
    return b.bundle();
  });

  return gulp.src('./app.js')
    .pipe(browserified)
    .pipe(sourcemaps.init({loadMaps: true}))
        // Añadir tasks de transformación aquí
        .pipe(uglify())
    .pipe(sourcemaps.write('./'))
    .pipe(gulp.dest('./dist/js/'));
});
```
