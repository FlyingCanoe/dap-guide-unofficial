# Number Type

When the spec want a number of any shape or form, it use the type "number" in the rendered version.

However the JSON schema is more precise and use the "integer" type.

After some research into how JSON schema, it does not seam like the "integer" is bounded (ie as a max in min value)
are that it event has a more precis meaning thant the "number" type

From what appears to be the [spec](https://json-schema.org/draft/2020-12/json-schema-core.html#name-instance-data-model)

> Note that JSON Schema vocabularies are free to define their own extended type system. This should not be
> confused with the core data model types defined here. As an example, "integer" is a reasonable type for a
> vocabulary to define as a value for a keyword, but the data model makes no distinction between integers and
> other numbers.

I think it is still safe to assume that the spec expect only whole numbers in those place

## exception

there are other place that the scheme utilize the "number" type.
the first is as part of the definition of any. Which it define as either

- array
- boolean
- integer
- null
- number
- object
- string

The second place in which the number type appear is more meaningful.

It also appears when the spec want a percentage.

In those case the spec expect a decimal number in the range [0, 100]

The spec currently as percentage in tow place:

- ProgressStartEvent
- ProgressUpdateEvent

The third place were number can appear is in a module custom attributes.

The spec allow you to register additional attributes for the module via the ColumnDescriptor.

when a adaptor register a custom attribute it can chose between the following type

- string
- number
- boolean
- unixTimestampUTC

## bounded number

### upper bound

In certain case the spec explicitly put bound ont the value of integer.

The first kind a place where this append is whens the spec appear to want to make the integer a i32.
In those case the spec put a upper bound at (2^31)-1.

However, in those kind the spec does not seam to currently put a lower bound on the number.

Except for sourceReference (see the lower bound section for detail)

Here is a list of place the spec appear to want a i32:

- sourceReference
- processId (in RunInTerminalResponse)
- shellProcessId (in RunInTerminalResponse)
- namedVariables (in SetVariableResponse, SetExpressionResponse and EvaluateResponse)
- indexedVariables (in SetVariableResponse, SetExpressionResponse and EvaluateResponse)

### lower bound

In a number of place the spec state that if the integer is greater that zero, use it, otherwise ignore it.

Technically this does not put a lower bound on the integer (you are free to put the value to -1000 I will just ignore it).

but that would be so weird that I think it is find to say that this amounts to putting a lower bound at 0.

There are tow place where the spec impose this requirement.

- sourceReference
- variablesReference
