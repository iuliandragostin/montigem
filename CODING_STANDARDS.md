# Coding Standards

Judgement calls a reviewer should enforce. Formatting (2-space indent, 100-char lines, no star
imports in Java) is handled by `.editorconfig` and is not repeated here.

## Java / MontiCore

- **Type checks on AST nodes use `instanceof`**, with pattern variables where a cast follows
  (`v instanceof ASTSetValueItem valueItem && valueItem.getExpression() instanceof ...`). Don't use
  the Mill `typeDispatcher()` / `*TypeDispatcher` `is…`/`as…` methods.
- **Unhandled cases in generators and decorators log, they don't throw or fall through silently.**
  Use `Log.warn(...)` (or `Log.error` when the output would be wrong) with the node's
  `get_SourcePositionStart()`, naming the decorator and the unhandled value.

## Python client generator (`experiments/python-client`)

- **Generated method bodies live in FreeMarker templates** under `resources/cd2python/`. Decorators
  build the signature with `CDMethodFacade` / `CDParameterFacade` and attach the body with
  `glex.replaceTemplate(Pyutils.EMPTY_TEMPLATE, method, new TemplateHookPoint(...))`. Don't build
  Python source as Java strings.
- **Templates keep hook points around the generated logic** (e.g. `Setter:Before` / `Setter:After`),
  so handwritten extensions can hook in without editing the template.
- **Shared runtime behaviour goes in the `montigem_runtime` package**, not into every generated
  class. A generated class should only contain what depends on the model.
- **Generated code refers to runtime types by fully qualified name**
  (`montigem_runtime.gembase.GEM_ID`, `typing.Any`), so it doesn't depend on import aliases.
