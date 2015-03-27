# Usando streams

> Es recomendable crear plugins que soporten streams. La siguiente información será util para plugins que manipulen streams.

> Asegúrate de seguir la mejor práctica respecto a manejo de excepciones y añadiendo código que permita a gulp re-emitir el primer error capturado durante la transformación del contenido.

[Crear un plugin](README.md) > Writing stream based plugins

## Uso de streams

Vamos a implementar un plugin que añada texto al principio de un archivo. El siguiente plugin brinda soporte a todas las formas de file.contents.

```js
var through = require('through2');
var gutil = require('gulp-util');
var PluginError = gutil.PluginError;

// constantes
const PLUGIN_NAME = 'gulp-prefixer';

function prefixStream(prefixText) {
  var stream = through();
  stream.write(prefixText);
  return stream;
}

// función principal del plugin (manejando archivos)
function gulpPrefixer(prefixText) {
  if (!prefixText) {
    throw new PluginError(PLUGIN_NAME, 'Missing prefix text!');
  }

  prefixText = new Buffer(prefixText); // guardar en memoria por adelantado

  // crear un through stream por el que cada archivo pasará
  var stream = through.obj(function(file, enc, cb) {
    if (file.isBuffer()) {
      this.emit('error', new PluginError(PLUGIN_NAME, 'Buffers not supported!'));
      return cb();
    }

    if (file.isStream()) {
      // definir el streamer que transformará el contenido
      var streamer = prefixStream(prefixText);
      // manejar las excepciones del streamer y emitir un error
      streamer.on('error', this.emit.bind(this, 'error'));
      // inicia la transformación
      file.contents = file.contents.pipe(streamer);
    }

    // asegúrate que el archivo pase al siguiente plugin
    this.push(file);

    // decirle gulp que hemos terminado con este archivo
    cb();
  });

  // devolviendo el stream
  return stream;
}

// exportando la función principal del plugin
module.exports = gulpPrefixer;
```

El plugin anterior puede ser usado de la siguiente manera:

```js
var gulp = require('gulp');
var gulpPrefixer = require('gulp-prefixer');

gulp.src('files/**/*.js', { buffer: false })
  .pipe(gulpPrefixer('prepended string'))
  .pipe(gulp.dest('modified-files'));
```

## Algunos plugins usando streams

* [gulp-svgicons2svgfont](https://github.com/nfroidure/gulp-svgiconstosvgfont)
