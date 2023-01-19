# Initialize

The Initialize Request is first request send as part of the initialization sequence.
The request argument contain the client capability and some context.

The client and adapter are forbidden from sending any msg utile the Initialize Request is complete.

# Argument

| field           | type      | optional | effectively optional |
| --------------- | --------- | -------- | -------------------- |
| clientID        | string    | yes      | yes                  |
| clientName      | string    | yes      | yes                  |
| adapterID       | string    | no       | no                   |
| locale          | string    | yes      | yes                  |
| linesStartAt1   | bool      | yes      | no                   |
| columnsStartAt1 | bool      | yes      | no                   |
| pathFormat      | open enum | yes      | no                   |

The clientID and clientName field are both freeform string identifying the client.
The clientName is supposes to be the "human-readable name of the client".
While the clientID is "the ID of the client". It is unclear what this mean in practice.

> The clientID may be use by the adapter for logging propose. The clientName should not be use by the adapter.

The adapterID field is suppose to be "the id of the debug adapter". So the client must tell the adapter what it name is?

> Adapter should ignore the adapterID field.

The locale field is suppose to be the ISO-639 locale. The spec give as example "en-US" and "de-CH".
However, as far as I am aware "en-US" and "de-CH" are not valid ISO-639 language code (ISO-639 does not try to define local but language code).
The part that is define by ISO-639 part 1 (there are 5) is "en" and "de". I do not think any part of ISO-639 define regional variant in the form "language-variant".
I could be wrong as I have not read the standard as it cost approximately 150 box per part.

> adapter should interpret the locale field as a BCP 47 language tag, because BCP 47 actually define tag such as "en-US" and because you can read it for free (thank IETF)

The linesStartAt1 and columnsStartAt1 allow the client to impose 0 or 1 based indexing on line and column.
If ether field is absent, it default to 1 based indexing.

The pathFormat field has currently two value with place for extension.

- path
- uri

How a local path should be formatted in the uri setting is unspecified.

> As the pathFormat is client decided, adapter should make best effort to support it. The adapter should handle local path the same way as browser.
> If a adapter receive a path format it does not understand, it should respond with a error after which it should crash and burn.

# Response

In response the adapter send it capability.
