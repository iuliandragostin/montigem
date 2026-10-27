# MontiGem

MontiGem generates information systems from UML/P models. A client stays in sync with a shared
backend by exchanging Commands. This glossary currently covers the shared terms plus the Python client.

## Language

**Domain**:
One class diagram together with everything generated from it. A Domain has a single id space for
its Domain Objects.
_Avoid_: package, model package

**Domain Object**:
A client-side copy of one instance of a class from the Domain, identified by its GemId.
_Avoid_: gem, element, entity, instance

**GemId**:
The identity of a Domain Object. The backend assigns it, and it is unique across the whole Domain.
_Avoid_: id, index

**Absent GemId**:
The GemId value `-1`. It means an optional association has no target.
_Avoid_: null id, empty

**Command**:
A typed message about a Domain, named `<domain>.commands.<Class>_<operation>`. Commands are
exchanged between a Participant and the backend.
_Avoid_: message, request, event

**Reply**:
The backend's answer to a Command that a Participant sent. It is matched to that Command by
commandId.
_Avoid_: response, result

**Remote Change**:
A Command the backend pushes to a Participant. It describes a change that another Participant made.
_Avoid_: update, notification, incoming message

**Participant**:
One client connected to the backend, identified by its participantId.
_Avoid_: client id, session, user

### Attributes and multiplicities

**Association End**:
An attribute whose value refers to Domain Objects. It comes either from an association or from an
attribute typed by a class of the Domain.
_Avoid_: gem attribute, link attribute

**Value Attribute**:
An attribute that holds a built-in value or an enum literal.
_Avoid_: built-in attribute, primitive attribute

**Mandatory**:
Multiplicity `[1]`: exactly one target.
_Avoid_: required, single

**Optional**:
Multiplicity `[0..1]`: one target, or the Absent GemId.
_Avoid_: nullable, maybe

**Many**:
Multiplicity `[*]`: any number of targets. Ordered and unordered are not distinguished.
_Avoid_: set, list, mult, multiple

## Python Client

**Reference**:
A lazy pointer to a Domain Object via its GemId. Awaiting it loads the Domain Object.
_Avoid_: gem, Gem object, link

**Reference List**:
An ordered list of References. It is the value of an association end with multiplicity `*`.
_Avoid_: gem list, list of gems

**Position**:
A 0-based place in a Reference List.
_Avoid_: index

**Loaded Objects**:
The Domain Objects this client has already fetched and keeps in sync. They are a subset of the
backend's objects, not the whole Domain. Remote Changes only apply to Loaded Objects.
_Avoid_: global list, global state, cache of all objects

**Domain Manager**:
The entry point for one Domain. It fetches and creates Domain Objects and owns the Loaded Objects.
_Avoid_: manager, api

**Command Manager**:
This Participant's connection to the backend. It sends Commands, matches Replies and queues Remote
Changes.
_Avoid_: manager, connection, client

**Command Handler**:
The part of a Domain that applies Remote Changes to Loaded Objects.
_Avoid_: consumer, command consumer

**Observer**:
A hook that user code attaches to a Domain Object to be told about changes to it.
_Avoid_: listener, callback

**Command Observer**:
The built-in observer that sends this client's own changes to the backend as Commands.
_Avoid_: sync observer, sender

**Opposite-End Sync**:
The built-in observer that keeps both ends of a bidirectional association consistent.
_Avoid_: bi-observer, back-reference observer

**Notification Scope**:
How far a change to a Domain Object spreads. The levels, from narrowest to broadest, are MINIMAL
(the object itself), LOCAL (plus this client's observers), GLOBAL (plus the backend) and ALL.
A Remote Change applies with LOCAL scope, so it is never sent back to the backend. In practice the
scope identifies where a change came from: ALL for this client's own changes, LOCAL for Remote
Changes, MINIMAL for Opposite-End Sync fix-ups. GLOBAL is only used as a threshold.
_Avoid_: notification level, propagation mode

**Handwritten Extension**:
Python code written in the model next to a class and copied into that class's generated code.
_Avoid_: HW, hook

**Custom Command**:
A Command that corresponds to a method declared in the Domain, rather than to creating, reading or
changing an attribute.
_Avoid_: method-command, method call
