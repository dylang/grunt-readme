## Basic readme example

```js
# my-project

> [%= description %]

## Overview
[%= _.include("docs-overview.md") %]

## Options
[%= _.include("docs-options.md") %]

## Examples
[%= _.include("docs-examples.md") %]

## License and Copyright
Copyright (c) 2012-[%= grunt.template.today('yyyy') %] [[%= author.name %]]([%= author.url %])
[%= _.license() %]
```


## All options

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

