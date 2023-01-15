# Object reference lifecycle and Protocol State

In the [overview of the specification](https://microsoft.github.io/debug-adapter-protocol/overview#Lifetime%20of%20Objects%20References),
the subject of Object reference and the lifetime of said reference is evoke.

the overview mention that some object reference have there lifetime limited to "the current suspended state",
while other object "do not have limited lifetime".

the [specification part of the specification](https://microsoft.github.io/debug-adapter-protocol/specification)
[does](https://microsoft.github.io/debug-adapter-protocol/specification#Events_Output)
[on](https://microsoft.github.io/debug-adapter-protocol/specification#Requests_DataBreakpointInfo)
[multiple](https://microsoft.github.io/debug-adapter-protocol/specification#Requests_RestartFrame)
[occasion](https://microsoft.github.io/debug-adapter-protocol/specification#Requests_Scopes)
link to the overview for more detail

overall the specification (including the overview and the specification part of the specification) fail to define a list of object reference.
The lifetime of those reference is as best implicit and at worse undefine/implementation define.
It also fail at unambiguously defining what "the current suspended state" mean.

I have made a list of all that can be considered a object reference in the debug adaptor protocol.
For eche type of reference I have included the object it refere to, the name of the field which hold the reference in said object,
the type of the reference type, and my interpretation of the lifetime.

| Object     | field name         | field type        | lifetime               |
| ---------- | ------------------ | ----------------- | ---------------------- |
| Message    | id                 | integer           | forever                |
| Module     | id                 | integer or string | util removed           |
| Thread     | id                 | integer           | util thread exit       |
| Source     | sourceReference    | integer           | debug session          |
| StackFrame | id                 | integer           | util continue          |
| Variable   | variablesReference | integer           | util continue          |
| Breakpoint | id                 | integer           | while breakpoint exist |

The spec seam to encorenge thinking of eche reference as existing independently and being valid util the time where ther lifetime expire.

I think it is more helpful to conceptialise those object as existing in some protocol state. Some request can change that state.
A reference is a identifant which may or may not refer to part of that state and which may or may not be reutilise when the thing the identifent refered to
sese to be part of the protocl state

### Message

The Message object is use for error response, it id is untended to act as a error code.
As such it suppose to be 1) googlable 2) unchanging.

According to the spec, the id is suppose to be "unique (within a debug adapter implementation)"

So if a message id is included in a release of a adaptor, it should not be later reassigned to a other Message,
even if the original error message is not longer in the adaptor.

It seam like a stretch that reusing no longer use error code would make a adaptor none complement, as
long as the error code are unique within a specific release of the adaptor.

As the Message or unchanging and do not need to be registered, I do not consider the Message to be part of The protocol state.

### Module

In a baseline mode, the adapter can add, update and remove module from a list of module through the Module Event.

However it is not clear what a Module represent. The Module do have a list of field, but it does not seem like the client is allow to interpret them,
at least not on a adapter independent way.

For example a Module have tow field which represent path, however these are "logical path" the meaning of which is "implementation defined".

As such this guide does not recommend the utilization of Module.

### Thread

All adapter must declare Thread. Your language do not have a concept of Thread? Does not matter make up some thread.

The main way in which Thread are communicated is the Thread Request. Some thing as cause the client to want a updated list of thread,
it ask the adapter for it, the adapter send back the list.

While the client is free to ask anytime it want, there three main raison that cause client to ask.

- a Thread Event
- the debugee has stop
- the initialisation is in progress.

Thread Event are a way for a adapter to hint to the client that it should update it ui, and pontensely ask for a new list of thread.
They come in two form 'started' and 'exited'. I 'started' thread event should include the id of a new thread. 'started' thread event does not content enofe information for
the client to proprely update it ui, science it does not content the name of the new thread. The 'exited' thread event does contain enofe information for the client to update it ui.

Even in the case of a 'exited' thread event, it would be polite for the client to do a Thread Request, but it not a obligation.

When the debugee stop, the client must ask for a list of Thread science the curent thread id is use for further request about the stacktrace, the list of variable, etc.

A Thread Rquest is also part of a the initilisation sequence.

### Source

A source can be a reference to the literal path of a file on disk, or a opaque reference that can be use to request the content of the Source,
or a opaque reference to noting at all (for the case where the source is unavailable).

if a Source is a reference to a file on disk is should have path and should not have a sourceReference has having a non zero sourceReference mean
that a Source is of the opaque reference varaity.

a Source that is unavailable should not have a reference and must have presentation hint set to deemphasize.

#### Source adapterData

the Source type has a field named adapterData, that field cause a lot of ambiguity.

That field is suppose to allow the adapter to loop some data through the client so that it has more context when the client pass that Source has a parameter to a request
without forcing the adapter to explicitly keep treak of that context. The client need to have a way of determining if it the same source and therefore it must send the data back.
Should two Source that reference the same file, but have different origins (a free form string with no semantically meaningful predefine value) the same Source? How checksum fit into that (note checksum are optional)?
what about a Source that point to the same Path, but have different checksum algorithms, should they be threated like that do not have a checksum? like they have a checksum that does not match? is there a difference?

The spec does't say.

And to cause even more trouble, the spec state the client should persiste the data across session.

A adapter can asigne a arbitery unique number for a Source with the sourceReference field, however the spec explicitly forbid using the sourceReference to persist a Source.
so when it come to persiting a Source across session all bet are off.

To avoid the mess this guide does not use the adapterData field.

### StackFrame

StackFrame represent a ongoing function call in a pause debuggee. A StackTrace represent the ordered list of StackFrame of a thread (if functionA call functionB, the stacktrace should be returned as \[StackFrameA, StackFrameB])
both only last util the debuggee resume it execution (only one thread resuming is enofe to invalidate all StackTrace).

The client can retrieve the StackTrace for a thread using a StackTrace Request.
The StackTrace Request as a threadId as a parameter, has such a client must retrieve the list of thread before it can ask for stacktrace.

StackFrame have a id, that id must be unique on all thread. Once the debuggee resume it execution, the id of all StackFrame are invalidate and can be reuse.
The StackFrame id can be use to retrieve Scope, which can, in turn, be use to retrieve Variable.

### Scope

The scope serve as a way to organize Variable. The adapter can separate it Variable between local variable, global variable and whatnot.
A scope has a name which is a freeform string which value is up to the adapter. A scope also has a presentationHint that can be use to tell the client what the type of the Scope, in a non implementation define, machine readable way.

Currently the presentationHint can take the following value:

- arguments
- locals
- registers

The list of Scope of a specifics StackFrame

A scope also has a variablesReference, which can be use to retrieve the list of variable.
