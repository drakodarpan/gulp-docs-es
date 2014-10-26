# Crear un plugin

Si estás pensando en crear un plugin para gulp, te ahorrás tiempo leyendo la documentación.

* [Guía](guidelines.md) (lectura RECOMENDADA)
* [Uso de buffers](using-buffers.md)
* [Uso de streams](dealing-with-streams.md)
* [Pruebas](testing.md)

## Qué hace

### Streaming objetos archivo

Los plugins de gulp siempre devuelven un stream en [modo objeto](http://nodejs.org/api/stream.html#stream_object_mode) que hace lo siguiente:

1. Recibe [objetos File de vinyl](http://github.com/wearefractal/vinyl)
2. Produce [objetos File de vinyl](http://github.com/wearefractal/vinyl)

También conocidos como [transform streams](http://nodejs.org/api/stream.html#stream_class_stream_transform_1) (algunas veces también denominados through streams).  Transform streams son streams de lectura y escritura que manipulan objetos que los atraviesan. 

### Modificando el contenido de archivos

Archivos de vinyl disponen de 3 tipos de atributos de contenido:

- [Streams](dealing-with-streams.md)
- [Buffers](using-buffers.md)
- Vacío (null) - Útil por ejemplo para [rimraf](https://www.npmjs.org/package/rimraf), limpieza, donde el contenido no es necesario.

## Otros recursos

* [objeto File](https://github.com/wearefractal/gulp-util/#new-fileobj)
* [PluginError](https://github.com/gulpjs/gulp-util#new-pluginerrorpluginname-message-options)
* [event-stream](https://github.com/dominictarr/event-stream)
* [BufferStream](https://github.com/nfroidure/BufferStream)
* [gulp-util](https://github.com/wearefractal/gulp-util)


## Plugins de ejemplo

* [sindresorhus' gulp plugins](https://github.com/search?q=%40sindresorhus+gulp-)
* [Fractal's gulp plugins](https://github.com/search?q=%40wearefractal+gulp-)
* [gulp-replace](https://github.com/lazd/gulp-replace)


## Acerca de streams

Si no estás familiarizado con streams, necesitarás leer sobre ellos:

* https://github.com/substack/stream-handbook (lectura RECOMENDADA)
* http://nodejs.org/api/stream.html
* [Streams](http://nodejs-es.github.io/api/all.html#all_es_streams) - Documentación de Node en Español.
* [Tuberías](http://es.wikipedia.org/wiki/Tuber%C3%ADa_(inform%C3%A1tica). (Wikipedia)

Otras librerías que no manipulan streams pero están hechas para ser usadas con gulp se etiquetan con la palabra clave [gulpfriendly](https://npmjs.org/browse/keyword/gulpfriendly) en npm.
