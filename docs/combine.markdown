---
layout: default
title: Combine
permalink: combine
---

Create a mechanism to assure every possibility is handeled across multiple domains and aspects. Additionally, once handeled, the handling itself must be composable.

For example, consider how Zig uses a few mechanisms to assure ErrorSets compose in specific ways. Haskell has similar ideas but using Monad and Monoids.

Examples
* All possible errors that can arise from a Block
  * Must be handeled from calling Block (even if to just indicate to bubble if that's even allowed)
  * Data should not be lost
  * If error arises from w/n a loop must be clear about how the error will bubble, how the loop will be handeled, if should be aggregated, etc.
* All results must be handeled
* How to compose data which may overflow (eg Integer overflow)
* Not determinable if composition of functions combined w/ logic will result in valid state or not. Not determinable at comptime.
  * Maybe have a safe, test point after?
* Combinations of effects
* Combine handlers so that their changes combine in sensible ways that are predictable and ideally clear in the code itself w/o too noisy
* Even if same exception, effect, etc. done multiple times in same block, can't be ambiguous which they are

Ideas
* Process is something like:
  * Everything known must be modeled in the code (all states, errors, rules, constraints, etc.)
  * When handeled, must be exhaustive and every case accounted for
  * The calling context must compose the handling code in a reasonable way
    * can't just drop or ignore it unless it is in code that they chose to do so
    * eg if error from w/n a loop over a collection of items how should calling code aggregate errors and results?
      * not allowed to niavely exit early w/o clear intent that's what they want to do b/c too likely to create a bug
      * instead some type of combinator of: array + (exceptions or data)? or transactional rollback?
  * Outside calling contexts which call the calling context which handles the data has some control or ability to paramaterize or combine w/ what they call
    * there's 3 levels here:
      * the Block which emits
      * the calling context
      * the calling context's calling context
    * they must all cleanly compose together and this may mean they have to interact and control one another?
* Look at effects systems and how they handle nested effects?
