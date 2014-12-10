# Combinar streams para manejar excepciones

Por defecto, al emitir un error en un stream se genera una excepción al menos que este ya tengo un listener en el evento `error`. Esto se vuelve ligeramente complicado al trabajar con conexiones complejas de streams.

Usando [stream-combiner2](https://github.com/substack/stream-combiner2) es posible convertir una serie de streams en uno solo, por lo que solo es necesario escuchar por el `error` en un solo lugar del código.

A continuación un ejemplo usando multistream en un gulpfile.


```js
var combiner = require('stream-combiner2');
var uglify = require('gulp-uglify');
var gulp = require('gulp');

gulp.task('test', function() {
  var combined = combiner.obj([
    gulp.src('bootstrap/js/*.js'),
    uglify(),
    gulp.dest('public/bootstrap')
  ]);

  // cualquier error en el stream anterior puede ser manejado a
  // través de la siguiente función en vez de generar una excepción
  combined.on('error', console.error.bind(console));

  return combined;
});
```
