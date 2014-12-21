# Guía

> Aunque esta guía es totalmente opcional, recomendamos **ALTAMENTE** que todo el mundo las siga. Nadie quiere un mal plugin. Estas direcciones te harán la vida más fácil y te darán la seguridad de que tu plugin encaja bien con gulp.

[Crear un plugin](README.md) > Guía

1. Tu plugin no debe hacer algo que pueda hacerse fácilmente con un module node ya existente
  - Por ejemplo: eliminar un directorio no es un buen plugin para gulp.Usa un módulo como [del](https://github.com/sindresorhus/del) dentro de la tarea en su lugar.
  - Incluir cualquier cosa posible sólo por hacerlo no hará más que contaminar el con plugins de baja calidad que no tienen sentido con el paradigma de gulp.
  - ¡gulp plugins son para hacer operaciones basadas en archivos! Si estás creando un plugin que lleva a cabo operaciones demasiado complejas con streams simplemente haz un módulo para node en su lugar.
  - Un buen ejemplo de un gulp plugin sería algo como gulp-coffee. El módulo de coffee-script no funciona directamente con Vinyl, así que abstraemos los puntos problemáticos para hacerlo funcionar bien con gulp.
1. Tu plugin debe hacer solo **una cosa**, y hacerla bien.
  - Evita opciones de configuración que hagan a tu plugin hacer dos cosas totalmente diferentes.
  - Por ejemplo: Un plugin para minificar JS no debería tener una opción para añadir cabeceras también.
1. Tu plugin no debería hacer cosas que otro plugin es reponsable de hacer
  - No debe concatenar, [gulp-concat](https://github.com/wearefractal/gulp-concat) hace esto.
  - No debe añadir cabeceras [gulp-header](https://github.com/godaddy/gulp-header) hace esto.
  - No debe añadir footers [gulp-footer](https://github.com/godaddy/gulp-footer) hace esto.
  - Si es un un uso común pero opcional, documenta que tu plugin suele usarse con otro plugin.
  - ¡Usa otros gulp plugins en tu plugin! Esto reduce la cantidad de código que tendrás que escribir y garantiza un ecosistema estable.
1. Tu plugin **debe de estar testeado**.
  - Testear un gulp plugin es fácil, ni siquira necesitas gulp para hacerlo.
  - Mira otros plugins para ver ejemplos de esto.
1. Incluye la palabra clave `gulpplugin` en tu `package.json` así aparecerás en nuestra búsqueda.
1. No generes excepciones dentro de streams
  - En su lugar, emite un evento de **error**.
  - Si descubres un error **fuera** del stream, tal como una configuración invalida mientras el stream es creado, podrías generar una excepción.
1. Prefija cualquier error con el nombre de tu plugin
  - Por ejemplo: `gulp-replace: Cannot do regexp replace on a stream`
  - Utilizar la clase [PluginError](https://github.com/gulpjs/gulp-util#new-pluginerrorpluginname-message-options) del módulo gulp-util para facilitar esto.
1. El tipo de `file.contents` saliendo debe ser siempre igual al que entró
  - Si `file.contents` es null (de no-lectura) simplemente ignora el archivo y pásalo
  - Si `file.contents` es un Stream y es algo que tu no soportas simplemente emite un error.
    - No guardes en memoria buffers un stream con la intención de que tu plugin soporte streams. Cosas horribles pasarán.
1. No pases el objeto `File` downstream hasta que hayas terminado con el.
1. Usa [`file.clone()`](https://github.com/wearefractal/vinyl#clone) para clonar un archivo o crear uno nuevo en base a otro.
1. Usa módulos de nuestra [lista de módulos recomendados](recommended-modules.md) y hazte la vida más fácil.
1. **NO** requieras `gulp` como dependencia o `peerDependency` en tu plugin.
  - Es fenomenal que uses gulp para automatizar el workflow de tu plugin, pero asegúrate que lo incluyes como `devDependency`.
  - Requerir gulp como dependencia de tu plugin significa que todo aquel que instale tu plugin también reinstalará gulp, y todo su árbol de módulos dependientes.
  - No debería haber razón alguna en utilizar gulp dentro de la implementación de tu plugin. Si te hallas en un caso similar [abre una nueva incidencia](https://github.com/gulpjs/gulp/issues) y solicita ayuda.

### ¿Porqué son estas reglas tan estrictas?

gulp intenta ser simple para los usuarios. Dando una guía estricta es posible ppropcionar un ecosistema consistente y de alta calidad para todo el mundo. Y aunque esto añade un poco más de trabajo para los autores de plugins, elimina un montón de problemas en un futuro.

### ¿Qué ocurre si no sigo estas direcciones?

npm es libre para todo el mundo, y eres libre de hacer como lo que quieras pero estas direcciones se prescriben por una razón. Hará test de aceptación pronto que serán integrados con la búsqueda de plugings. Si no sigues la guía esto será visible de manera pública vía un sistema de puntuación. La gente siempre preferirá plugins que concuerden con "la filosofía de gulp".

### ¿Cómo se haría un buen plugin?

```js
// through2 es un pequeño módulo alrededor de node transform streams
var through = require('through2');
var gutil = require('gulp-util');
var PluginError = gutil.PluginError;

// Constantes
const PLUGIN_NAME = 'gulp-prefixer';

function prefixStream(prefixText) {
  var stream = through();
  stream.write(prefixText);
  return stream;
}

// función para manejar archivos
function gulpPrefixer(prefixText) {
  if (!prefixText) {
    throw new PluginError(PLUGIN_NAME, 'Missing prefix text!');
  }

  prefixText = new Buffer(prefixText);
  // guardar memoria por adelantado

  // Crear un stream através del que cada archivo pasará
  return through.obj(function(file, enc, cb) {
    if (file.isNull()) {
       // no hacer nada si no hay contenido
    }

    if (file.isBuffer()) {
      file.contents = Buffer.concat([prefixText, file.contents]);
    }

    if (file.isStream()) {
      file.contents = file.contents.pipe(prefixStream(prefixText));
    }

    // devolver archivo vacío
    cb(null, file);
  });
};

// Exportando la función principal del plugin
module.exports = gulpPrefixer;
```
