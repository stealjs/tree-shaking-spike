# Competitive

* systemjs-builder uses rollup directly.
* webpack does its own tree-shaking (I believe, not 100% confirmed).
  * webpack uses the strategy of marking exports as being used, and during bundling it doesn't export the exports that are not used. This causes the minifier to DCE it.

# Problems

* Different module formats makes this more difficult.
  * If a CommonJS module ever imports a ES module, that entire ES module has to be marked as __used__.
* Recursive tree-shaking is import.
  * *one.js* imports `a` and `b` from *two.js*. It exports `c` and `d` which uses `a` and `b` respectively.
    * If `c` is tree-shaken out, then `a` is unused even though *one.js* imports it.
    * This means we *can't* assume that an export is used just because something exports it, anything can be tree-shaken out.
  * Pretty sure that webpack [doesn't handle](https://github.com/webpack/webpack/issues/2867) recursive tree-shaking. [Docs](https://webpack.js.org/guides/tree-shaking/#caveats).
