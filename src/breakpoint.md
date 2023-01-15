# Breakpoint

A source breakpoint is a breakpoint for a specify source at a specify position within that source.
Source breakpoint are the only type of breakpoint that do not need additional capability.

## SetBreakpoint Request

| SetBreakpoint argument | type               | optional |
| ---------------------- | ------------------ | -------- |
| source                 | Source             | no       |
| breakpoints            | SourceBreakpoint[] | yes      |
| lines                  | integer[]          | yes      |
| sourceModified         | bool               | yes      |

When the client send a SetBreakpoint Request, it ask the adapter to replace all previous source breakpoint in a specific list
by a new list of source breakpoint. Some of these breakpoint may already exist.

In a SetBreakpoint Request the list of source breakpoint can be specify as ether a
list of line number or as a list of SourceBreakpoint object.

A SourceBreakpoint object contained two field, a line number and (optionally) a column number.

> A list of line can trivially be converted to a list SourceBreakpoint object. A adapter should internally do this before precessing the request.

The sourceModified field may be use by a client to indicate that the content of a Source has change in a way that (potencely) require to refresh the breakpoint

> if a adapter do not support using line number other then those present at the lunch of the debug session,
> it should send a error response if the client set the sourceModified to true.

## SetBreakpoint Response

The response the the SetBreakpoint Request must include a list of Breakpoint object.
The list must be of the same length and in the same order as the list of source breakpoint.

A Breakpoint Object represent the result of attempt at setting the breakpoint.

| Breakpoint Object | type    | optional |
| ----------------- | ------- | -------- |
| id                | integer | yes      |
| verified          | bool    | no       |
| message           | string  | yes      |
| source            | Source  | yes      |
| line              | integer | yes      |
| column            | integer | yes      |
| endLine           | integer | yes      |
| endColumn         | integer | yes      |

The id field can be use by the adapter to change or remove a breakpoint through a Breakpoint Event.

> Adapter refrains from unilaterally changing or removing breakpoint through the Breakpoint Event.
> As such the id field is unnecessary and should not be use.

The verified field indicate if the adapter successfully set the breakpoint.

The message field can, at the client discretion, be show to the user. It is suggested that this field be use to provide a explanation for why the a breakpoint could not be verified.

This guide suggest that the message field should be use to indicate why a breakpoint could not be verified and should not be use when a breakpoint is verified.

The source, line, column, endLine and endColumn fields are use to specify the position of the breakpoint.
The adapter could use those field to indicate that the a breakpoint was set but not at the desired location.

This guide suggest that adapter should refrained from setting a breakpoint at a location other than the desired location.
if setting the breakpoint at the desired location is impossible, the adapter should refuse to set the breakpoint and set the verified field to false.
