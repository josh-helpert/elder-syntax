---
layout: default
title: Partial Evaluation
permalink: partial-eval
---

Notes:
* Not really just partial evaluation but partial everything like declaration, definition, etc.
  * Need to partially define data, definitions, functions, steps in a block, etc. and can reuse it anywhere
    * Could also be how we DRY up some code?
* for functions
  * need to model
    * explicit comptime args often used to generate comptime code
      * like variants, generics, parametric polymorphism, polymorphism, ...
    * implicit args (eg generics) which are captured by the calling context
    * incomplete/holes which must be specified by caller (at least before called at runtime)
    * rules
      * really anything that can be added to specify input, output, precond, body, etc.
  * this may mean we need diff way to write signature as it will get rather long, nested, complex (like it's own computation)?
    * also doesn't indicate what's required, optional, etc. but that may be about compliancy, verification, etc.?
      * are there ever optional rules?
        * some output rules can be inferred?
* look at types of flexibility of Idris too and see if we can recreate
  * extend further w/ computational types, comptime, and metadata?
    * we don't have to prove but demonstrate/implement w/ computation any gaps or missing parts that would usually have to be done by the compiler/prover
* Whiley used 3-values logic: http://whiley.org/about/overview/
  ```
  Whiley addresses this by enforcing its prime directive: every condition must be convertable into a runtime check and, hence, must be computable.
  Thus, Whiley employs a form of three-valued logic when checking conditions: definitely correct, definitely incorrect and unknown.
  The first indicates there is definitely no error at that program point.
  The second indicates there definitely is an error at that program point.
  The third indicates Whiley could not definitely determine an answer.
  When this latter state arises, Whiley inserts a runtime check at that program point and produces a warning to indicate this.
  ```
* 

### Form Ideas
* Void
  ```
  RandInt = Fn () -> Int
  ```
* Simple
  ```
  SumInt = Fn (x:Int, y:Int) -> Int
  ```
* Ident (return exactly same value and make clear in constraints)
  ```
  ```
* Generic
  ```
  ```
* Explicit pass type (Zig-like)
  ```
  ```
* Capture (or implicit) type, constraint, etc. (like TypeClass?)
  ```
  ```
* Call determines (Crystal-like)
  ```
  ```
* Type is value (Idris-like)
  ```
  ```
* Type is literal values (TypeScript-like)
  ```
  ```
* Complex rules
  ```
  ```
* Computations types and rules
  ```
  ```

### Example: Integral Range

IntRange = #(T is Integral) -> Fn (min:T = Undefined, max:T = Undefined) -> Type
  = T
    = if min then :min = min
    = if max then :max = max

IntRange = #(T is Integral) -> Fn (min:T = Undefined, max:T = Undefined) -> Type
  = T
    :min = min or Undefined
    :max = max or Undefined

IntRange (#t is Integral) = Fn (min:#t = Null, max:#t = Null) -> Type
  = #t
    = if min then :min = min
    = if max then :max = max

IntRange = Fn (#t is Integral, min:#t = Null, max:#t = Null) -> Type
  = #t
    = if min then :min = min
    = if max then :max = max

IntRange ({#t is Integral}, min:#t = Null, max:#t = Null) = Type
  = #t
    = if min then :min = min
    = if max then :max = max

IntRange = #(T is Integral)
  Fn (min:T = Null, max:T = Null) -> Type
    = T
      = if min then :min = min
      = if max then :max = max

IntRange =
  #
    T is Integral

  Fn (min:T = Null, max:T = Null) -> Type
    = T
      = if min then :min = min
      = if max then :max = max

U8 = IntRange(Int, 0, 255)

### Example: Different type based on param

// Valid? better formulation of this possible?
Singleton = (b:Bool) -> if b then Nat else List Nat
  if b
    42
  else
    [ 1, 2, 3 ]

### Example: Struct

