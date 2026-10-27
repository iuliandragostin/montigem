# The Python client is a partial replica of the Domain

The Python client only tracks its Loaded Objects: the Domain Objects it has fetched itself. It
ignores Remote Changes for any other object and logs a warning instead. The next fetch of such an
object reads its current state from the backend. This was decided upstream (`d9c5252a2`,
"pyclient: ignore unwanted updates"), and the full rationale is not recorded there.

## Consequences

- An Observer never hears about changes to objects this client hasn't loaded.
- A Remote Change that arrives while a fetch of the same object is in flight is lost. Whether the
  local copy ends up stale then depends on the order the backend processes things in. This is
  tracked as a known limitation.
