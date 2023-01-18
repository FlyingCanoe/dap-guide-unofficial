# Continue Request

The Continue Request ask the client to unpause all thread. With additional capability,
the request can ask to unpause a thread instead of all thread.

If no additional capability is enable, the request still has two argument which are meaningless

# Argument

| field        | type      | optional | effectively optional |
| ------------ | --------- | -------- | -------------------- |
| threadId     | reference | no       | yes                  |
| singleThread | bool      | yes      | yes                  |

> Those two argument have no meaning when no capability are enable. Adapter should ignore them.

# Response

| field               | type | optional | effectively optional |
| ------------------- | ---- | -------- | -------------------- |
| allThreadsContinued | bool | yes      | no                   |

the allThreadsContinued indicate if, well, all thread continued.
if no capability is enable, the allThreadsContinued must be true.
If the field is absent, it default to true.

> The allThreadsContinued field should be absent.
