# Hacer un stream de un buffer (contenido en memoria)

A veces puede que necesites comenzar un stream con archivos cuyo contenido es variable y no está en un archivo físico. En otras palabras, como ejecutar 'gulp' sin usar `gulp.src()`.

Digamos por ejemplo que tenemos un directorio con archivos js lib y otro con versiones de algún módulo. El objetivo de este proceso sería crear un js para cada versión, conteniendo todos los libs y la versión del module concatenado.

Desde un punto de vista lógico sería así:

* cargar los archivos lib
* concatenar el contenido de los archivos lib
* cargar las versiones de los archivos
* para cada archivo de versión, concatenar el contenido de los libs y el contenido del archivo de versión
* para cada archivo de versión, escribir el resultado en un archivo

Imagina esta estructura de archivos:

```sh
├── libs
│   ├── lib1.js
│   └── lib2.js
└── versions
    ├── version.1.js
    └── version.2.js
```

Deberías obtener:

```sh
└── output
    ├── version.1.complete.js # lib1.js + lib2.js + version.1.js
    └── version.2.complete.js # lib1.js + lib2.js + version.1.js
```

Un forma simple y modular de hacer esto podría ser la siguiente:

```js
var gulp = require('gulp');
var runSequence = require('run-sequence');
var source = require('vinyl-source-stream');
var vinylBuffer = require('vinyl-buffer');
var tap = require('gulp-tap');
var concat = require('gulp-concat');
var size = require('gulp-size');
var path = require('path');
var es = require('event-stream');

var memory = {}; // mantendremos los assets en memoria

// tarea para cargar el contenido de los archivos en memoria
gulp.task('load-lib-files', function() {
  // leer los archivos lib de memoria
  return gulp.src('src/libs/*.js')
  // concatenar todos los arhivos lib en uno
  .pipe(concat('libs.concat.js'))
  // "tocar" el stream para obtener el contenido de cada archivo
  .pipe(tap(function(file) {
    // guardar el contenido de los archivos en memoria
    memory[path.basename(file.path)] = file.contents.toString();
  }));
});

gulp.task('load-versions', function() {
  memory.versions = {};
  // leer los archivos lib del disco
  return gulp.src('src/versions/version.*.js')
  // "tocar" el stream para obtener el contenido de cada archivo
  .pipe( tap(function(file) {
    // guardar el contenido del archivo en los assets
    memory.versions[path.basename(file.path)] = file.contents.toString();
  }));
});

gulp.task('write-versions', function() {
  // guardamos todas las versiones de los archivos en un array
  var availableVersions = Object.keys(memory.versions);
  // el array donde guardaremos todas las stream promises
  var streams = [];

  availableVersions.forEach(function(v) {
    // hacer un nuevo stream con un nombre falso
    var stream = source('final.' + v);
    // cargamos los datos de los lib concatenados
    var fileContents = memory['libs.concat.js'] +
      // añadimos la información referente a la version
      '\n' + memory.versions[v];

    streams.push(stream);

    // escribimos el contenido de los archivos en el stream
    stream.write(fileContents);

    process.nextTick(function() {
      // en el siguiente ciclo del proceso, cerrar el stream
      stream.end();
    });

    stream
    // transformar los datos crudos del stream en objeto/archivo vinyl
    .pipe(vinylBuffer())
    //.pipe(tap(function(file) { /* hacer algo con el contenido aquí */ }))
    .pipe(gulp.dest('output'));
  });

  return es.merge.apply(this, streams);
});

//============================================ nuestra tarea principal
gulp.task('default', function(taskDone) {
  runSequence(
    ['load-lib-files', 'load-versions'],  // cargar los archivos en paralelo
    'write-versions',  // listo para escribir una vez todos los recursos en memoria
    taskDone           // listo
  );
});

//============================================ nuestro observador
// observar solo una vez desdpués de haber ejectuado la tarea 'principal'
// y así todos los recursos ya estarán en memoria
gulp.task('watch', ['default'], function() {
  gulp.watch('./src/libs/*.js', function() {
    runSequence(
      'load-lib-files',  // solo hay que cargar los archivos que han cambiado
      'write-versions'
    );
  });

  gulp.watch('./src/versions/*.js', function() {
    runSequence(
      'load-versions',  // // solo hay que cargar los archivos que han cambiado
      'write-versions'
    );
  });
});
```
