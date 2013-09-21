# grunt-readme [![NPM version](https://badge.fury.io/js/grunt-readme.png)](http://badge.fury.io/js/grunt-readme) 

> Generate your README from a template. If you already use Grunt, this is a no brainer.

## Heads up!

Please take note! If you add code examples to your templates that have a syntax similar to the Lo-Dash templates used in this project (`{% ... %}`), then it is recommended that you _change the curly braces on the templates to square brackets_ to prevent Lo-Dash from trying to evaluate templates that shouldn't be evaluated. Not to worry though, this plugin will replace your square brackets with curly braces in the rendered output.

So convert anything that has a pattern like this: `{%= ... %}`, `{%- ... %}`, and `{% ... %}`. If anyone has problems, please file an issue and it will be resolved.


## Getting Started
_If you haven't used [grunt][] before, be sure to check out the [Getting Started][] guide._

From the same directory as your project's [Gruntfile][Getting Started] and [package.json][], install this plugin with the following command:

```bash
npm install grunt-readme --save-dev
```

Once that's done, add this line to your project's Gruntfile:

```js
grunt.loadNpmTasks('grunt-readme');
```

If the plugin has been installed correctly, running `grunt --help` at the command line should list the newly-installed plugin's task or tasks. In addition, the plugin should be listed in package.json as a `devDependency`, which ensures that it will be installed whenever the `npm install` command is run.

[grunt]: http://gruntjs.com/
[Getting Started]: https://github.com/gruntjs/grunt/blob/devel/docs/getting_started.md
[package.json]: https://npmjs.org/doc/json.html


In your project's Gruntfile, load the plugin with `grunt.loadNpmTasks('grunt-readme');` outside of `grunt.initConfig()`:

```js
grunt.initConfig({
  grunt.initConfig({
    // tasks
  });

  grunt.loadNpmTasks('grunt-readme');
  // Build the README.
  grunt.registerTask('default', ['readme']);
});
```

Optionally, if you need to change the plugin's defaults, In your project's Gruntfile, add a section named `readme` to the data object passed into `grunt.initConfig()`:

```js
grunt.initConfig({

  // The "readme" task
  readme: {
    options: {
      sep: '\n',
      resolve: 'readme-template',
      prefixes: ["helper", "util", "assemble", "mixin"],
      metadata: 'test/fixtures/metadata.json',
      templates: ['tasks/templates/']
    }
  }
});
```



## Options
This task does not require any configuration in the Gruntfile, so all of the following options are... ahem, optional.

### readme
Type: `String`
Default: `./node_modules/grunt-readme/tasks/templates/README.tmpl.md`

By default, if no options are specified the task will look for a `README.md.tmpl` template to use, if none is found the task will use the "starter" file supplied by `grunt-readme` (more detail below). Example:

```js
readme: {
  options: {
    readme: 'path/to/custom/README.md.tmpl'
  }
}
```

1. If the `readme` options is defined, the task will use that custom template.
1. If (1) is undefined, the task uses the directory defined by `options: { docs: ''}`
1. If (2) is undefined, the task checks if `README.tmpl.md` exists in the `./docs` directory (without having to define it in the options)
1. if (3) is undefined, `options: { resolve: { readme: ''}}` attempts to automagically use a `README.tmpl.md` template from `node_modules`. The module must must be defined in `devDependencies`. Note that for a README template to resolve properly from `node_modules`, the `main` property in the `package.json` of the module being referenced must specify the path to the template. This option is probably most useful when you plan to use the same README template on a number of projects.
1. If (4) is undefined, the task uses the "starter" README template from `grunt-readme`.


### metadata
Type: `String|Object`
Default: `package.json`

Optional source of metadata to _extend the data object_ that is passed as context into the templates. Context of the data object is the value of `this`, and properties in `package.json` will be ignored when matching properties are defined on the `metadata` object. Example:


```js
readme: {
  options: {
    metadata: 'docs/metadata.json'
  }
}
```
or

```js
readme: {
  options: {
    metadata: {
      name: 'Foo',
      description: 'This is foo.'
    }
  }
}
```

Since context is the value of "this", the `metadata` path is not required in templates, only property names:

* `{%= name %}` (e.g. not `{%= metadata.name %}`) => `Foo`
* `{%= description %}` => `This is foo.`


### docs
Type: `String`
Default: `./docs/`

Override the default directory where your local docs ("includes") will be stored. This defaults to the `./docs` directory in the root of your project.  with the 'docs' option.


### templates
Type: `String`
Default: `./node_modules/grunt-readme/tasks/templates/` (relative to your project)

The `cwd` for "includes" defined using the `{%= _.include() %}` template. By default, the `include` mixin will look for files in the `./tasks/templates` directory of this project (grunt-readme), where some starter templates are stored. (also see [EXAMPLES.md](./EXAMPLES.md))

You may overide this by specifying a path in the `templates` option:

```js
readme: {
  options: {
    templates: 'docs/templates'
  }
}
```


### resolve

All of the `resolve` options enable including content _from named NPM modules listed in `devDependencies`_.

#### resolve.readme
Type: `String`
Default: `undefined`

Name of the npm module containing the `README.tmpl.md` file to use for the README template. The module must be listed in the `devDependencies` of your project, and the template must be defined in the `main` property of the named module.

```js
options: {
  resolve: {
    readme: 'my-npm-module'
  }
}
```
If defined properly in the `main` property of the `package.json` of `my-npm-module`, this would resolve to:  `./node_modules/my-npm-module/README.tmpl.md`.


#### resolve.docs
Type: `String`
Default: `undefined`

If defined, `resolve.docs` becomes the `cwd` for files to be included using the `{%= _.doc() %}` mixin.

```js
options: {
  resolve: {
    docs: 'my-npm-module'
  }
}
```

This would resolve to:  `./node_modules/my-npm-module/`.


#### resolve.metadata
Type: `String`
Default: `undefined`

If defined, `resolve.metadata` will resolve to a specific file to


### prefixes
Type: `Array`
Default: `grunt|helper|mixin`

Any prefixes defined will be removed from content passed in using the `{%= _.shortname() %}` template.

Example:

```json
{
  "name": "helper-prettify"
}
```

Used in a template like this:

```js
## {%= _.titleize(_.shortname(name)) %}
```

Renders to:

```
## Prettify
```

### contributing
Type: `Boolean`
Default: `True`

By default, the README task copies a basic `CONTRIBUTING.md` file to the root of your project. If one exists, the task will skip this. If you wish to prevent the task from adding this file to your project, set the `contributing` option to `false`.

### sep
Type: `String`
Default: `\n`

Separator to use between sections of content that is included using the `include` or `doc` mixins (more about these in the "Mixins" section below). This option is more useful when you use minimatch patterns to specify the files to include.

The `sep` option can either be defined in the task options:

```js
readme: {
  options: {
    sep: '\n***\n'
  }
}
```

or as a second parameter in the `include` or `doc` mixins.

* `{%= _.include("docs-*.md", "***") %}` (more below...)
* `{%= _.doc("*.md", "\n***\n") %}` (more below...)




## mixins
> Three different mixins are built into the task for including "external" content: `include`, `doc` and `resolve`. Each is used for a different purpose.

Here is a summary of what they do (settings for the `include` and `doc` mixins can be customized in the task options):

* `{%= _.include("file.md") %}`: include a file (or files using [minimatch][minimatch] patterns) from the `./templates/` directory of _the grunt-readme task_.
* `{%= _.doc("file.md") %}`:  include a file (or files using [minimatch][minimatch] patterns) from the `./docs/` directory of _your project_.
* `{%= _.resolve("file.md") %}`: include a **specific file** from *node_modules*`.


### _.include()
Use the `include` mixin in templates to pull in content from other files:

```js
{%= _.include("examples.md") %}
```

[Minimatch][minimatch] patterns may also be used:

```js
{%= _.include("docs-*.md") %}
```

Unless overridden in the `templates` option, the `include` mixin will use the `./node_modules/grunt-readme/tasks/templates/` directory (from the root of your project) as the `cwd` for templates.


### _.doc()
Same as the `include` mixin but is hard-coded to use the `docs/` folder of your project as the `cwd` for templates.


### _.resolve()
Use the `resolve` mixin in templates to include content _from named NPM modules listed in `devDependencies`_:

```js
{%= _.resolve("my-npm-module") %}
```

where `my-npm-module` is the name of a `devDependency` currently installed in `node_modules`. For the `resolve` mixin to work, the referenced file must be listed in the `devDependencies` of your project's `package.json`, it must be installed in `node_modules`, and the referenced project must have the file defined in the `main` property of that project's `package.json`. Last, in your templates make sure you _use the name of the module, not the name of the file to "include"_.


#### _.resolve() example
Here is a `package.json` for a bogus project we created, `my-npm-module`, to store the template we want to use as an include:

```js
{
  "name": "my-npm-module",
  "main": "README.tmpl.md"
}
```

### _.contributors()



## Usage Examples
### Basic readme example

```js
## my-project

> {%= description %}

### Overview
{%= _.include("docs-overview.md") %}

### Options
{%= _.include("docs-options.md") %}

### Examples
{%= _.include("docs-examples.md") %}

### License and Copyright
Copyright (c) 2012-{%= grunt.template.today('yyyy') %} [{%= author.name %}]({%= author.url %})
{%= _.license() %}
```


### All options

```js
readme: {
  options: {
    templates: '',
    metadata: '',
    resolve: {
      cwd: '',
      readme: '',
      docs: '',
      templates: '',
      metadata: ''
    },
    sep: '\n',
    prefixes: [],
    contributing: true
  }
}
```





## Release History

 * 2013-09-17   **v0.1.0**   First commmit.
 

## Author

+ [github/Jon Schlinkert](https://github.com/jonschlinkert)
+ [twitter/Jon Schlinkert](http://twitter.com/Jon Schlinkert)

## License
Copyright (c) 2013 Jon Schlinkert
Released under the MIT license


***

_This file was generated on Sat Sep 21 2013 11:00:41._

[minimatch]: https://github.com/isaacs/minimatch