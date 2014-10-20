# Tests

> Testear tu plugin es la única manera de asegurar calidad. Da confianza en tus usuarios y hace tu vida más simple.

[Crear un plguin](README.md) > Tests

## Herramientas

La mayoría de los plugins usan [mocha](https://github.com/visionmedia/mocha), [should](https://github.com/visionmedia/should.js) y [event-stream](https://github.com/dominictarr/event-stream) para ayudarles a testear. Los siguientes ejemplos harán uso de estas herramientas.

## Testeando plugins para modo streaming

```js
var assert = require('assert');
var es = require('event-stream');
var File = require('vinyl');
var prefixer = require('../');

describe('gulp-prefixer', function() {
  describe('in streaming mode', function() {

    it('should prepend text', function(done) {

      // crear un archivo falso
      var fakeFile = new File({
        contents: es.readArray(['stream', 'with', 'those', 'contents'])
      });

      // Crear un prefixer stream con el plugin
      var myPrefixer = prefixer('prependthis');

      // escribe el archivo falso
      myPrefixer.write(fakeFile);

      // esperar a que el archivo vuelva
      myPrefixer.once('data', function(file) {
        // asegurase de que ha vuelto igual que entró
        assert(file.isStream());

        // guardar en memoria el contenido asegurandose
        // de que fue colocado al principio
        file.contents.pipe(es.wait(function(err, data) {
          // comprobar el contenido
          assert.equal(data, 'prependthisstreamwiththosecontents');
          done();
        }));
      });

    });

  });
});
```

## Testeando plugins para modo buffer

```js
var assert = require('assert');
var es = require('event-stream');
var File = require('vinyl');
var prefixer = require('../');

describe('gulp-prefixer', function() {
  describe('in buffer mode', function() {

    it('should prepend text', function(done) {

      // crea el archivo falso
      var fakeFile = new File({
        contents: new Buffer('abufferwiththiscontent')
      });

      // Crear un prefixer stream con el plugin
      var myPrefixer = prefixer('prependthis');

      // escribe el archivo falso
      myPrefixer.write(fakeFile);

      // esperar a que el archivo vuelva
      myPrefixer.once('data', function(file) {
        // asegurase de que ha vuelto igual que entró
        assert(file.isBuffer());

        // comprobar el contenido
        assert.equal(file.contents.toString('utf8'), 'prependthisabufferwiththiscontent');
        done();
      });

    });

  });
});
```

## Algunos plugins con Tests de alta calida

* [gulp-cat](https://github.com/ben-eb/gulp-cat/blob/master/test.js)
* [gulp-concat](https://github.com/wearefractal/gulp-concat/blob/master/test/main.js)