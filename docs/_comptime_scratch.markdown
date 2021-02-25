## Comptime is
------------------------------------------------------------------------------------------------------------

Description at comptime:
* data
  * eg types, traits, lookups, cache, ...
* code/block/step
  * eg macro, inline steps, step, ...
* fn
  * eg partial function, capture, variant, generic, polymorphism, ...
  * include tested input, config, externalities (eg state), etc. with mapping to output
  * 
* finite amount of processing possible
  * not really a problem usually b/c programs are finite even if they're proving infinite things
    * just b/c there's a arbitrary-sized integer doesn't mean it's infinite, instead it's until OOM or crash
* caching
  * cache everything that's possible to speedup comptime processing
  * only true runtime values need to be left to runtime
  * use quick tests instead of redo
    * eg hash of file to assure don't need to reread
  * cache submodules, steps, and parts wherever possible

Expression of comptime at runtime:
* Memory
* Lookup in tested/precomputed/verified input/output
  * optimization
  * Runtime value, externalities, etc. are compared against lookup once and then given result or is memoized (or added to comptime known) if not yet known
* Immutable compiler info and metadata (eg type, branch)
* Templates to be filled out? w/ values?
  * eg partial definition, partial block, partial function, 
* 

Intermediaries between runtime to utilize comptime:
* Variants, bounds, tests, etc. brought into comptime knowns
* Methods to lookup
* Define variants to vary to test and those variants which can be ignored
* 

Runtime only:
* IO, externalities, unknowables, etc. which return unknown results each time (eg REST API)
  * Can still be bound but may be unknowable
  * Poisons others which depend/derived based on it
* 

Devs use comptime in different ways for different goals which shouldn't be conflated:
* specification
  * codify known and unknown
  * must be as arbitrarily descriptive as needed
  * won't necessarily be provable
* 
* ideas/scratch
  * specification
    * expect
    * known and unknown
    * rules, constraints, bounds
    * often breaks into:
      * what dev knows and unknowns are
      * what they want to be done
      * what they expect
  * syntax
    * term rewriting
    * macros
  * services
  * execution
  * codegen and code-erasing
  * define abstractions and customize them
    * eg from composition (multiple fields) to interface (abstraction) to logical (SOA, AOS, etc.) to memory (actual memory layout)
    * 
  * tools
  * examples
  * tools
  * control/interface with compiler
    * eg control how overloaded functions are handled w/ variants, conditionals, etc.
    * eg overwrite code in some cases to customize how code is handled
  * info that the compiler needs to make smarter decisions
  * these are all separate steps of correctness
    * specification
    * analysis
      * verification here?
      * testing here?
    * developer handle analysis results, effects, requirements, etc.
      * eg dev must provide more proof for analysis that will fail to meet requirements they've configured/requested
      * 
    * 
  * these are all separate steps of execution?
    * 
  * optimization?
* 


## Comptime Generation
------------------------------------------------------------------------------------------------------------

Ideas:
* 

TODO:
* List out the target of metaprogramming
  * targets
    * variables
    * names
    * scope
    * branch
    * stack
* 

Questions:
* How to unify if multiple locations need different parts of the same data?
  * Generate once set of data and then share w/ each?
  * Up to the developer to get right and decide to create once structure to share or do multiple times?
* How to model unknowns
  * unknowns like:
    * state
    * runtime data
      * runtime options
    * valid and invalid compositions of data, state, etc.
    * 
* How to model data which has incomplete information?
  * Just like a function which returns a different definition?
* Need to be able to model caller interation requirements
  * requirements like:
    * input the caller must supply
    * optional input the caller may supply
    * free variables
    * implicit variables
      * eg capture
      * eg ctx, state, caller, defn details
* 

### Generate at Comptime

### Examples - Ante Trait w/ Default Methods
```
trait RelOps 't
    //require cmp to be implemented
    cmp 't 't -> i32

    //and implement all comparison operators based on cmp
    (=) a:'t b:'t =
        cmp a b == 0

    (<) a:'t b:'t =
        cmp a b < 0

    (!=) a:'t b:'t =
        cmp a b != 0

    (>=) a:'t b:'t =
        cmp a b >= 0

    (<=) a:'t b:'t =
        cmp a b <= 0

    (>) a:'t b:'t =
        cmp a b > 0


impl RelOps Str
    cmp l r = strcmp l r

//now cmp, =, <, !=, >=, <=, and > are defined for Str
```

// TODO:
// - Need Elder equivalent for `trait RelOps 't`
```
```

### Examples - Ante Combine Trait
```
// Inline
print_eq a:'t b:'t -> unit given Eq 't, Print 't
    print a "==" b "is" (a == b)


// Type Aliasing
type EqPrint 't is 't given Eq 't, Print 't

print_eq (a: EqPrint 't) (b: EqPrint 't) =
    print a "==" b "is" (a == b)
```

```
```

## Runtime to Comptime Bridge
------------------------------------------------------------------------------------------------------------

Ideas
* Nothing is truly runtime only b/c something must be known about it (even if it just Bytes must be later interpreted as something). How to utilize this?
  * Check heuristic?
  * Enumerate known options which are meaningful to context?
* Do runtime check (like flow typing) which categorizes runtime, unknown data
  * Checks everything at once and associates to a unique, known, comptime state which is a combination of all possible state
  * Also can handle if unknown or invalid combined state which isn't handled (ie not expected)
* Reason about runtime values as range and sub-ranges instead of a value
  * The value is uknown (until tested) but can be part of a range (which are it's possible values)
* Refinement
  * Induction
* Literal (eg TypeScript literal)
* Previously tested comptime value and do manual lookup at runtime
  * There is a cost but it's a one-time cost to lookup which is precalculated comptime branch
* Rework runtime and model so it's not needed to know exact value as multiple ranges of values can be treated together (as they have the same result, effect, output, etc.)
* Move more to comptime and constrain surface area of runtime as small as possible to truly variable values
  * This also means we can erase more data as only the variable values needed at runtime
* Model the valid combinations of ranges more and more precisely (eg cross-cutting, combination of multiple ranges in a struct)
* Generate code paried with comptime knowns
  * can be JIT, generate new code, self-modifying, codegen, generate variants, etc.
* After checking the value, have code branches later on which use result. Like flow typing but can be used to generate variants in code to make branchless.
* Runtime staging
  * eg https://dotty.epfl.ch/docs/reference/metaprogramming/staging.html
    * 
* Test values during testing and then add to comptime known
  * Over time will learn more about what is used
* Provide proof details to SMT solver
* 
