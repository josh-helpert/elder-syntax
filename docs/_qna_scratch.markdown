

## Ideas
------------------------------------------------------------------------------------------------------------

### Why homoiconic (or data oriented) language?
* Easier to grok syntax as AST mimics syntax
* Comptime becomes a process of:
  * Add new data and relators
    * for structure
    * 
  * Add functions 
    * for rules, constraints, semantics
    * 

* Elements
  * Name
  * Relator
  * Value
    * Seq
  * Function
  * Comptime
 
### Name-oriented
* Benefits
  * Each name acts as their own namespace
  * Allows hiding details below the name w/o creating stub-variables (eg `x`, `x_lock`) which indicate what they target
  * Jives w/ keywords code generation (and erasure when not used)
  * Private-ness is also possible by adding a match condition
    * Only if match condition succeeds will keywords be usable
    * Way to assure shared namespace isn't polluted or collides
  * 
* Downsides
  * May want to require multiple names to add explicitness
    * Still possible you just add them and name as you like
    * Seems unlikely in the general case you'd want to?
  * 
