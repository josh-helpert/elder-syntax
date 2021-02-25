---
layout: default
title: Declaration
permalink: declaration
---


### Example: Bound with MinMax

TODO:
* Need a way to describe details about the definition and then the actual definition itself
  * b/c it's necessary to define, describe, and constrain terms before the definition itself (eg preconditions)
  * This should be computable/executable at comptime
* Need a way to add more constraints after the definition
  * Use the definition to add more rules like: `min <= max`
* Must be clear why and how what names and what relator are present where
  * eg how to be clear `:min` is present and it means what's expected?
    * or should this just be exported names and can be placed anywhere?
  * should use a operator equal to `like` which indicates it must be a similar form?
    * or should use a series of rules which mean they must be present?
  * A way to differentiate inclusive or exclusive for min and max?
    * Often way you create multiple classes but that can also be annoying b/c the values are the same?

Ordered = #do
  ...

Ideas:
* comptime function
  ```
  MinMax = #Fn T:Type -> Trait
    Rule
      T is-a Comparable

    Trait
      min:T
      max:T

  U8 = MinMax Int
    min = 0
    max = 0
  ```
* comptime capture block
  ```
  MinMax = #do
    T = TypeCapture
     :is-a Comparable

    #Fn T -> Trait
      Trait
        min:T
        max:T

  U8 = MinMax
    min = 0
    max = 255
  ```
* comptime capture further define inline
  ```
  MinMax = #Fn T -> Trait
    Rule
      T = TypeCapture
      T is-a Comparable

    #Fn T -> Trait
      Trait
        min:T
        max:T

  U8 = MinMax
    min = 0
    max = 255
  ```
* comptime long call
  ```
  ```
* comptime block
  ```
  MinMax = #do
    T = Type
    //T is-a Comparable
    //T:type is-a Comparable
    
    Type
      :min:type = T
      :max:type = T

      Rule

  U8 = #implements Int MinMax
    :min = 0
    :max = 255
  ```
* comptime implicit `#` to complete defn?
  ```
  MinMax = #Fn T -> Trait
    #
      T = TypeCapture
        :is-a Comparable

    #Fn T -> Trait
      Trait
        min:T
        max:T

  U8 = MinMax
    min = 0
    max = 255
  ```
