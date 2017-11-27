# Core concepts

# The tree-shaking algorithm

This is the process of walking the dependency graph to remove exports that are not used. When invoked as `shake(graph)` it should:

1. If `isDependencyGraph(graph)` is false, throw a *TypeError*.
1. Starting with the *main modules* run the following substeps on __moduleName__, catching any errors:
  1. Set `node` to be `graph[moduleName]`.
  1. Run the __determine a module's used exports__, passing `node`.
1. TODO -> now we know all of the used exports, can remove stuff.

# Determine a module's used exports

When invoked as `determineUsedExports(node)` run the following steps:

1. If `isNode(node)` is false, throw a *TypeError*.
1. TODO
