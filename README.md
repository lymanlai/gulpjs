# all about gulpjs

1. https://github.com/gulpjs/gulp
2. https://github.com/gulpjs/gulp/tree/master/docs/recipes#recipes
1. http://gulpjs.com/plugins/
2. https://github.com/gulpjs/gulp/blob/master/docs/API.md
3. https://github.com/gulpjs/gulp/blob/master/docs/README.md#articles

# code snipping

## https://github.com/google/web-starter-kit/blob/master/gulpfile.js
TL;NP

## https://github.com/gulpjs/gulp/blob/master/docs/recipes/rebuild-only-files-that-change.md
```
var gulp = require('gulp');
var sass = require('gulp-sass');
var watch = require('gulp-watch');

gulp.task('default', function() {
  return gulp.src('sass/*.scss')
    .pipe(watch('sass/*.scss'))
    .pipe(sass())
    .pipe(gulp.dest('dist'));
});
```

## https://github.com/gulpjs/gulp/blob/master/docs/recipes/incremental-builds-with-concatenate.md
```
var gulp = require('gulp');
var header = require('gulp-header');
var footer = require('gulp-footer');
var concat = require('gulp-concat');
var jshint = require('gulp-jshint');
var cached = require('gulp-cached');
var remember = require('gulp-remember');

var scriptsGlob = 'src/**/*.js';

gulp.task('scripts', function() {
  return gulp.src(scriptsGlob)
      .pipe(cached('scripts'))        // only pass through changed files
      .pipe(jshint())                 // do special things to the changed files...
      .pipe(header('(function () {')) // e.g. jshinting ^^^
      .pipe(footer('})();'))          // and some kind of module wrapping
      .pipe(remember('scripts'))      // add back all files to the stream
      .pipe(concat('app.js'))         // do things that require all files
      .pipe(gulp.dest('public/'));
});

gulp.task('watch', function () {
  var watcher = gulp.watch(scriptsGlob, ['scripts']); // watch the same files in our scripts task
  watcher.on('change', function (event) {
    if (event.type === 'deleted') {                   // if a file is deleted, forget about it
      delete cached.caches.scripts[event.path];       // gulp-cached remove api
      remember.forget('scripts', event.path);         // gulp-remember remove api
    }
  });
});
```

## http://jaysoo.ca/2014/01/27/gruntjs-vs-gulpjs/
Example from gulp-reload:
```
var lr = require('tiny-lr'),
    gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload'),
    server = lr();

gulp.task('less', function () {
  gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload(server));
});

gulp.task('watch', function () {
  server.listen(35729, function (err) {
    if (err) return console.log(err);

    gulp.watch('less/*.less', function () {
        gulp.run('less');
    });
  });
});
```

## http://www.bymichaellancaster.com/blog/why-i-like-gulpjs-more-than-gruntjs/
```
// Require modules
var gulp = require('gulp')
    , uglify = require('gulp-uglify')
    , minifyHTML = require('gulp-minify-html')
    , sass = require('gulp-sass');

// Here's the tasks
gulp.task('build', function(){
    var dist = 'dist/'
        , dirPublic = 'public/'
        , distStylesheets = dist + dirPublic + 'stylesheets/'
        , distJavascripts = dist + dirPublic + 'javascripts/';

    gulp.src('public/stylesheets/scss/*.scss')
        .pipe(sass())
        .pipe(gulp.dest(distStylesheets));

    gulp.src('*.html')
        .pipe(minifyHTML())
        .pipe(gulp.dest(dist))

    gulp.src('public/javascripts/*.js')
        .pipe(uglify())
        .pipe(gulp.dest(distJavascripts))
});

// Gulp watch for file changes
gulp.task('default', function() {
    gulp.watch([
        'public/stylesheets/scss/**',
        'public/javascripts/*.js',
        '*.html',
        '!dist/**'
    ], function(event) {
        gulp.run('build');
    });
});
```

## 

