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

| Object          | field name         | field type        | lifetime               |
| --------------- | ------------------ | ----------------- | ---------------------- |
| Message         | id                 | integer           | forever                |
| Module          | id                 | integer or string | util removed           |
| Thread          | id                 | integer           | util thread exit       |
| Source          | sourceReference    | integer           | debug session          |
| StackFrame      | id                 | integer           | util continue          |
| Variable        | variablesReference | integer           | util continue          |
| Breakpoint      | id                 | integer           | while breakpoint exist |
| StepInTarget    | id                 | integer           | util continue          |
| GotoTarget      | id                 | integer           | unclear/debug session  |
| memoryReference | memoryReference    | string            | unclear/debug session  |

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
long as the error code are unique within a specifique release of the adaptor.

### Module

The id of a Module is a bit wirde. the only thing the spec say about the id is that it must be "unique".

The only place where the Module is unambiguously use is in the Module Event, where it can be use by the adaptor to update or remove a Module.

So the Module is valid from the moment it is added to the (implicit) list of module.
There are tow way a module can be added to loaded module.

- a Module Event which as the raison set to 'new'
- be included in response to a module request

A module continue to be valid util the moment a Module Event with the raison set to 'removed' and with the module id.

### Thread

The list of Thread
