# Breakpoint state

All request that add breakpoint return a generic breakpoint that does't indinquate it origin.

The client and the adaptor are nitherless supose to keep trak of the breakpoint:

- SetBreakpoint request delete all previous SourceBreakpoint (in a spesifique Source)
- SetFunctionBreakpoint request delete all previous FunctionBreakpoint
- SetDataBreakpoint request delete all previous DataBreakpoint
- etc.
