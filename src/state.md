# State

## adaptor state

here a incomplete list of state that a adaptor is (implicitly) suppose to keep track of

- source breakpoint list: Map\<Source, SourceBreakpoint>
- function breakpoint list: Vec\<FunctionBreakpoint>
- data breakpoint list: Vec\<DataBreakpoint>
- thread list: Vec\<(Thread, ThreadState)>
  - is_pause: bool
  - StackTrace: Vec<(StackFrame, Vec\<Scope>)>
    - variables list: Vec\<Variable>
