---
layout: default
title: Modifiers
description: Modifiers description
permalink: modifiers
---



## 
------------------------------------------------------------------------------------------------------------


Scratch
* Different elements to consider
  * Each element has different concerns
  * Elements
    * Name/Symbol
      * Shadow
      * Rebind
      * Introduce/let
      * Destructure
      * Visibility (like `public`, `private`, and others)?
    * Pointer/Reference
      * Update/Mutate
      * Const (only point at a specific address)
    * Value
      * modifiers are a shorthand to relator names and defaults
        * eg `const`
      * Describe details about the meta (and other relators) of the values
* Issues
  * Compatibility
    * When is it enforced and not? D has a novel approach to differentiate `const` vs `immutable`
    * How to avoid useless noise?
      * What is inferrable and not?
    * Don't want everything to be compatible so we need to model this in some way
      * `const` is only one type but others like `Object Capabilities` are more complex
      * Validity, enforcement, etc. will likely need to be their own system which is more robust and well-defined?
  * 
