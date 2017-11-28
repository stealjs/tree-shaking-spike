# Core concepts

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

When invoked as `determineUsedExportsForESModule(node)` run the following steps:

1. Set `metadata` to equal `node.load.metadata`.
1. Set `unusedIdentifiers` to equal a new *Set*.
1. For each import, add to the `unusedIdentifiers` Set.
1. Set `exportNames` to equal a new *Set*.
1. Walk the AST, adding exports to `exportNames`.
1. Walk the AST. For each identifier run these steps:
  1. If the identifier is included in `exportNames` check if:
    1. If `metadata.usedExports.has(identifier)` returns false.




1. Set `metadata` to equal `node.load.metadata`.
1. Set `usedImports` to equal a new *Map*.
1. For each import run the following steps:
  1. Set `moduleName` to equal the dependency's module name.
  1. Set `imports` to equal a new *Set*.
  1. Call `usedImports.set(moduleName, imports)`.
  1. For each identifier in the import declaration run:
    1. `imports.add(identifier)`.




1. Set `metadata` to equal `node.load.metadata`.
1. If `export *` export exists, set `metadata.allExportsUsed` to equal `true`.
  1. return undefined.
1. Set `usedExports` to equal `node.metadata.usedExports`.
1. Set `moduleName` to equal `node.load.name`.
1. For each named export run the following substeps:
  1. Set `exportName` to equal the identifier.
  1. If `usedByOthers(moduleName, exportName)` returns `true`, push `exportName` into `usedExports`.



1. If `export default` export exists, push `"default"` into `usedExports`.
