---
layout: default
title: Comptime
permalink: comptime
---

Many languages use compile time in just a few ways and is kept isolated from the developer. Elder takes a different approach by making it easier (and safer) to develop at compile time.

Elder exposes compile time is in a few main ways:
* When compilation occurs (lexing, parsing, codegen, ...)
* Correctness
  * Specification
    * 
  * Analysis
  * Verification
* Code generation (codegen) and erasing
* Compile time (comptime) execution and knowns
* 











In Elder, concepts like Types, Traits, Interfaces, Protocols, etc. are really just a subset of a features provided by compile time execution.

Instead of thinking of specialized concepts which conflict and have nuanced requirements, Elder conceptualizes all of these as compile time execution combined with the metadata relator (`:`). We also give special meaning to some metadata relator keys like `type` but a developer is free to add their own.

Arbitrary compile time execution is possible but that will be in a later section. For now, we'll just review a limited type of execution.

The eventual goal is to:
* Allow the developer to model
* Conceptualize compile time first and then, only when necessary, use runtime
* Make the code amenable to analyzers so it's possible to have external static analyzers

TODO:
* How is this document constrained compared to arbitrary execution?
  * What does this include and exclude?
* How does this help with DRY code?
  * Template-like? Generics?
  * Data instead of arbitrary processing?
  * Look at Dhall and see what can extract?
  * Look at decidable languages and see what can extract?
  * Look at other languages which use templates, comptime exec, etc.
* Do I go over the usefulness of codegen?
* Need to explain how this is more general than Types, Traits, etc.
  * Also relates how to applies to metadata relator
  * Imcomplete/partial types?
* How does this relate to comptime known?
* Used for data declarations and then filled out by callers?
  * Like schema + shape w/ partial complete
  * Focus on data, not functions, at this point
* Instead of having specialized comptime and language concepts use computation
  * Use comptime and partial 
  * Have a few reusable contructs and apply them in diff ways like:
    * comptime
    * relator
    * functions
    * data
    * data target
    * data shape
    * partial evaluation
      * Definitition has holes or leaves some for caller to specify
      * All must be specified before executed but can have multiple reference it so can special in diff context bit by bit?
    * builtins: types, eval order, etc.
* Novel and multiple ways to integrate comptime and runtime
  * Like the classical ways but also beyond considered a constant in BSS
  * examples
    * use comptime to generate data which is a form of verification
      * then directly use some of that data in runtime w/o computation
        * eg set to local stack data but can be used in multiple places to save of long-term memory
        * a way to reuse computation w/o storing in memory
    * partially evaluate, in different branches and contexts, then generate functions specific to each for straightline code
      * this is a general issue wher we want as much straightline code as possible
    * use comptime to compute and then generate lookup tables
      * tables can be loaded and unleaded into memory (or constant)
    * use comptime to create memory heirarchy and init w/ data
      * then a reference to that data can be used by runtime to populate based on optimal access
    * use comptime to generate conforming data. Associate claims to the conforming data.
      * then that data can be loaded, w/ the type and claims, to whatever memory information is useful
      * this is a way to avoid proving a specification but instead generate conforming data first (although we'll need others)
* Uses for comptime
  * macro, AST transformation, term rewriting
  * comptime lookup
  * partial evaluation
  * signature and definition
  * analysis
  * comptime known
    * literals
  * 

## Schema Syntax Ideas
------------------------------------------------------------------------------------------------------------

Need different syntax b/c we must express much more details about things like method signatures, rules in blocks, rules in specific contexts, etc. which means a simple schema is often not enough.

Ideas:
* Use data or def structures to build up abstractions and then use those concepts in the schema (much like Haskell uses data defn)
* Write a schema overview then add details w/n the schema after
  * This is like the schema provides the general overview of the details but the rest are inline?
  * or, the gaps in the schema are refined, defined, and specified after it but the schema gives you the overview even if you have to look for the definitions?
* Have a schema-specific definition vs the inline version (like Haskell) so we can focus on them separately
  * For those case where you just want to define and infer you can do so in a single statement like `f = Fn (...) -> (...)` which will infer from it's args. In cases of more complex you can separate the type of `f` out?
* Have holes, gaps, placeholders, names that are unbound in schema but will be filled out w/n the block
* Make it like a block where it can really be any series of steps but the last step is the definition
  * Means we can build it up in steps using `#do`
  * Can be extended to require input by making it like a function where it accepts input at comptime like `#Fn`
* 

Goals:
* Want simple forms to be able to be single line while more complex can be multiline
  * This also extends to things like blocks, data/type defn, captures, lambdas, functions, etc.
* It should share the same syntax and semantics as rest of Elder just a comptime version
  * It should also use the common constructs of: names, data, relators, functions, rules, etc. instead of fancier concepts like Traits, Interface, TypeClass, Generics, etc.
* 














## Comptime Known
------------------------------------------------------------------------------------------------------------

There are two types of compile time ways code is lifted to compile time: implicit and explicit.

Implicit are all the elements which are knowable at compile time like constants.

Explicit are identified by:
* The `#` prefix
* Some reserved comptime names `:`
* Literals
* Upper-case comptime names

## Partial Comptime Metadata
------------------------------------------------------------------------------------------------------------

## Lookup
------------------------------------------------------------------------------------------------------------

Notes:
* comptime known value will emit a constant and runtime known will require a single lookup
  * all are really lookups but is about if comptime known or not!
  * lookup will have to handle multiple cases:
    * categories
      * tested (ie comptime tested)
      * memoized
      * edge and custom cases
        * known invalid cases
        * known custom cases
        * unknown or unhandeled? or must be total categories over all data combinations?
    * lookup output will be everything needed to handle the result including w/o excessive branching (or none ideally):
      * new values
      * status, metadata, flags, info, etc.
      * generated code to custom handle results
      * 
      * 
* 
