---
layout: default
title: Condition System
permalink: condition-system
---

## Scratch

Goals
* Generalize condition system
  * Useful to require callers to supply more details and called to request them
  * Parts
    * Emit:        problems, questions, unknowns, choices to be made, etc.
    * Declare:     potential answers, responses, solutions, etc. to what is emitted below or equal to yourself
      * Partial or full details, answer, solution
      * Combine multiple partial details into more complete answer
    * Select:      from potential answers
      * A single answer, combine multiple together, etc.
* Pick choices at specific times, conditions, matches, and data
  * Can select at comptime w/ no runtime overhead
    * Then can pick method to implement like codegen variant for each choice or function pointer
* 

Ideas
* A way to declare incomplete details
* A way to provide details across the multiple layers of the stack
  * Different layers can supply different details, functions, etc.
* 

Issues
* Composing
  * 
* Multiple conflicting answers and selections
  * Multiple ways to conflict
    * Choice of answers when composed
    * Guarantees of selected answers aren't what expected
* DRY
  * 
* Incomplete and unhandeled answers
* Pass on 
* How better than passing arguments?
* 
