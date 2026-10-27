# Association Ends are awaited, lazy References

In the Python client, every Association End holds a Reference (or a Reference List), not the target
Domain Object. Reading the target means awaiting it (`await person.sibling`), and chains such as
`await person.sibling.sibling.name` are resolved lazily, one fetch per step, when the chain is
awaited. The alternative would be to load the reachable object graph up front and hand out plain
attributes. This was decided upstream (`986c24187`, "Automatic getters & async chains"), and the
rationale is not recorded.

## Consequences

- The client's whole public API is `async`. Calling code needs an event loop and has to `await`
  every Association End.
- Only the objects that are actually reached get fetched and become Loaded Objects (see ADR-0001).
