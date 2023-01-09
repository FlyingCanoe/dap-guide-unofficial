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

As the Message or unchanging and do not need to be registered, I do not consider the Message to be part of The protocol state.

### Module

There tow dap msg whitch can modify the list of Module.

- Module Event
- Module Request

The Module Event allow the adapter to add or remove a specific Module

The Module Request is gated behind the supportsModulesRequest capability

The Module Request replace the list of Module by the list it return.

### Thread

There three dap msg whitch can modify the list of Thread.

- Thread Event
- Thread Request
- TerminateThread Request

The Thread Request allow the client to ask for a updated Thread list.
The Thread list is replace by the list included in the response.

The Thread Event allow the adapter to remove a Thread from the list of Thread, however it does not allow the adaptor to
add Thread to the list.

The Thread Event only contain a reference to a Thread.

If the Thread Event has the raison 'exited', the Thread is remove from the list.

If the Thread Event has the raison 'started', the adaptor merely inform the client that it could update the list of thread with a thread request
Despit a 'started' Thread Event only being a hint, the threadId is require to not be the id of any Thread in the list of Thread.

It could be argue that the id in the Thread Event need not be asigne to a Thread in subsequent Thread Request, because Thread event are optional
the Thread could have exited and no 'exited' Thread event emitted.

Furthermore the overview state

> Whenever the generic debugger receives a stopped or a thread event, the development tool requests all threads that exist at that point in time.

Which could be taken as meaning that the threadId of a 'started' Thread Event is ignore.

The TerminateThreads Request is gated behind the supportsTerminateThreadsRequest capability.

The effect a TerminateThreads Request on the the thread list is ambiguous.
The response is just a acknowledgment, the spec does't not say if the response should only be send after the Thread where successfully terminated,
or if update to the Thread list (if any) should come in the form of a 'stopped' thread event.

I assume that the list is updated when the the response is send.

### Source

In the protocol Source can come from the client or from the adaptor. All the field are optional with the exception of Name.
The Name field is mandatory only when it come from the adaptor.

There are 2 field which cause a whole lot of ambiguity.

- sourceReference
- adapterData

let start with sourceReference and adapterData.

the doc on the adapterData state that

> The client should leave the data intact and persist it across sessions.

So the client need a way of figuring out if a source is the same source. The sourceReference look like a premising place to start,
but the doc explicitly forbid it.

> Since a `sourceReference` is only valid for a session, it can not be used to persist a source.

the spec does no say what should be use to persist a source across a session

I do not know how client typically handle this persistance.

To avoid dealing with that mess adaptor should not send adaptorData.

#### Type of Source

I condider that they are three type of Source

- Path Source
- Reference Source
- Unavailable Source.

Path Source are for the case where the Source are just file that are on the local disk.
They should have a path, but no sourceReference.
If the client send a Source request with a Path Source as the parameter, the adapter should just return the content of the local file.

If a adapter wish to provide the content of a Source, it should send a Reference Source with a non zero sourceReference. When there a Source Request for
a Reference Source the adaptor should provide it content.

Unavailable Source are for the time where the content of a Source is not available. Unavailable should have nittier a Path nor a sourceReference.
A Unavailable Source should have it presentationHint presentationHint set to 'deemphasize'

The adapter should keep track of all Reference Source it has send.
