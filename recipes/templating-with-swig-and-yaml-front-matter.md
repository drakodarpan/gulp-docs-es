# Plantillas con Swig y YALM
Se pueden preparar plantillas usando `gulp-swig` y `gulp-front-matter`:

##### `page.html`

```html
---
title: Cosas que hacer
todos:
    - Primer que hacer
    - Segundo
    - Tercero
---
<html>
    <head>
        <title>{{ título }}</title>
    </head>
    <body>
        <h1>{{ título }}</h1>
        <ul>{% for todo in todos %}
          <li>{{ todo }}</li>
        {% endfor %}</ul>
    </body>
</html>
```

##### `gulpfile.js`

```js
var gulp = require('gulp');
var swig = require('gulp-swig');
var frontMatter = require('gulp-front-matter');

gulp.task('compile-page', function() {
  gulp.src('page.html')
      .pipe(frontMatter({ property: 'data' }))
      .pipe(swig())
      .pipe(gulp.dest('build'));
});

gulp.task('default', ['compile-page']);
