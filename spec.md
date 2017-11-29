# Overview

> __Note__ that this is very incomplete and should not be taken as being exact steps to implement the tree-shaking algorithm.

This contains a partial specification for how to implement tree shaking in [steal-tools](https://github.com/stealjs/steal-tools). It is currently incomplete.

# Core concepts

The core algorithm involves

# The tree-shaking algorithm

This is the process of walking the dependency graph to remove exports that are not used. When invoked as `shake(graph)` it should:

1. If `isDependencyGraph(graph)` is false, throw a *TypeError*.
1. Starting with the *main modules* run the following substeps on __moduleName__, catching any errors:
  1. Set `node` to be `graph[moduleName]`.
  1. Run the __determine a module's used exports__ step, passing `node`.
1. TODO -> now we know all of the used exports, can remove stuff.

# Determine a module's used exports

When invoked as `determineUsedExports(node)` run the following steps:

1. If `isNode(node)` is false, throw a *TypeError*.
1. Set `metadata` to equal `node.load.metadata`.
1. Set `metadata.allExportsUsed` to equal `false`.
1. Set `metadata.usedExports` to equal an empty *Set*.
1. If `isMain(node)` is true, return `undefined`.
1. Set `format` to equal `metadata.format`. Run the following steps on `format`:
  1. If `format` equals `"cjs"` return undefined. // TODO
  1. If `format` equals `"es6"` run __determine an ES module's exports__, passing `node`.
  1. Otherwise, return `undefined`.

# Determine an ES module's used exports
