# Tree shaking in steal-tools

This repository explores how to implement tree shaking in [steal-tools](https://github.com/stealjs/steal-tools).

## The Problem

Tree shaking is a special type of [dead code elimination](https://en.wikipedia.org/wiki/Dead_code_elimination) that optimizers can use. In tree shaking dead code is able to be detected in ES modules because of the static import/export declarations (although it is possible to tree-shake some CommonJS modules as well).

The `sample/` folder contains a project that could potentially be tree-shaken. A simple example is:

__utils.js__

```js
export function flatten() {
  // ...
};

export function find() {
  // ...
}
```

__main.js__

```js
import { flatten } from './utils.js';

flatten(array); // ...
```

In this example the `flatten` function from *utils.js* is used, but `find` is not. This means that the `find` function can be removed.

## Possible Solutions

There are two primary paths:

* __Use an existing library that does tree-shaking__: [Rollup](https://github.com/rollup/rollup) is the bundler that popularized tree shaking.
* __Implement tree shaking using AST transformations__: Do it ourselves using the ASTs that steal-tools already generates.

### Using an existing library

This path is appealing because the hard work is already done for us. It's worth nothing that webpack *does not* use rollup, but systemjs-builder does.

The problem with this approach is that rollup is not a low-level tree-shaking library. It's a high-level bundler. As far as I can tell you can't get the tree-shaking without the bundling.

To make this work in steal-tools we would have to do something a bit different:

1. Create the bundlers *before* transpiling.
1. Use rollup on each bundle to create tree-shaken bundles.
1. Transpile the bundle's source rather than the individual modules.

This *might* work but would definitely be a big change. I would likely consider this a breaking change.

### Implementing with AST transformations

This is what webpack does. The difficulty with this approach is that the algorithm is somewhat difficult to implement, as you have to do multiple "shakes" of the tree, meaning several passes.

The algorithm is:

1. Find unused `export` declarations.
2. Remove any code that was used by those declarations.
3. By removing that code, there are possibly new unused export declarations. Start back at __1__.

And then continue until there are no longer any unused declarations found.

The biggest question would be at what point in the process should __steal-tools__ do tree shaking, there are two choices:

1. Before transpilation. This would mean that we traverse each AST in the graph. This is problematic because:
  * We would be including a parser in steal-tools where there isn't one currently.
  * Some code might depend on Babel plugins and wouldn't parse with a normal parser.
1. After transpilation. This has the advantage of that we can treat ES modules, CommonJS, and AMD all the same.  If we assume Babel is the transpiler we can specifically target ES modules that have been transpiled by Babel by flagging this information in the load object or such.

## Conclusion

Tree-shaking is a good feature to have, especially to help with the marketability of steal-tools, but would likely take a couple of weeks or longer to implement properly.

The best approach is to implement it ourselves after the *transpile* phase by un-exporting unused exports and then doing the recursive algorithm as specified above.
