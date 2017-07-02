# GulpProjects
How to use gulp.js
What is a Task Runner

Why would you want to use a task runner? Well they’re small applications that automate often time consuming and boring tasks. If you ever need to do any of the following then a task runner is for you:-

Minification and concatenation of JavaScript and CSS files
CSS Preprocessing
Image optimisation
Unit testing and linting
By creating a task file you can instruct the task manager to take care of many development tasks and watch for changes in relevant files. All you’ll need to do is start up the task runner and get to work on the more interesting parts of your project.

Gulp vs. Grunt

Unlike Grunt which is all about configuration files, Gulp is about streams and code-over-configuration. That might sound off putting to some, but bear with me.

Like Grunt, Gulp uses Node, but you don’t need to know Node to use it. Some basic JavaScript knowledge will help, but is not essential as using Gulp is really simple.

The advantage of Gulp’s code-over-configuration approach is that you end up with a cleaner and easier to read task file with greater consistency between tasks. Each Grunt task can be considerably different in setup.

Gulp uses streams which I won’t go into here, but this is a good resource if you’re interested. The advantage of streams is that you put a file into the stream and get one out; there’s no need for temporary files and folders like with Grunt. The flow is easier to read from the code too.

One other advantage of Gulp over Grunt is that Gulp plugins are kept simple. They are designed to do one thing and do it well. Gulp simply just applies them. With Grunt you can find yourself running into conflict issues or shared functionality. We’ll take a look at plugins in a bit.

Let’s Get Started and Install Gulp

As mentioned earlier Gulp uses Node, so if you haven’t already got it you’ll need to install it. You can get it from the Node website, just hit the big install button and follow the instructions. If you’re not sure if you’ve got Node installed open up a terminal and run the command:-

node -v
If you get something like ‘command not found’ you’ll need to install it.

Next you’ll need to globally install Gulp using the Node Package Manager (npm) from the terminal:-

npm install -g gulp
If you’re running this from OSX or Linux you’ll probably need to run this as an administrator so prepend it with sudo.

Gulp is now installed globally, but will need installing locally on a project before you can use it.

Add Gulp to a Project

From the terminal navigate to the project folder you want to use Gulp on; something like:-

cd ~/projects/my-project
From now on all the commands you’ll be running will be from the project’s root folder. The project needs a file called package.json. So create one in the project’s folder, the contents of the file should be something like:-

{
  "name": "my-project",
  "version": "0.1.0",
  "devDependencies": {
  }
}
Alternatively you could generate the file by running npm init. It’s up to you which method you prefer.

Now install Gulp locally:-

npm install gulp --save-dev
The --save-dev flag will automatically add Gulp to the depenencies of your package.json file. You should see a new line like:-

"gulp": "~3.5.2"
The number might be a bit different, but that shouldn’t matter. That just states which version of Gulp is required. You should also see a new folder in your project called node_modules (check using ls or dir if you’re using Windows).

You need to create one more file in the project’s folder called gulpfile.js (from now on refered to as just the gulpfile). This file will contain the instructions for what you want Gulp to actually do. For now we’ll just include Gulp:-

// Include gulp
var gulp = require('gulp');
We’ll add some tasks to this file in a moment, but first it is perhaps worth just quickly introducing the four Gulp methods that we will be using:-

gulp.task(name, fn) – registers a function with a name
gulp.watch(glob, fn) – runs a function when a file that matches the glob changes
gulp.src(glob) – returns a readable stream
gulp.dest(folder) – returns a writable stream
Don’t worry if that doesn’t mean much to you yet. All will hopefully start to make sense when we start putting Gulp into action…

Concatenating Files with Gulp

Gulp on its own doesn’t do a lot. We need to install plugins and add tasks to the gulpfile to put Gulp into action. To concatenate files we’ll need the gulp-concat plugin; to install it run this from the command line:-

npm install gulp-concat --save-dev
Again, if you check your package.json file you should see a new line referencing the newly installed plugin:-

"gulp-concat": "~2.1.7"
Now we need to add a task to the gulpfile that will instruct Gulp to concatenate some files. Let’s concatenate the project’s JavaScript files:-

// Include gulp
var gulp = require('gulp');
 // Include plugins
var concat = require('gulp-concat');
 // Concatenate JS Files
gulp.task('scripts', function() {
    return gulp.src('src/js/*.js')
      .pipe(concat('main.js'))
      .pipe(gulp.dest('build/js'));
});
 // Default Task
gulp.task('default', ['scripts']);
What’s happening here is we’re including the gulp-concat plugin and naming it with the variable concat. We then define the task using gulp.task, naming it ‘scripts’. The task involves three processes:-

We grab the files we want to concatenate using gulp.src (any file with the extension .js in the directory src/js/.
We then concatenate these files as main.js.
Finally we tell Gulp where to put main.js, in this case the directory build/js.
The default task tells Gulp what tasks to call when it’s run, for now that’s just the scripts task. To run Gulp just run:-

gulp
Hopefully if everything’s gone right main.js will have been created in build/js and will be a concatenation of all the JS files.

If you need to concatenate files in a particular order, then you can always pass an array to gulp.src. For example:-

gulp.src(['src/js/plugins/*.js', 'src/js/main.js'])
Minify the JavaScript with Gulp

We’re now going to minify the concatenated JavaScript file. Again we need to:-

Install a plugin
Add/Modify a task to gulpfile
Minifying in Gulp is done using gulp-uglify; install it and another plugin called gulp-rename using NPM:-

npm install gulp-uglify gulp-rename --save-dev
That should have installed both plugins and added them to package.json. We’re now going to modify the scripts task so that we minify the JavaScript file from before:-

// Include gulp
var gulp = require('gulp');
 // Include plugins
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');
 // Concatenate & Minify JS
gulp.task('scripts', function() {
    return gulp.src('src/js/*.js')
      .pipe(concat('main.js'))
        .pipe(rename({suffix: '.min'}))
        .pipe(uglify())
        .pipe(gulp.dest('build/js'));
});
 // Default Task
gulp.task('default', ['scripts']);
Our scripts task now concatenates the JavaScript files as main.js, renames the file main.min.js using the gulp-rename plugin and then minifies it using uglify. The final file is output to build/js.

Run Gulp again and you should find the minified JavaScript:-

gulp
Reducing the number of assets a browser needs to download to a single small sized file will instantly improve a site’s performance thanks to Gulp.

Preprocess CSS with Gulp

Now it’s time to add a new task for compiling Sass files into regular CSS.

There is a gulp-sass plugin that uses the Node version of Sass, but we’ll use the official Ruby Sass plugin (gulp-ruby-sass) as it’s more stable and feature rich:-

npm install --save-dev gulp-ruby-sass
You will need to have Ruby and Sass installed to use this plugin, but if you’re already using Sass you’ve probably already got them.

Now in the gulpfile add a new task (updated 28/04/2015):-

var sass = require('gulp-ruby-sass');
 gulp.task('sass', function() {
    return sass('src/scss/style.scss', {style: 'compressed'})
        .pipe(rename({suffix: '.min'}))
        .pipe(gulp.dest('build/css'));
});
This task will take the style.scss file (and any Sass partials imported into it), compile it to minified CSS (using Sass’s in-built compression), rename it style.min.scss and output the final file to build/css.

Before we can run the task we need to add it to Gulp’s default tasks:-

// Default Task
gulp.task('default', ['scripts', 'sass']);
Run gulp and you’ll find it not only generates the concatenated, minified JavaScript file, but also compiles the Sass file:-

gulp
If Less CSS is more your thing there’s a plugin for that too.

Image Optimisation with Gulp

Hopefully, you should be getting the hang of how things work with Gulp by now. It can do more than work with scripts and stylesheets. Let’s now optimise some images using the gulp-imagemin and gulp-cache plugins. I’ll leave it up to you to install these using NPM. It’s just the same process as before.

We need to add another new task to our gulpfile:-

var imagemin = require('gulp-imagemin');
var cache = require('gulp-cache');
 gulp.task('images', function() {
  return gulp.src('src/images/**/*')
    .pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))
    .pipe(gulp.dest('build/img'));
});
This takes any images in our defined source and compresses them using the gulp-imagemin plugin. We’ve wrapped it in the gulp-cache plugin which will ensure only new or changed images get compressed. Again to make sure this task runs when we run Gulp it needs adding to the default task list:-

// Default Task
gulp.task('default', ['scripts', 'sass', 'images']);
Watching for Changes

This is all very useful, but at the moment we’re needing to run Gulp each time we make a change to a file, which isn’t all that clever. The good news is that Gulp can watch for changes and automatically run the tasks.

Unlike Grunt, Gulp can do this out-of-the-box. So this time there’s no need to install a plugin.

Add the watch task:-

gulp.task('watch', function() {
   // Watch .js files
  gulp.watch('src/js/*.js', ['scripts']);
   // Watch .scss files
  gulp.watch('src/scss/*.scss', ['sass']);
   // Watch image files
  gulp.watch('src/images/**/*', ['images']);
 });
Add make sure Gulp runs this by default:-

// Default Task
gulp.task('default', ['scripts', 'sass', 'images', 'watch']);
This time when you run gulp it will keep running and watching for changes in the src/js, src/sass and src/images folders and run the scripts, sass and images tasks when a change is detected in their respective folder.

Finally

Let’s pull all this together and look at the complete gulpfile:-

// Include gulp
var gulp = require('gulp');
 // Define base folders
var src = 'src/';
var dest = 'build/';
 // Include plugins
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');
var sass = require('gulp-ruby-sass');
var imagemin = require('gulp-imagemin');
var cache = require('gulp-cache');
 // Concatenate & Minify JS
gulp.task('scripts', function() {
    return gulp.src(src + 'js/*.js')
      .pipe(concat('main.js'))
        .pipe(rename({suffix: '.min'}))
        .pipe(uglify())
        .pipe(gulp.dest(dest + 'js'));
});
 // Compile CSS from Sass files
gulp.task('sass', function() {
    return sass('src/scss/style.scss', {style: 'compressed'})
        .pipe(rename({suffix: '.min'}))
        .pipe(gulp.dest('build/css'));
});
 gulp.task('images', function() {
  return gulp.src(src + 'images/**/*')
    .pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))
    .pipe(gulp.dest(dest + 'img'));
});
 // Watch for changes in files
gulp.task('watch', function() {
   // Watch .js files
  gulp.watch(src + 'js/*.js', ['scripts']);
   // Watch .scss files
  gulp.watch(src + 'scss/*.scss', ['sass']);
   // Watch image files
  gulp.watch(src + 'images/**/*', ['images']);
 });
 // Default Task
gulp.task('default', ['scripts', 'sass', 'images', 'watch']);
A couple of small changes have been made to the gulpfile to show how use can use JavaScript variables to define the source and destination folders used across the script. This should make it easier to maintain.

Just run gulp on the command line and all the tasks should start running. Another neat feature of Gulp is that we can run specific tasks just by passing the task name as a parameter when calling Gulp from the terminal. For example, to just compile the CSS you can run the sass task from above as:-

gulp sass
This means we can also define tasks not called by the default task and just call them when needed.


