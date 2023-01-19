# Attach Request

The Attach Request is akin to the Launch Request

It should contain implementation define field which contain the real information about what to attach to.

# Argument

| field       | type   | optional | effectively optional |
| ----------- | ------ | -------- | -------------------- |
| \_\_restart | string | yes      | yes                  |

The \_\_restart field allow a adapter to cycle data through the client across restart.
It value derive from the Terminated Event.
