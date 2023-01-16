# Scope

| SetBreakpoint argument | type      | optional |
| ---------------------- | --------- | -------- |
| name                   | string    | no       |
| presentationHint       | open enum | yes      |
| variablesReference     | integer   | no       |
| namedVariables         | integer   | yes      |
| indexedVariables       | integer   | yes      |
| expensive              | bool      | no       |
| source                 | Source    | yes      |
| line                   | integer   | yes      |
| column                 | integer   | yes      |
| endLine                | integer   | yes      |
| endColumn              | integer   | yes      |

The scope is manly a intermediary between a StackFrame and its Variable.
The Scope Object allow the adapter the classify different variable into different category (local, global, etc.).

The name field is a freeform string which represent the name of the category up to the adapter.
The presentationHint field is closely related to the name field, that can be use to tell the client what the type of the Scope,
in a non implementation define, machine readable way.

Currently the presentationHint can take the following value:

- arguments
- locals
- registers

A adapter may use a other as a presentationHint, however the meaning of such a extension is adapter specify

> Since using a value other then the predefine value has unspecified meaning, adapter should not use value other then the predefine value.

The variablesReference, namedVariables and indexedVariables fields are use to retrieve the variable in the scope.
The semantic of these field are base on the semantic of the field with the same name in a Variable.

The source, line, column, endLine and endColumn fields are use to indicate the range in which a Scope is valid.

> if a adapter wish to indicate the range in which variable are valid (the Variable Object does not contain a source range),
> it should specify it in a the StackFrame instead.
