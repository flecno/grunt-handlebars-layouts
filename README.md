Grunt Handlebars Layouts
========================

Handlebars helpers which implement Jade-like layout blocks.

* Extend: extend layout (partial)
* Append
* Replace
* Prepend

A grunt.js task to render Handlebars templates against a context &amp; produce HTML

### Resources

* [Handlebars templates](http://handlebarsjs.com)
* [Mustache](http://mustache.github.io)
* [Treehouse Blog, Handlebars.js Part 2: Partials and Helpers](http://blog.teamtreehouse.com/handlebars-js-part-2-partials-and-helpers)
* [NetTuts+: An Introduction to Handlebars](http://net.tutsplus.com/tutorials/javascript-ajax/introduction-to-handlebars/)

## About

This task renders Handlebars templates against a context to produce HTML.

Inspired by [grunt-dust-html](https://github.com/ehynds/grunt-dust-html) and [handlebars-layouts](https://github.com/shannonmoeller/handlebars-layouts)

## Getting Started
This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-handlebars-layouts --save-dev
```

Next, add this line to your project's grunt file:

```js
grunt.loadNpmTasks("grunt-handlebars-layouts");
```

*This plugin was designed to work with Grunt 0.4.x. If you're still using grunt v0.3.x it's strongly recommended that [you upgrade](http://gruntjs.com/upgrading-from-0.3-to-0.4), but in case you can't please use [v0.3.3](https://github.com/gruntjs/grunt-contrib-handlebars/tree/grunt-0.3-stable).*

Lastly, add the configuration settings (see below) to your grunt file.

## Documentation

This task has two required properties, `src` and `dest`. `src` is the path to your source file and `dest` is the file this task will write to (relative to the grunt.js file). If this file already exists **it will be overwritten**.

```js
  files: {
    'dest.html': 'src.html'
  },
```

An example configuration looks like this:

```js
  grunt.initConfig({
    handlebarslayouts: {
      home: {
        files: {
          'dist/home.html': 'src/home.html'
        },
        options: {
          partials: [
            'src/partials/*.hbs',
            'src/layout.html'
          ],
          modules: [
            'src/helpers/helpers-*.js'
          ],
          basePath: 'src/',
          context: {
            title: 'Layout Test',
            items: [
              'apple',
              'orange',
              'banana'
            ]
          }
        }
      }
    }
  });
  grunt.registerTask('default', ['handlebarslayouts']);
```

### Optional Configuration Properties

This plugin can be customized by specifying the following options:

* `partials`: partials files.
* `basePath`: The base location to all your templates so that includes/partials can be resolved correctly.
* `context`: A JavaScript object to render the template against. This option supports a few different types:
* `modules`: add your customs helpers

Useful Handlebars Helpers : [handlebars-helpers](https://github.com/assemble/handlebars-helpers)

**String**: the location to a file containing valid JSON:

```js
context: '/path/to/file.json'
```

**Object**: a regular ol' JavaScript object:

```js
context: {
  pageTitle: 'My Awesome Website'
}
```

**Array**: an array of contexts, either string (files to parse) or JavaScript objects, or both. Each item in the array will be merged into a single context and rendered against the template:
    
```js
context: [
  'path/to/context.json',
  'path/to/another/context.json',
  { more: 'data' }
]
```

### Template example

```html
{{#extend "layout"}}
    {{#append "head"}}
      <link rel="stylesheet" href="assets/css/home.css" />
    {{/append}}

    {{#replace "body"}}
      <h2>Welcome Home</h2>

      <ul>
        {{#items}}
          <li>{{.}}</li>
        {{/items}}
      </ul>
    {{/replace}}

    {{#prepend "footer"}}
      <script src="assets/js/analytics.js"></script>
    {{/prepend}}
{{/extend}}
```

### Layout example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    {{#block "head"}}
          <title>{{title}}</title>
          <meta name="description" content="">
    {{/block}}
  </head>

  <body>
    <div class="site">
      <div class="site-hd" role="banner">
        {{#block "header"}}
            <h1>{{title}}</h1>
        {{/block}}
      </div>

      <div class="site-bd" role="main">
        {{#block "body"}}
            <h2>Hello World</h2>
        {{/block}}
      </div>

      <div class="site-ft" role="contentinfo">
        {{#block "footer"}}
            <small>&copy; 2014</small>
        {{/block}}
      </div>
    </div>

    {{#block "footer"}}
      <p>footer</p>
    {{/block}}
  
    {{> footer}}
  </body>
</html>
```

### Partial example

```html
<footer>footer</footer>
```

### Custom Helper example

Helpers can either be an object or a single register function. If register is on the object, then it calls the register function, passing in the engine. 

```js
module.exports.register = function (Handlebars, options)  { 
  Handlebars.registerHelper('foo', function ()  { 
    return 'foo';
  });
};
```

#### MD5 Helper (inspired by [handlebars-md5](https://github.com/neoziro/handlebars-md5))

```js
'use strict';

var crypto = require('crypto'),
    fs = require('fs');

// The module to be exported
var helpers = {
  md5: function (path) {
    var content = fs.readFileSync(path);
    return crypto.createHash('md5').update(content).digest('hex');
  }
};

// Export helpers
module.exports.register = function (Handlebars, options) {
  options = options || {};

  for (var helper in helpers) {
    if (helpers.hasOwnProperty(helper)) {
      Handlebars.registerHelper(helper, helpers[helper]);
    }
  }
};
```

#### Foo and bar Helper

```js
'use strict';

// The module to be exported
var helpers = {
  foo: function () {
    return 'foo';
  },
  bar: function () {
    return 'bar';
  }
};

// Export helpers
module.exports.register = function (Handlebars, options) {
  options = options || {};

  for (var helper in helpers) {
    if (helpers.hasOwnProperty(helper)) {
      Handlebars.registerHelper(helper, helpers[helper]);
    }
  }
};
```

#### Handlebars Markdown Helpers

Very simple Markdown for partial. **do not forget** to add your .md files to the partial list.

```js
options: {
  partials: 'src/partials/*.md',
  ...
}
```

```js
/**
 * Handlebars Markdown Helpers
 * Copyright (c) 2014 Thierry Charbonnel
 * Licensed under the MIT License (MIT).
 */
'use strict';

var marked = require('marked');

// Export helpers
module.exports.register = function (Handlebars, options) {
  options = options || {};
  options.marked = options.marked || {
    renderer: new marked.Renderer(),
    gfm: true,
    tables: true,
    breaks: false,
    pedantic: false,
    sanitize: true,
    smartLists: true,
    smartypants: false
  };
  
  Handlebars.registerHelper('md', function(name, context){
    var result;
    marked.setOptions(options.marked);
    // Convert inline markdown by prepending the name string with `:`
    if(name.match(/^:/)) {
      result = marked(name.replace(/^:/, ''));
    } else {
      try {
        result = marked(Handlebars.partials[name]);
      } catch(err) {
        result = '<!-- error -->'; 
      }
    }
    return new Handlebars.SafeString(result); 
  });  
};

```

## Other interresting projects 

* To precompile Handlebars templates to JST file use [grunt-contrib-handlebars](https://github.com/gruntjs/grunt-contrib-handlebars)
* Static site generator for Grunt.js, Yeoman and Node.js. [Assemble](http://assemble.io)
* This task renders Dust templates against a context to produce HTML. [grunt-dust-html](https://github.com/ehynds/grunt-dust-html) 
* Handlebars helpers which implement Jade-like layout blocks for Node project [handlebars-layouts](https://github.com/shannonmoeller/handlebars-layouts)
* Library of 100+ handlebars helpers. [handlebars-helpers](https://github.com/assemble/handlebars-helpers)

## Release History and Roadmap

 * 2014-08-17   v0.1.0   First Release.
 * 2014-08-14   v0.0.*   Alpha ans Beta Releases

## License

Copyright (c) 2014 Thierry Charbonnel, contributors.  
Released under the MIT license
