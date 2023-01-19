# Launch Request

The lunch request ask the adapter to launch the debuggee. Currently, it has two field.
Those field do not contain enfoe information to indicate what executable should be lunch.
Instead they must be supplemented by additional field in a implementation define manner.

In the word of the spec.

"the arguments for \[the launch request] are not part of this specification."

# Argument

| field       | type   | optional | effectively optional |
| ----------- | ------ | -------- | -------------------- |
| noDebug     | string | yes      | yes                  |
| \_\_restart | any    | yes      | yes                  |

The noDebug field is a hint that the adapter should lunch the executable without debugging it.

The \_\_restart field allow a adapter to cycle data through the client across restart.
It value derive from the Terminated Event.

# Response

The response do not contained any data. A successful response is a hint that the launch has is complete and did not produce a error.
A launch response does not constitute a hint that the adapter is reedy to accept configuration request (setBreakpoint Request, etc.)
