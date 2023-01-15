# Variable

| Variable Object    | type                     | optional |
| ------------------ | ------------------------ | -------- |
| name               | string                   | no       |
| value              | string                   | no       |
| presentationHint   | VariablePresentationHint | yes      |
| evaluateName       | string                   | yes      |
| variablesReference | integer                  | no       |
| namedVariables     | integer                  | yes      |
| indexedVariables   | integer                  | yes      |

a Variable object represent a node in a tree. if it field variableReference is less or equal to zero, it mean that it his a leaf node, otherwise it branch node.

the name, value and evaluateName field are freeform string.
If a Variable is a struct/object, it can be represented as a branch node where it children are it field.

The evaluateName field allow a adapter to specify a name with which a Variable should be refer to in Evaluate Request which is different then the Variable name.
The value of evaluateName may or may not be show to the user. Some (all?) expression passed to the Evaluate Request or user crafted.

> The use of a evaluateName may result in situation were the user is unaware of it existence,
> but must still use the evaluateName. As this situation is undesirable, a adapter should not use a evaluateName.

## Indexed and Named Variable

The Variable request can specify a filter where it ask for only indexed Variable or only for named Variable.
This future seam to be mean for time where paging is require has indicted by adapter sending hint of the number of child indexed and named variable
(via the namedVariables and indexedVariables field on Variable). In addition to filtering Variable by type, the Variables Request has facility to fetch Variable in chunk which size in chosen at the client direction.
This is accomplish via the start and count field of the Variable Request.

While the various paging and filtering future are mean to be use when the adapter hint the possibility, a client can chose to use them whenever it want.
As such, I suggests that adapter internally keep track of indexed and named Variable separately.

## Variable Presentation Hint

| VariablePresentationHint Object | type              | optional |
| ------------------------------- | ----------------- | -------- |
| kind                            | open enum         | yes      |
| attributes                      | open enum         | yes      |
| visibility                      | list of open enum | yes      |
| lazy                            | bool              | yes      |

A Variable can have a Presentation Hint there are 4 field to a presentation hint, kind, attributes, visibility and lazy.
All the field are optional.

The field kind, visibility, and attributes all have predefine value, but can also take a freeform String. If you supply a non-standard value it his probable that the client will ignore it.
visibility and kind take single value, attributes take a list of value.

the lazy field is use inform the client that it should ask for user confirmation before retrieving the child of a Variable.
The spec mention two case in which this behavior may be desirable :
if retrieving the value is costly or if retrieving the value has side effect.
