---
layout: default
title: Names
permalink: names
---

Scratch:
* 

Elder purposely does very little to complicate names because they used for so much. Instead of names only being operated on and used for structure, names act like their own namespaces (much like ***realtors*** do).

Names serve multiple purposes beyond most languages which use them as identifier:
* A name is it's own namespace and the details which describe that name are nested under it.
  * This often results in simpler code as you don't have to create artificial conventions
    * For example, we can avoid unncessary complexity of tracking data related to name `x`.
      Compare the contrived example which adds boundry conditions and memory model to a name by using convention:
      ```
      x               // Name we're referring to
      x_bounds        // Boundry conditions
      x_bounds_min    // Boundry conditions min
      x_bounds_max    // Boundry conditions max
      x_mem           // Memory model for x
      x_mem_is_stack  // Is a stack reference
      x_mem_ref_count // Reference counting 
      ```

      vs using `x` like it's own namespace:
      ```
      x               // Name we're referring to
      x:bounds        // Boundry conditions under metadata (reminder metadata relator syntax is `:`) of `x`
      x:bounds.min    // Boundry conditions min
      x:bounds.max    // Boundry conditions max
      x:mem           // Memory model under metadata (reminder metadata relator syntax is `:`) of `x`
      x:mem.is_stack  // Is a stack reference
      x:mem.ref.count // Reference counting
      ```
    * This often means we don't have to create external structure when we really just want to refer to specific names
  * The default action of a name is to get and set it's value. This means, for the default cases it acts just like a normal variable.
* Keyword-driven computation
* Isolation, uniqueness, shadowing, aliasing, 
* Context and internal specification pattern
* 

We keep names rather unadulterated and don't confuse their use with modifiers (like `const`). Instead names have their own concerns like:
* declaration
* shadowing
* uniqueness
* keywords


## Names are Namespaces
------------------------------------------------------------------------------------------------------------

