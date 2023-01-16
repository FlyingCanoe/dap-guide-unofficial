# StackFrame

| StackFrame | type    | optional |
| ---------- | ------- | -------- |
| id         | integer | no       |
| name       | string  | no       |
| source     | Source  | yes      |
| line       | bool    | no       |
| colum      | bool    | no       |
| endLine    | bool    | yes      |
| endColum   | bool    | yes      |

StackFrame represent a ongoing function call in a pause debuggee.

The id field must be unique on all thread.

The name is a freeform string and is typically the name of the function/method.

The source, line, column, endLine and endColumn fields are use the specify the source range of the StackFrame.
If the source is absent, the line and colum must be zero.

The client can retrieve the StackTrace for a thread using a StackTrace Request.
The StackTrace Request as a threadId as a parameter, has such a client must retrieve the list of thread before it can ask for stacktrace.
