# Uso de buffers

> He aquí alguna información de cómo crear un plugin para gulp que manipula buffers

[Crear un plugin](README.md) > Usando de buffers

## Usando de buffers

Si tu plugin depende en una librería basada buffers, es probable que elijas basar tu plugin alrededor de file.contents como un buffer. Vamos a implementar un plugin que anteponga texto a archivos:


```js
var through = require('through2');
var gutil = require('gulp-util');
var PluginError = gutil.PluginError;

// constantes
const PLUGIN_NAME = 'gulp-prefixer';

// función principal del plugin (manejando archivos)
function gulpPrefixer(prefixText) {
  if (!prefixText) {
    throw new PluginError(PLUGIN_NAME, 'Missing prefix text!');
  }

  prefixText = new Buffer(prefixText); // guardar en memoria por adelantado

  // crear un through stream por el que cada archivo pasará
  var stream = through.obj(function(file, enc, cb) {
    if (file.isStream()) {
      this.emit('error', new PluginError(PLUGIN_NAME, 'Streams are not supported!'));
      return cb();
    }

    if (file.isBuffer()) {
      file.contents = Buffer.concat([prefixText, file.contents]);
    }

    // asegúrate que el archivo pase al siguiente plugin
    this.push(file);

    // decirle gulp que hemos terminado con este archivo
    cb();
  });

  // devolviendo el stream
  return stream;
};

// exportando la función principal del plugin
module.exports = gulpPrefixer;
```

El plugin anterior se puede usar así:

```js
var gulp = require('gulp');
var gulpPrefixer = require('gulp-prefixer');

gulp.src('files/**/*.js')
  .pipe(gulpPrefixer('cadena antepuesta'))
  .pipe(gulp.dest('modified-files'));
```

## Manejando streams

Desafortunadamente, el plugin anterior falla al utilizar `gulp.src` en modo non-buffered (streaming). Debes de dar soporte a streams siendo posible. Ver [Usando streams]((dealing-with-streams.md)) para más información.

## Algunos plugins basados en buffers

* [gulp-coffee](https://github.com/wearefractal/gulp-coffee)
* [gulp-svgmin](https://github.com/ben-eb/gulp-svgmin)
* [gulp-marked](https://github.com/lmtm/gulp-marked)
* [gulp-svg2ttf](https://github.com/nfroidure/gulp-svg2ttf)
