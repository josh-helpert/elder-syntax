---
layout: default
title: Composition
permalink: composition
---

## Scratch
* goals
  * exhaustive
    * every valid and invalid must be modeled and handeled
  * info/data is not lost
    * often information is lost when
      * abstracted as details are hidden
      * from far away in the call stack (whether a direct or indirect invocation)
      * when intermediate steps compose in indeterminate ways and don't propogate the info
         * sometimes this is desirable, sometimes not
            * intermediate steps can propose a solution to a specific issue raised but the context can override it (or intercept or mixin w/ it)?
              * that way outer calls aren't obligated to use inner calls solution/handling but they can interplay or override it?
    * if data needs to be retreived is not lost even from far away (in call stack)
    * intermediate calls should not take an action that conflicts w/ their outer context requirements as the outer has more info and details
      * actually info is passed up and down the stack so need ways to add details from each part
        * like Pug-style pre/post or override the handler and can either pre/post or override previously defined handlers (or other means as well)?
    * outer contexts can always request actual errors if they want to handle manually
      * this way can pivot but also have benefit of inner handlers providing default handlers
  * issues are both tracked at their source AND can be aggregated
    * just b/c of fn can call multiple functions which each fail in their own way and the composition can also fail; it doesn't mean the info from the called functions aren't also able to be handled separately
      * handlers can target in many ways like any 'integer overflow' but also target specific locations (even deep within the stack) or specific tags
  * outer contexts can always know what happens within
    * the contexts provide the solutions while the inner calls define the errors
* ways to signal
  * define models which take into account possible errors
    * eg Optional, Either, ...
  * manually define, raise/signal issues
* ways to handle
  * model
    * eg computation over Ints can result in BoundedInt, OverflowInt, ...
    * eg data presence (or Error) can result in Optional, Either, ...
  * Result a la Rust
    ```
    Result<T, U>
      Ok<T>
      Err<U>
    ```
  * default
  * abort/exit
  * transaction
    * safe/commit points
  * LISP-like conditions
    * signaling or raising conditions
    * handling conditions
    * recovery from conditions
  * assert/assure they won't happen
  * filter (or otherwise remove/correct) the input which can cause issues
  * prove correctness (or errors are impossible a lesser requirement)
    * or at least areas of knowably valid, knowably invalid, and unknown
  * run and test it
    * then can memoize, store at comptime, ...
  * reset/rewind
  * 
* is none/partial/full state, steps, data, etc. allowable
  * if
    * none or full then
      * transactions
      * retry
      * recover
        * eg adjust input or details of error (eg state)
    * partial
      * filter/categories success and failures
      * tagged union or Either
      * optional
      * 
  * eg can be in an intermediate step or must fully either commit or reset
* 
