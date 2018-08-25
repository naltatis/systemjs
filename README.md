SystemJS 2.0
============

[![Build Status][travis-image]][travis-url]
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/systemjs/systemjs?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Sponsor](https://cdn.canopytax.com/images/canopy-sponsorship.svg)](https://canopytax.github.io/post/systemjs-sponsorship/?utm_source=systemjs)

Configurable module loader enabling backwards compatibility workflows for ES modules in browsers.

[Read the SystemJS 2.0 announcement post](TODO)

_[For the previous release see the SystemJS 0.21.x branch](https://github.com/systemjs/systemjs/tree/0.21)_

_SystemJS is [currently sponsored by Canopy Tax](https://canopytax.github.io/post/systemjs-sponsorship/?utm_source=systemjs)._

SystemJS 2.0 provides two hookable base builds:

1. The 1.5KB [s.js](dist/s.min.js) minimal loader:

  * Loads [System.register](docs/system-register.md) modules [supporting all ES module semantics](docs/system-register.md#semantics)
  * Loads and resolves URLs only, excluding support for [bare specifier names](docs/package-name-maps.md#bare-specifiers) (eg `/lodash.js` but not `lodash`)
  * Hookable loader supporting [custom extensions](docs/hooks.md)
  * Works in IE11+ when Promises are polyfilled
  * Ideal for use in [Rollup code-splitting builds](TODO)

2. The 2.5KB [system.js](dist/system.min.js) loader:

  * [Tracing hooks](docs/hooks.md#trace-hooks) and [registry deletion API](docs/api.md#registry) for reloading workflows
  * Supports loading global scripts as modules, as well as [Sytem.register](docs/system-regsiter.md) modules
  * Supports [package name maps](docs/package-name-maps.md) for resolving [bare specifier names](docs/package-name-maps.md#bare-specifiers), loaded via `<script type="systemjs-packagenamemap">`
  * Supports loading WASM based on the `.wasm` file extension
  * Supports running in Web Workers

In addition, the following [pluggable extras](dist/extras) are provided:

* AMD loading support (through `Window.define`)
* Named exports convenience extension support for global and AMD module formats (`import { x } from './global.js'` instead of `import G from './global.js'; G.x`)
* Translate loader support, using fetch and eval, supporting a hookable `loader.translate`

Since all loader features are hookable, custom extensions can be easily made following the same approach as the bundled extras. See the [hooks documentation](docs/hooks.md) for more information.

For discussion, join the [Gitter Room](https://gitter.im/systemjs/systemjs).

Documentation
---

* [Getting Started](docs/getting-started.md)
* [Workflows](docs/workflows.md)
* [Package Name Maps](docs/package-name-maps.md)
* [API](docs/api.md)
* [System.register](docs/system-register.md)
* [Loader Hooks](docs/hooks.md)

Example Usage
---

### Loading a System.register module

```html
<script src="system.js"></script>
<script>
  System.import('/js/main.js');
</script>
```

where `main.js` is a module available in the System.register module format.

### Package Name Maps

Say `main.js` depends on loading `'lodash'`, then we can define a package name map:

```html

<script type="systemjs-packagemap">
{
  "packages": {
    "lodash": "/path/to/lodash.js"
  }
}
</script>
<!-- Alternatively:
<script type="systemjs-packagemap" src="path/to/map.json">
-->
<!-- SystemJS must be loaded after the package map -->
<script src="system.js"></script>
<script>
  System.import('/js/main.js');
</script>
```

### Browser transpilation

Browser transpilation workflows are no longer first-class in SystemJS, and require a little more customization to work:

```html
<script src="system.js"></script>
<script src="extras/translate.js"></script>
<script>
  // create a custom translator
  System.translate = function (url, source) {
    return Promise.resolve(someCustomTransform(source));
  };
</script>
<script>
  // main and all its dependencies will now run through translate before loading
  System.import('/js/main.js');
</script>
```

An extension for in-browser Babel compilation can be created easily this way, but SystemJS does not provide the
individual translator implementations, as this is left for third-party implementors.

### Third-party extensions

This list can be extended to include third-party loader extensions. Feel free to [post a PR to share your work](TODO).

* Nothing yet :)

### Contributing to SystemJS

Project bug fixes and changes are welcome for discussion, provided the project footprint remains minimal.

To run the tests:

```
npm run build && npm run test
```

License
---

MIT

[travis-url]: https://travis-ci.org/systemjs/systemjs
[travis-image]: https://travis-ci.org/systemjs/systemjs.svg?branch=master
