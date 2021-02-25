---
layout: default
title: Rules
permalink: rules
---

One of Elder's goals is to allow the developer to much more fully specify what they know (and assume) about their code. To make the implicit details, explicit code.

This has several effects:
* The code is much closer to live documentation than most languages where comments are often used to specify usage, prerequisites, environmental details, etc.
* We must allow arbitrary rules at almost any location within the code.
  * This implicitly means we must allow unprovable rules. To ameliorate this we will add several tools to work with claims which aren't provable.
* Developers can develop (or select from) static and dynamic analyzers to verify and generate code
* Move away from a focus on specialized constraint system like Interfaces, Traits, Protocols, etc. to a more general conceptualization of comptime rules
  * This is similar to Racket Contract or Clojure Spec but we allow for more general constructs
* Instead of hard-coding a few specification systems like Types, Interfaces, and Inheritance as the only means to model a domain; developers are able use whatever is appropriate for the problem their solving.
  * This often means using different rules and models for different parts of the codebase



TODO:
* Rules must target/use something
  * Often pulled w/ specific keys in some relator like `:` or `.`
* How to know how input data is meaningful or not?
  * eg `x:(type = Int, min = 0)` is a rule or is it a metadata key or both?
    * or the rule requires it be specified/claimed by the caller?
* Parts that pair w/ rules?
  * Rules + Analyzers + Data + Claims + ..?..
    * rules: are requirements
    * analyzers: are that which reads rules, data, claims, and descriptions
    * data: actual data which flows through system
    * claims: are statements made by the developer which are often not analyzable or knowable statically
* Builtin analyzers
  * Type
  * Memory
  * 
* 
