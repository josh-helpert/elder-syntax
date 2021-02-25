## TODO
* differentiate between simpler syntax and more complex
  * means we can model simpler syntax b/c most of the complex are just sugar for simpler statements
    * also means we more closely model simple syntax and then build on that
  * decide when to add this, and other sugar, back in?
    * need to decide the limits between most basic structure, sugar, etc. as even sugar itself can cause compositing issues
  * eg
    * complex
      * selection `o.(a, b, c):(x, y)`
      * multi-assign `o.(a, b, c) = 1, 2, 3`
      * destructure `x, y, z == 1, 2, 3`
    * simple
      * seq of exp
      * simple assign
      * chain
      * multiline lit
      * inline lit? or just chain?
* Add clarity on parens between precedence `()` and nested sequence `/()`
  * add syntaxes for shorthand like `/x, /y` is `/(x, y)`
  * Parentheses drop is many cases and don't make children
    * but can be used to gruop expressions when not evaluated? like in `for`?
    * `/()` is for sibling literal only but will also eval w/n?
* until code is evaluated nothing is executed
  * eg consider base:
    ```
    x = 1
    y = x
    ```
    * Since doesn't eval, `y = x` is exactly that. Only in eval context can `y = 1`
  * 
* Headers and block sections
  * Way to comment and group together, divide into steps, etc.
    * Also useful to define custom directives, settings, metadata, etc. about the following block
      * Can be used for a block or file header
  * 







### Reduction

(1)
(a) // Should this reduce to just `(a)`?
(1 + 2)
(1, 2)

### Expand

Different expansions:
* `o.(a, b = 1) = 0`
  * Groups and expands to:
    ```
    (o = 0; o.a = _; o.b = 1)
    ```
* `o.(a, b) = 1, 2`
  * Groups and expands to: `(o.a = 1, o.b = 2)`
    ```
    (o.a = 1, o.b = 2)
    ```
  * Additional parenthesis aren't needed b/c clear w/ parenthesis before expansion?

### Function and Relator usage pattern

### Conflict





Cases
* Precedence
  * `x = (3 + 4) * 5 => x = 35`
  * `x = (1, 2, 3)`
* Code/Block
  * should signatures explicitly look for a block? or should a single expression work?
    * eg
      ```
      if
        x > 0
        y < x
        z > x
      then
        log x, y, z
        z
      else
        log x, y, z
        x
      ```
      translates to shorter:
      ```
      if   (x > 0 and y < x and z > x)
      then (log x, y, z; z)
      else (log x, y, z; x)
      ```
      translates to shorter:
      ```
      if   x > 0 and y < x and z > x
      then log x, y, z; z
      else log x, y, z; x
      ```
      translates to inline:
      ```
      if (x > 0 and y < x and z > x) then (log x, y, z; z) else (log x, y, z; x)
      ```
    * Pattern seems to be a group of children can be parens?
      * relators use this as well:
        ```
        x
          .a
          .b
          .c
        ```
        is also:
        ```
        x .(a, b, c)
        ```

        just like:
        ```
        x
          a
          b
          c
        ```
        is also:
        ```
        x /(a, b, c)
        ```
  * eg
    * `if (...) then (...) else (...)`
    * `when (...) (...)`
    * `cond (...) ( (...), (...) )`
* DRY/path
  * `o.(a, b = 1, c) = 0`
  * `o.(a, b, c)`
* other
  * function
    * f(a, b, c, d, e)
    * f(a, (b, c, d), e)
    * f (a, b, c, d, e)
    * f a, b, c, d, e
    * f a, (b, c, d), e
    * f (a, (b, c, d), e)
    * f(a, [b, c, d], e)
    * f [a, [b, c, d], e]
    * f([a, [b, c, d], e])
    * f(a, g(b, c, d), e)
    * f(a, (b + c + d), e)
  * distribute
    * 4 (1, 2, 3)
  * structure
    * same as Code/Block?
      * desirable in any way outside of Block b/c we actually don't want permanent structure from `()` but use `[]`?
  * 

## Brackets and Parenthesis
------------------------------------------------------------------------------------------------------------

Parenthesis Issues:
* What about forms where there may be a mistake and unclear if to eliminate parens?
  * equals
    ```
    x = (1)   => x = 1
    x = ((1)) => x = 1

    a = 1
    x = (a)   => x = 1
    x = ((a)) => x = 1

    x = (1 + 2)   => x = 3
    x = ((1 + 2)) => x = 3

    x = (1, (2, 3), 4)   => x = (1, 2, 3, 4)
    x = ((1, (2, 3), 4)) => x = (1, 2, 3, 4)


    x = f(x)     => 
    x = (f(x))   => 
    x = ((f(x))) => 
    ```
    * Don't want for equals to implicitly evaluate b/c data forms don't evaluate
      * What do they represent then? Just a grouping? the shape of data?
      * or should I add some sugar so that parenthesis are dropped in a similar way so that the data and eval forms match in practice?
* `()` need to be orthogonal to lists `[]` so clear when to use each
  * Since `()` may be dropped not to use them when you want to store the sequence forever?
* `()` reduction rules need to be clear
  * when they'll be dropped
  * what is and isn't dropped or evaluated
  * what is structure
* forms
  * function
    ```
    f a, b, c
    f(a, b, c)
    f (a, b, c)
    f ((a, b, c))
    f((a, b, c))
    ```
  * sibling functions
    * if
      ```
      ```
    * for
      ```
      ```
* Which forms have priority?
  * eg DRY with expression
    ```
    o.(a, b = (1 + 2), c = 3) => ?
    ```
  * 

We have to model tools for both data and code. Consider each aspect:
* aspect/task
  * precedence
    * eg `(3 + 4) * 5`
    * eg `x = (1, 2, 3)`?
    * only useful for infix operators?
  * grouping
    * eg `f(x, y), z`
  * sequence
    * eg `1, 2, 3, 4`
    * eg `1, (2, 3), 4`
    * eg `1, [2, 3], 4`
  * nesting inline sequence
    * eg `1, [a, b, c], 2, 3`
  * DRY, path, common
    * eg `o.(a, b) => o.a, o.b`
  * start/stop
    * eg `o .(a = 1, b = 2, c = 3)`
  * block
    * Used to group into a block so that multiple statements are together
      * Not sure if `()` or `[]` is more appropriate?
        * If we use `()` then it's used for both grouping and blocks while `[]` is reserved for data while `()` is never data.
    * eg inline conditional
      ```
      if (x < 0 and y > 1) then (log x; 0) else (log y; 1)

      if(x < 0 and y > 1 ) then( log x; 0 ) else( log y; 1 )

      if( x < 0 and y > 1 )then( log x; 0 )else( log y; 1 )
      ```
  * code
    * 
  * 
* 




Part of issue may be determining equivalences so that the syntax can flow more cleanly?
* eg conditional
  * multiline
    ```
    if
      x < 0
      y > 1
    then
      log x
      0
    else
      log y
      1
    ```
  * fn-like
    ```
    if(x < 0, y > 1) then(log x; 0) else(log y; 1)
    ```
  * group-like
    ```
    if (x < 0, y > 1) then (log x; 0) else (log y; 1)
    ```
  * keyword-like
    ```
    if x < 0, y > 1 then log x; 0 else log y; 1
    ```
  * block-like
    ```
    if [x < 0, y > 1] then [log x; 0] else [log y; 1]
    ```
* Rebol
  * either
    ```
    either (rebol-is-very...interesting? = true) [
        print "Is Rebol very interesting?"
        print "Signs point to yes"
    ] [
        print "Is Rebol very interesting?"
        print "My sources say no"
    ]
    ```
* Clojure
  * defn
    ```
    (defn greet
      [name]
      (str "Hello, " name))
    ```


Reduction rules
* Determines when a `()` will be removed
* How does outside operations (like `do`, `eval`, `reduce`, `reform`, ...) affect and distribute w/n `()`
  * Diff things distribute based on outside and inside?
    * eg expression inside and integer juxtaposition outside
    * eg sequence of names inside relator outside
* 
* 


Maybe add convenience for parenthesis as they don't represent data so that we can have cleaner syntax?
* add space
  ```
  f a, b, c, d, e   === f(a, b, c, d, e)
  f(a, b, c), d, e  === f(a, b, c), d, e
  f (a, b, c), d, e === f(a, b, c), d, e
  f a, b, c; d, e   === f(a, b, c), d, e
  ```
* chain sibling?
  ```
  f (a, b, c) g (x, y, z) === ???
  ```



Since Elder uses the same syntax for data and code there needs to be a way to group data and group code. Elder differentiates these by using:
* `()` for:
  * Precedence in computations (like most languages): `5 * (3 + 4)`
  * Represents the start `(` and stop `)` of:
    * expression like `(1 + 2)`
    * operator like `+(1, 2)`
    * function like `f(1, 2)`
    * relator like `o.(a, b)`
  * Groups elements together
    * like `x = (1, 2, 3)`? or must use `[]`?
    * like `` `1, (2, 3), 4` `` in code block?
* `[]` for:
  * Represents the start `[` and stop `]` of:
    * sequence literal like `1, [2, 3], 4`
    * multiline anonymous element like:
      ```
      1
      [
        2
        3
      4
      ```
    * 


When one is composing data, there's really only two locations you generally want the sub-data to be located:
* As a sibling to other elements
  * This is referred to as an atom in LISP
  * eg the sequence `1, (2 + 3), 4` should result in the subexpression `(2 + 3)` being inline to it's siblings `1, 4` like `1, 5, 4`
* Isolated w/n a container
  * In LISP, ...
  * In Rebol, ...
  * eg the 

LISP implementations differentiate a expression `(...)` from a sequence literal `` `(...) `` by including a backtick while making brackets `[...]` used as a way to differentiate from parenthesis but semantically the same or sometimes as a `Vector` literal (a la Clojure).






As a reminder, a list of names delineated by commas is a sequence. With our current syntax it's not possible to represent nested sequences inline.

Consider the simple multiline example:
```
a
b, c, d
e, f
```

Notice that this is really a sequence of 3 elements which are themselves sequences. To model this inline we'll have to introduce new syntax like:
```
a, [b, c, d], [e, f]
```

### Parenthesis expanded

* When does expand?
  ```
  f: () -> (1, 2)
  ```
  * Consider using this:
    * Use siblings
      ```
      count(0, f(), 3)
      = count(0, (1, 2), 3)
      = count(0, 1, 2, 3) // Drop () b/c nothing to do?
      = 4
      ```
    * Use immediately
      ```
      count(0, count(f()), 3)
      = count(0, count((1, 2)), 3)
      = count(0, count(1, 2), 3)
      = count(0, 2, 3)
      = 3
      ```
      * Notice subexp are fully reduced before outer expression begins
    * Wrap w/ []
      ```
      count(0, [f()], 3)
      = count(0, [(1, 2)], 3)
      = count(0, [1, 2], 3)
      = 3
      ```
    * Wrap w/ ()
      ```
      count(0, (f()), 3)
      = count(0, ((1, 2)), 3)
      = count(0, (1, 2), 3)
      = count(0, 1, 2, 3)
      = 4
      ```
* This more trouble that it's worth b/c it's like auto-composing w/n a context?
  * the rule can be:
    * whatever the subexp returns it's written in place
      * if it's a singular value then it's written unwrapped there
      * if it's multiple values it's written unwrapped there
    * use `[]` for sequence literals and `()` for grouping, precedence/ordering, evaluation, distribute, start/stop, ...
    * 
  * How to model quoting which changes eval?
    * We use code literals which surround it? Like markdown does?
      * Either use `\` or more backtick than subexpression uses w/ spaces?
    * 
### Brackets vs Parens

### Optional syntax for grouping

Use-cases:
* Common
  * Sequence of single values (eg `x, y, z`)
  * Key-Value is single key and name (eg `age = 26`)
  * Value is a sequence (eg `staff = [ Jile, Bob, Jane ]`)
  * Value is result of evaluation (eg `next = get-next()`)
* Somewhat common
  * A few conjoint values (eg `name = Josh Helpert`)
  * 
* Rare
  * A few conjoint names (eg `first name = Josh`)
  * Complex structure

Common-sense rules:
* words are delineated by spaces. Since the most common these are siblings.
  * eg Elder `x y z` === JS `[ x, y, z ]`
* key-value are delineated by `=`.
  * When inline, the words to the left and right become the name and value
    * eg `x = 1 y = 2 z = 3`
  * When inline name and multiline value, the left becomes the name and the indented children become the value
    * 

* Not ideal to have commas be optional for data? even if slightly noisier?
  * Data are like: Nouns or Direct Objects
    * noun-like:
      * they are what verbs use as their context, choose implementation, configure the function
        * OOP not necessary as often the state is stored in noun-like containers and supplied to functions. This serves a similar conceptual purpose.
      * Having multiple nouns doesn't make sense but all the most rare cases
    * direct object-like
      * what's operated on by the function
      * they are what's acted upon and are often function params
      * Having multiple direct objects is very common
  * Functions are like: Nouns, Verbs, or Direct Objects
    * noun-like
      * this is useful for refinement or meta or composition
    * verb-like
      * they are supplied state, called on a noun-like, or supplied direct object-like
    * direct object-like
      * this is where implementation is selected by context and passed into the calling function
* The difference between using commas and not isn't that it's not possible for each to act in a certain way but rather that they all act in a role.
  * When their role is:
    * noun-like
    * verb-like
    * direct object-like

### Compare to other syntaxes

### Paths and selectors

* From CSS
  * Basic
    * universal: `*`
    * type: `input`, `button`
    * class: `.my-header`
    * id: `#my-id`
    * attribute:
      * Syntax: [attr] [attr=value] [attr~=value] [attr|=value] [attr^=value] [attr$=value] [attr*=value]
      * `[autoplay]` will match all elements w/ `autoplay` to any value
  * Grouping
    * selector list: `div, span`
  * Combinators
    * descendent: `div span` match all `span` that are inside a `div`
    * child: `ul li` match all `li` nested directly under a `li`
    * general sibling: `p ~ span` match all `span` that follow a `p` immediately or not (not necessarily adjacent)
    * adjacent sibling: `h2 + p` match all `p` that directly follow an `h2`
    * column combinator: `col || td` match all `td` that belong to the scope of `col`
  * Psuedo
    * class:
      * state info that is not in the document tree
      * `a:visited` match all `a` visited by the user
    * element:
      * elements not included in the document tree
      * `p:first-line` match the first line of all `p` elements
* From SASS
  * 

### Issue: Inconsistent syntax
* Selecting uses the ends of a tree while chaining returns the target value
  * They have very similar syntax while the semantics are different
* Nesting forces them to assign values w/n a `()` like `x.(y, z = 4) = 3`
* Maybe rule is ???
  * Rules
    * In exp, if using selection only then will not pass head
      * eg `x:(a, b, b.(c, d))` will not pass `x` when an expression
                   vs
           `x:(a, b = 1, b.(c, d))` will pass `x` b/c b is using an assignment?
    * Otherwise, head is passed as first element to binding
  * Issues w/ composing this?
    * Does it cause side-effects one wouldn't expect?
      * What about when mixed w/ the multiple ways to select values?
* In general, when should we constrain the methods of assignments
  * Types:
    * selection/destructure (L-hand name and R-hand values)
      * eg `o.(x:(a, b), y/c, z), t = 1, 2, 3, 4, 5`
    * path
      * eg `o.x:a, o.x:b, o.y/c, o.z, t = 1, 2, 3, 4, 5`
    * w/n parens/subexp
      * eg `o.(x:(a = 1, b = 2), y/c = 3, z = 4), t = 5`
    * literal
      * eg `o .(x:(a = 1, b = 2), y/c = 3, z = 4); t = 5`
        * Should we terminate `o` using `;`? and to indicate `t` as a sibling of `o`?
    * multiline
      * eg 
        ```
        o.
          x:
            a = 1
            b = 2
          y
            c = 3
          z = 4
        t = 5
        ```
    * mixed
      * eg 

```
// This is inconsistent b/c inner sub-exp doesn't act like inner exp
// - x:(a, b) = 1, 2 === x:a = 1; x:b = 2
// - o.(...) === o = 3 .(...)
o.(x:(a, b) = 1, 2) = 3

o.(x:(a = 1, b = 2)) = 3

o.(x:a = 1, x:b = 2) = 3

o = 3, o.x:a = 1, o.x:b = 2

o, o.x:(a, b) = 3, 1, 2

o = 3
  .x:(a, b) = 1, 2

o .(x:(a, b) = 1, 2) = 3
```

```
o.(x, y, z) = 1, 2, 3

vs

o.(x, y = a, z) = 1, 2, 3
```

```
o.(x.y.(a = 1, b) = 2) = 3
```

### Same task, diff ways (consider what readable, composable, useful for multiple contexts, etc.)

* Allowed
  ```
  // Multiline
  o = 1
    .x = 2
      :a = 3
      :b = 4
    .y = 5

  // Build
  o          = 1
  o.(x, y)   = 2, 5
  o.x:(a, b) = 3, 4

  // Destucture w/ paths
  o, o.x, o.x:a, o.x:b, o.y = 1, 2, 3, 4, 5

  //

  ```
* Maybe
  ```
  // Destucture w/ parens
  o, o.(x, x:(a, b)), y) = 1, 2, 3, 4, 5

  // Literal w/ parens w/ R-hand value
  o .(x :(a = 3, b = 4) = 2, y = 5) = 1

  // Literal w/ parens w/ L-hand value
  o = 1 .(x = 2 :(a = 3, b = 4), y = 5)

  // Use chain implicit return to assign to R-hand value
  o.(x:(a = 3, b = 4) = 2, y = 5) = 1

  // Literal w/ terminal
  o = 1 .x = 2 :a = 3, :b = 4; .y = 5

  // Chaining implicit return of 1st value
  o.(x:(a = 3, b = 4) = 2, y = 5) = 1

  // Chaining
  o.(x:(a = 3):(b = 4) = 2).(y = 5) = 1

  // Implicit return name in subexp
  (o = 1).((x = 2):(a = 3, b = 4), y = 5)

  //

  ```
* Not Allowed
  ```
  ```

### Complex, nested example
```
// Multiline
o = 1
  .a = 2
  .b = 3
    :x = 4
    :y = 5
  .c = 6
  :d = 7
  :e = 8

// Incremental build syntax TODO!

// Not possible? when and what is possible for this form?
o = 1 .a = 2, .b = 3 :x = 4, :y = 5; .c = 6, :d = 7, :e = 8

// possible using `;` terminal? reads terribly...
o .a = 2, .b :x = 4, :y = 5; = 3 .c = 6, :d = 7, :e = 8; = 1

// literal has similar purpose of connected w/ paren?
o .(a = 2, b :(x = 4, y = 5) = 3, c = 6), :(d = 7, :e = 8) = 1

// chaining has similar purpose to spaced w/ paren?
o.(a = 2, b:(x = 4, y = 5) = 3, c = 6):(d = 7, :e = 8) = 1

// paths to destructure
o, o.(a, b, b:(x, y), c) = 1, 2, 3, 4, 5, 6
```

```
f 1, 2, g 3; 4, h 5, 6; 7

f(1, 2, g(3), 4, h(5, 6), 7)
```

### Orthogonal syntaxes
* Purposes
  * get using paths
  * assign: path on L-hand, values on R-hand
    * This may affect destructure and chaining?
  * 

### Nesting
* almost looks like default values of a function?
* 

o
  .x = 1
  .y = 2
    :a = 4
    :b = 5
    .t = 6
    .u = 7
  .z = 3

o .(x = 1, y :(a = 3, b = 4), .(t = 6, u = 7) = 2, z = 5)

o .(x = 1, y:(a = 3, b = 4).(t = 6, u = 7) = 2, z = 5)

or don't allow it? or only allow in multiple lines when nesting?

### Mixed
* Problems
  * path and inline assignment syntax too close where it becomes hard to tell
    * need to be clear about when selecting, when assigning, when def literal, etc. and should ORTHOGONAL task
      * select must use L-hand for paths to names and R-hand for value
      * literal w/ space
      * define via chaining
      * define via

o .x = 1, .b, .c = 1, 2 // Does this mean b, c = 1, 2 or o?






## Invalid, Dissallowed, Issues

### object literal vs equals?

### Determine when we have assignment and literal what name is exposed?
```
obj 1, 2, 3
```
vs 

```
o = obj 1, 2, 3
```

```
obj 1, 2, 3

o = obj
```
* is `o` or `obj` mutable?
* which name is exposed? `o` or `obj`?
* any copying? or only referencing?
* issue of data literal vs reference?
* issue is the `o` is a variable while `obj` is a tree literal
  * could make a rule that only variables are mutable while tree literals are not?


### Determine if this is allowed or not (consider w/ nested functions)
The complex tree can be compacted as well:
```
x
  a
  b /1, /2, /3
  c
```

but not fully inline with our current syntax:
```
x /a, /b /1, /2, /3; /c
```
This is close but we specifically disallow having nested inline relators. Notice that `/1, /2, /3` are children of `b` and are nested under `x`. This is disallowed to make the code readable and allows us to make the assumption that a inline relator always refers to the first element. We'll be able to better model this w/ syntax introduced later.

### Make it clear what's reserved

### Nested assignment
Meaning of:
```
x
  a = 1
  b = 2
    y = 3
  z = 4
```
Ideas:
* Just a series of entries w/ a mapping
  * Depends on interpretation if valid semantically or not?
* 

### Top level
* literals
* adjacent lines
* names/variables?

### Inline Sequence of Sequence (& top level?)
If we wanted to represent a sequence of seqences using the current syntax we'd be stuck with something like this:
```
a, b, c
x, y, z
1, 2, 3
```
Notice that we know:
* this isn't one long list b/c it's not allowed that we mix expanding and inline siblings
* each sub-sequence belong to the same parent sequence b/c they are visually on each line (ie no gaps or `\n \n`)
* is equivalent to:
  * `'( (a, b, c), (x, y, z), (1, 2, 3) )` in LISPy languages
  * `[ [a, b, c], [x, y, z], [1, 2, 3] ]` is the closest JavaScript can represent

We can futher compact the code using another new syntax `;`
* Represents the end of sequence
* Often useful for a sequence of functions and arguments and some data-structures
* For consistency, you can't mix inline and multiline syntax `;` for the same sequence
* At the end of a line if there's a `DEDENT` it implicitly terminates a sequence

Using this, we can compact the previous sequence of sequences to:
```
a, b, c; x, y, z; 1, 2, 3;
```

### Access
Accessing relator names syntactically acts more like other languages.

to access:
```
obj.b
```
which is read as:
* access `obj` name
* access `.` relation in `obj`
* access `b` name in `.` relation
* return value of `b`

to assign:
```
obj.b = 3
```
which is read as:
* access `obj` name
* access `.` relation in `obj`
* access `b` name in `.` relation
* name `b` equals 3

[//]: # (TODO: obj.* get and set tuple?)

### Parent-Child Relator

Here's an example:
```
x
  /a
  /b
  /c
```

this is equivalent to:
```
x
  /
    a
    b
    c
```

and can be inline:
```
x /a, /b, /c
```

to access:
```
x/c
```

to assign:
```
x/c = 1
```

### Object-Property Relator

Here's an example:
```
obj
  .a = 0
  .b
  .c = 2
```

this is equivalent to:
```
obj
  .
    a = 0
    b
    c = 2
```

and can be inline:
```
obj .a = 0, .b, .c = 2
```

to access:
```
x.c
```

to assign:
```
x.c = 3
```

### Data-Metadata Relator

Here's an example which models `Age` as positive, integral number:
```
Age
  :type = Int
  :min  = 0
```

this is equivalent to:
```
Age
  :
    type = Int
    min  = 0
```

and can be inline:
```
Age :type = Int, :min = 0
```

to access:
```
x:min
```

to assign:
```
x.min = 1
```

Elder levels:
* Syntax (s-exp, StrictYAML)
  * with implicits (JSON)
  * with schema (InternetObject, StrictYAML w/ Schema)
  * with types
  * with variables
  * with functions

Using this, we can further compact our previous examples from:
```
x /a, /b, /c
obj .a = 0, .b = 1, .c = 2
Age :type = Int, :min = 0
```

to:
```
x /(a, b, c)
obj .(a = 0, b = 1, c = 2)
Age :(type = Int, min = 0)
```

### Destructure
* Complex?
  * Incomplete forms?
* Wildcards
* Skip `_`
  * Skip multiple? `_(2)`
* Will destructure automatically if:
  * No values are assigned?
  * Is totally ambiguous L-hand
    * eg `x = 4`
    * eg `(x, y) = 1, 2`
      * L-hand values are a sequence literal `(...) = ...` (eg `o.(a, b, c) = 1, 2, 3`)
* `==` forces destructuring
  * Desugars to `) = (`
* Issues
  * data vs execution context
    * Ambiguous between execution context or data context where precedence of `,` and `=` are swapped
      * in data, precedence order: `=` > `,` (for multiple elems)
      * in exec, precedence order: `,` > `=` (for destructure)
    * How to know if data or exec context? may not be worth it...
  * Compare:
    ```
    x = a
    x = a, b, c
    x = (a, b, c)
    x == a
    x == a, b, c
    x == (a, b, c)
    ```

* Syntax
  * Consider using `==` for destructure which acts just like `=` but a lower precedence than `,`
    * This means we can then easily mix `=`, `,`, and `==` universally?
    * Precence order will be like:
      * `=` top
      * `,` middle
      * `==` bottom
    * Then can also compose other aspects we have to consider like:
      * Shadowing using `:=` or `:==`
      * Constness using `-=` or `-==`
        * Likely constness needs to be it's own thing b/c it's rather nuanced?
      * Mixed liked `-:=` or `-:==`?

or as destructure:
```
o.a, o.b, o.c, o:x, o:y, o:z = 1, 2, 3, 4, 5, 6
```

or as destructure:
```
o.(a, b, c), o:(x, y, z) = 1, 2, 3, 4, 5, 6
```

or even as a inline literal with destructure subexp (TODO: precedence an issue here between `,` and `=` or can be dissambiguated? possible to dissambiguate or must choose?)):
```
o .(a, b, c = 1, 2, 3), :(x, y, z = 4, 5, 6)
```

### Juxtaposition

`o .x .y .z`

`o .x, .y, .z`

### Distribute

* based on type
  * For relators, paths are distributed
  * For numbers, do what?
  * For symbol, do what?
  * For fn, do what?
* syntax pattern
  * options
    * `el (...)`
    * `el(...)`
    * `(...) el`
    * `(...)el`
    * `(...) (...)`
    * `(...)(...)`

## Relator
### Nested Inline? like subexp or nested fn? eg `o .x .y .z`
### Declaration Sugar

## Parenthesis
// Then nest them? and show how need better syntax

```
o
  .
    a = 1
    b = 2
      :
        x = 4
        y = 5
        z = 6
    c = 3
```




  ```
  o
    .x
      :a = 1
      :b = 2
    .y
      c = 3
    .z = 4
  t = 5
  ```

  ```
  o = 1
    .x = 2
      :a = 3
      :b = 4
    .y = 5
  ```

  ```
  o.
    a = 1
    b = 2
    c.d.
      x = 3
      y = 4
  ```




### TODO: selection

### TODO: selection and assignment (w/n and not)
o.
  a = 1
  b = 2
  c.d.
    x = 3
    y = 4

o .(a = 1, b = 2, c.d.(x, y) = 3, 4)
o .(a = 1, b = 2, c.d.(x = 3, y = 4))
o.(a, b, c.d.(x, y)) = 1, 2, 3, 4


o.(x, y) = 1, 2
o.(x = 1, y = 2)
o.(x, y = 1, 2)
o.(x = 1, y = 2) = 3, 4
o.(x, y = 1, 2) = 3, 4

### Advanced Destructure

o.(x, y, y:a, y:(t, u)) = 1, 2, 4, 6, 7

o.(x, y).y(:a, .(t, u)) = 1, 2, 4, 6, 7

o.(x, y), o.y(:a, .(t, u)) = 1, 2, 4, 6, 7

### Chaining
------------------------------------------------------------------------------------------------------------

It useful (and common) to have syntax like `x:Int = 0`. We can't represent this exactly with our current syntax but we can get close using a pattern named chaining.

Chaining is meant to be a very natural extension of the the canonical usage of `=`. Here's a simple logical progression of the syntax:
* Assign a name to a value
  ```
  x = 4
  ```
* Add some metadata to a name as well as assign it to a value
  ```
  x:(type = Int) = 4
  ```
* Add some additional metadata
  ```
  x:(type = Int, min = 0, max = 1024) = 4
  ```
* Add some properties
  ```
  x:(type = Int, min = 0, max = 1024).(a = 0, b = 1) = 4
  ```
* For clarity, here's the expanded form:
  ```
  x = 4
    :type = Int
    :min = 0
    :max = 1024
    .a = 0
    .b = 1
  ```

The effect of chaining is to pass along the name on the L-hand of `=` so it can be related to the R-hand value. This isn't always desirable so there are rules when chaining will occur:
* Within an expression, if all names aren't assigned values it will not chain. Conversly, if any name is assigned it will.
  * This is b/c when destructuring we don't set values on the L-hand, just reference them. We need the syntax to be clearly different between destructuring and chaining.
* It must be inline
* Generally chaining should be avoided if there's too much information and the expanded form should be used instead but this isn't enforced

TODO:
The effect of these rules is a simple syntax rule:

TODO:
The effect of this rule is that it's not possible to both define data on the L and R hand of a `=` in a arbitrary way. This segments the type of `=` syntaxes into 2 tasks:
* Destructuring where the L-hand is references to one or many names
* Data definition where the L-hand is describing data and values. Depending on the syntax, this may chain.


Here's a few valid examples which will chain:
* Simple inline
  ```
  x:(type = Int) = 0
  ```
* Some values set, but not all
  ```
  x:(a, b = 1, c) = 0
  ```
  which is equivalent to the more expanded form:
  ```
  x = 0
    .a
    .b = 1
    .c
  ```
  which chains although not all the names on the L-hand are set, some of them are.

  This works b/c when values are set on the L-hand side this generally only occurs when we're defining data. Generally we only want chaining when defining data.

  When values aren't set (here `a` and `c`) they are still present and assumed to be structural names which may be used later even w/o a value.
* Inner subexpression destructure
  ```
  x:(a, b, c == 1, 2, 3) = 0
  ```
  which chains b/c in inner subexpression is evaluated before the outer expression.
* Multiple chain relators
  ```
  x:(type = Int).(a, b = 1, c) = 0
  ```
  this is equivalent to the more expanded form:
  ```
  x = 0
    :type = Int
    .a
    .b = 1
    .c
  ```
  which chains b/c some names are set on the L-hand.

  What's unique about this form is that it's allowable to have multiple relators on the L-hand. This is why we used the word chaining b/c it's possible to chain multiple relators and each will emit the starting name.

and a few counter examples which won't chain:
* Path
  ```
  x:a.b = 0
  ```
  this is read as a path `x:a.` to the name `b`. This means the name `b` is emitted from the L-hand which relates to value `0`.
* Destructure
  ```
  x:(a, b, c) = 0
  ```
  This is read as a destructure b/c no names are assigned on the L-hand side. This means the names `a, b, c` are emitted from the L-hand. Since there's only a single value on the R-hand it's only related to `a`.
* ???
  ```
  (x, y = 1, z) = 0, 2
  ```
  There's nothing to chain here but there's also assignment on L-hand...

### Levels

* Simple Structure
  * S-Expressions
* Multiple Structures
  * StrictYAML
* Literals
  * YAML
  * JSON
  * 
* Types
  * 
* Schema
  * StrictYAML
  * InternetObject
  * 
* Comptime
  * Codegen
  * Comptime Proofs
  * 
* Decidable
  * Dhall
* Homoiconic
* Next?
  * Metalang
  * Refinement Types
  * Dependent Types
  * 

### Return data constrains of any type
* Extact data values (4, :String)
* Values w/n a range (3..5)
* Type
* Shape of data (*:type = Any)

### Get value vs reference
x = 4   // Def x
y = x   // Ref x
z = x() // Value x

## More Destructuring
------------------------------------------------------------------------------------------------------------

### TODO: matching arity, multiples, skip, ...

## Parenethes and Brackets

* Concat
* A Block which groups statements, often unevaluated, together:
  * inline conditional `if (x > 1 and y < 2) then (log x) else (log y)`
  * inline while `while (x > 0) (log x; x -= 1)`
  * group data together like `((Billy Bob), 35)` as long as it's not evaluated

## Type signature
* arity and types
  * (3 * Bool) === 3 bool expression
  * (Bool, Int, Bool) === sequence of Bool, Int, Bool
  * [3 * Bool] === 3 bool expression w/n brackets

## Parens or blocks determine evaluation
* eg `for ()` vs `for []`
* form, concat, etc. (like Rebol) don't work for `()`
* block is both a list and a type of execution. How to know which acting as?
  * determined by ctx, parent, interpretation, etc.?

### Container and Type meaning
Body for container
  Containers are interpretation contexts

### Example - Covert Parenthesis and Blocks

It's trivial to covert from parentheses (really just a sequence) into a block. You just have to wrap it w/ brackets:
```
x = (1, 2, 3)
y = [ x ] => [ (1, 2, 3) ] => [ 1, 2, 3 ]
```

The opposite, brackets to parentheses, is more complex as we essentially want to get the body of the block. 


Parentheses to Block

Block to Parentheses




* Block relation to indentation
* Anon item
* examples
* edge-cases
  * conditional `if (x > 0 and y < 10) then [log x; x] else [log y; y]`

### Blocks in fns

* Issues
  * Blocks can be a series of instructions as well as a list; how to know which?
    * eg if we're using block for setup then we need to return the condition at the end?

* conditional 

if (x > 0, y < 0)    then (log x; x) else (log y; y)
if (x > 0 and y < 0) then (log x; x) else (log y; y)
if (x > 0 and y < 0) then [log x; x] else [log y; y]
if [x > 0 and y < 0] then [log x; x] else [log y; y]

if
  x > 0
  y < 0
then
  log x
  x
else
  log y
  y

if (x > 0 and y < 0)
  log x
  x
else
  log y
  y

* for

for
  [
    i:Int = 0
    j:Int = 0
  [
    i < 10
    j < 100
  [
    i += 1
    j += 1
  [
    log i, j

for
  - i:Int = 0
    j:Int = 0
  - i < 10
    j < 100
  - i += 1
    j += 1
  - log i, j

for [ i:Int = 0, j:Int = 0 ], [ i < 10, j < 100 ], [ i += 1, j += 1 ]
  log i, j

for i:Int = 0, j:Int = 0; i < 10, j < 100; i += 1, j += 1
  log i, j

* while


### Parentheses reduction
* Before or after substution
* Parentheses used as:
  * data definition
  * data structure which will be processed
    * the structures is significant (like a ordered set)
  * handle/merge results
    * eg ( f(x), g(y) ) => ( a, b, c, d )
      * f => (a, b), g => (c, d)
* Notice that subsitution gets us the most general solution and then more characters to reduce it?
  * or more characters to preserve it? what makes the most sense for a default?

```
a = o.(x = 1)
b = a
```

```
a = f(x)
b = g(y)
c = h(a, b)

c = h( f(x), g(y) )
c = h( f', g' )
c = h'
```

```
a = (1 + 2)
b = (3 + 4)
c = (a, b)
d = (a + b)

c = ( (1 + 2), (3 + 4) )
c = (3, 7)

d = ( (1 + 2) + (3 + 4) )
d = ( 3 + 7 )
d = ( 10 )
d = 10
```

```
a = (1, 2, 3)
b = (4, 5, 6)
c = (a, b)

c = (1, 2, 3, 4, 5, 6)
c = ( (1, 2, 3), (4, 5, 6) )
```

```
a = ((Billy Bob), 16, 0)
b = ((Jilly Jane), 23, 1)
c = (a, b)

c = ((Billy Bob), 16, 0, (Jilly Jane), 23, 1)
c = ( ((Billy Bob), 16, 0), ((Jilly Jane), 23, 1) )
```

() and [] differ by:
* their semantic meaning
* () has no data representation; more a syntactical tool
* () can often be reduced based on multiple conditions
* 

## Parentheses
------------------------------------------------------------------------------------------------------------


Parentheses perform multiple duties depending on how they're used:
* Like in most languages they're the highest precedence:
  * expression  `(1 + 2) * 3`
  * infix operator `x = (1, 2, 3)`
* Is the start `(` and stop `)` of an operator, relator, function, etc.:
  * expression `(1 + 2)`
  * operator act-like function `+(1, 2)`
  * function input call `f(1, 2)`
  * relator data definition `o.(a, b = 1, c)` or `o .(a, b = 1, c)`
  * relator selection `o.(a, b, c)`
* As blocks:
  * in functions to group steps like `if (x > 0 and y < 10) then (log x; x) else (log y; y)`
  * even to model grouping of data like `((Billy Bob), 16), ((Jill Jane), 26)` to represent a custom grouping of 2 people with their names and age
    * or should this be: `[(Billy Bob), 16], [(Jill Jane), 26]`

The underlying pattern of parentheses is that they:
* group their contents together
* depending on how they used, the effect varies (eg `o.(a = 1)` means something different than `(1 + 2)` but the pattern is the same of grouping their contents together)



### Parentheses are not Data

Since parentheses are more like hints, they're generally not inspectable as they're automatically reduced and result in a unwrapped sequence. When composed, this has an effect that's novel.

Consider the following psuedo code:
```
x = (1, 2, 3)
y = (4, 5, 6)
z = (x, y)
```

TODO:
* Are they reduced now so the results is `z = (x, y) = (1, 2, 3, 4, 5, 6)`?
  * This reduces the composibility though right? b/c what if inner elements look like: `x = ((Billy Bob), 16)` then when composed will be flat elements?

The value of `z` won't be 2 sequences like one may expect. Let's go through it step by step.

The process which starts by substitution in place:
```
z = ((1, 2, 3), (4, 5, 6))
```

Since there is nothing left to do w/n the inner expression, the parentheses are reduced:
```
z = (1, 2, 3, 4, 5, 6)
```
Notice that although we started with several sequences, we end up w/ a flat sequence.

This may seem weird but it's a natural extension to do what we do in other languages. It's common in other languages to:
* return a single item like `4`, `8.7`, `'c'`, `"Hello"`
* return 0 to many items w/n some type of container like `[1, 2, 3]`, `{x, y, z}`

What parentheses allow us to do is add another category which act like multiple items w/o a container. This allows us to avoid extra wrapping and unwrapping of data which leads to more visual noise.

For example, consider this psuedo code where we use an external function to construct the properties of a HTML-like element:
```
f = Fn () -> Seq
  (width = 100%, height = 20px)

header .(f(), background-color = FFF)
footer .(f(), background-color = 000)
```

This results in what you'd hope w/o having to unwrap the function result:
```
header .(width = 100%, height = 20px, background-color = FFF)
footer .(width = 100%, height = 20px, background-color = 000)
```

## Blocks
------------------------------------------------------------------------------------------------------------

Much like parentheses, blocks (notated with brackets `[]`) wrap a sequence. However since a block is a container they are not reduced automatically.

This makes blocks appropriate to model:
* List which is just an isolated sequence
  * inline
    ```
    x = [1, 2, 3]
    ```
  * nested inline
    ```
    x = [1, [2, 3, 4], 5]
    ```
  * multiline
    ```
    x = 
      1
      [
        2
        3
        4
      5
    ```
* Unevaluted code
  * Often represent unevaluated code which is evaluated step-by-step
  * Differs from parentheses (which return each child) in that you can pick what is the result (often the last child)
    * This allows you to execute multiple statements (each child) while only returning parts of the sequence instead of each child.

Notice that, just like parentheses, blocks don't make a sequence (the `,` do). Instead they are a container around the sequence which provides context in how to interpret the sequence.

If you need to pick between parentheses or blocks here's a few general rules:
* Use parenthesis if you:
  * Want the result to be a sequence of values
  * Want to change the precedence
  * Don't want to unwrap the result so it'll merge with the location it's used it (whether a function or variable result)
* Use blocks if you:
  * Want to isolate a sequence even when composed
  * Execute a series of steps and only return the result of one of those steps. Which is how a Block is often evaluated.
  * Pick some values, but not all, of the children.

### Example - Composing

Consider the following example:
```
x = (1, 2, 3)
y = [1, 2, 3]
```
This is read as:
* `x` is a sequence of `1, 2, 3`
* `y` is a block containing a sequence of `1, 2, 3`

When used, the differences are clear:
```
a = (0, x, 4)
b = (0, y, 4)
```

the next step is substitute in place:
```
a = (0, (1, 2, 3), 4)
b = (0, [1, 2, 3], 4)
```

which reduces `a` further to the final result:
```
a = (0, 1, 2, 3, 4)
b = (0, [1, 2, 3], 4)
```

Notice how the inner block isn't reduced while the parentheses are.

## Common Patterns
------------------------------------------------------------------------------------------------------------

At this point we've introduced all the basic syntax needed to describe the common syntactical patterns.

For each pattern, there's a few effects to consider:
* When a pattern in used next to it's siblings; what name(s) are visible to other siblings.
  * For example, below `x` and `y` are siblings but `z` isn't:
    ```
    a
      x
      y
    b
      z
    ```
  * For convenience, we'll consider a "scope" as what siblings and their decendents can access. For Elder syntax, these are often just names and paths to those names.
* When used as a node in a tree what is returned by the subtree.
  * For example, the middle element is a sequence which is both a subtree and subexpression. It is the sequence `(b, c)`:
    ```
    (a, (b, c == 1, 2), d)
    ```
    this expands to:
    ```
    a, b = 1, c = 2, d
    ```

These are the common patterns along with the effects they will produce:
* Multiline Literal
  ```
  x = 0
    .a = 1
    .b = 2
    .c
    :d = 3
  y
  z
    .e = 4
  ```
  * Useful to model complex data as the structure is most explicit and verbose.
  * This can only be composed within other multiline literal patterns as it's the only pattern which uses multiple lines.
  * Effects
    * The sequence of names `(x, y ,z)` will added to scope.
    * When used in a subexpression `(x, y, z)` will be returned by the expression.
* Inline Literal
  ```
  x .a = 1, .b = 2, .c, :d = 3
  ```
  * Useful to model moderately complex data which is often structured line-by-line like HTML, CSS, etc.
  * It's possible to have multiple literals but the syntax becomes harder to understand.
  * Effects
    * The name `x` will be added to scope.
    * When used in a subexpression `x` will be returned by the expression.
* Chain
  ```
  x.(a = 1, b = 2, c):(d = 3) = 0
  ```
  * Useful to model nested structure as this more easily composes within other expressions.
  * Often used to mimic syntax like `x:Int = 4` which we approximate w/ our current syntax as `x:(type = Int) = 4`
  * Effects
    * The name `x` will be added to scope.
    * When used in a subexpression `x` will be returned by the expression.
* Selection
  ```
  x.(a, b, c):(d), z.e
  ```
  * Useful to traverse (or define) the structure to a name.
  * Selection multiple names results in a sequence of names. The above example expression becomes `(x.a, x.b, x.c, x:d, z.e)`
    * Since selection only returned the deepest names of a structure. If you want to pass higher level names (like `x` in the above example) they need to be listed as well. For example `x, x.(a, b, c):(d), z.e` would expand to `(x, x.a, x.b, x.c, x:d, z.e)`.
  * Effects
    * This pattern is a bit different. Remember only top-level names will added to scope. For this example, only the sequence of names `(x, z)` are top level while the rest describe structure internal to those names.
    * When used in a subexpression `(x.a, x.b, x.c, x:d, z.e)` is returned by the expression.
* Destructure
  ```
  x.(a, b):(d), z.e == 0, 1, 2, 3
  ```
  * Destructure is a combination of L-hand selection and R-hand value. The pattern is names of the L-hand and values on the R-hand.
  * Useful to model multiple return values, build data in a specific structure, define multiple names within scope.
  * Effects
    * This pattern is a bit different like selection above. For the same reason, only the sequence of names `(x, z)` are top level while the rest describe structure internal to those names.
    * When used in a subexpression `(x, x.a, x.b, x.c, x:d, z.e)` is returned by the expression.
* Sequence of Expressions
  ```
  x = 0, y, z.a = 2
  ```
  * Useful to model internals of a relator. Generally not preferred as a way to introduce names into a scope as can be confusing compared to multiline literal or destructure.
  * Since `,` is lower precedence than `=` this is a series of 3 expressions.
  * Effects
    * The sequence of names `(x, y, z)` will be added to scope.
    * When used in a subexpression `(x, y, z.a)` is returned by the expression.

Although each of these are generally equivalent to one another, some patterns have different effects. The next sections discuss what they are and the reasoning behind their design decisions.

### Top level names in scope

The names which are added to the current scope can be different than what is returned by an expression. The rule for this is rather simple: only the top-level names will be brought to scope.

Here's a few examples with the names brought into scope:
* Multiline Literal
  ```
  x
    .a = 1
    .b = 1
  y
    :c = 2
  z
  ```
  `(x, y, z)` are brought into scope. Their internal structure is internally scoped. For example `a` and `b` share the same scope of `x.`.
* Inline Literal
  ```
  x .a, .b = 1, :c
  ```
  `x` is brought into scope. `a, b` share scope under `x.` and `c` under `x:`.
* Chain
  ```
  x.(a, b = 1):(c)
  ```
  Scope rules are just like the previous example.
* Selection
  ```
  x.(a, b:(t, u), c)
  ```
  `x` is brought into scope. `a, b, c` share scope under `x.` and `t, u` under `x.b:`
* Destructure
  ```
  x.a, x.b, y.c.d, z == 1, 2, 3, 4
  ```
  Since only the top-level names are brought into scope; `x, y, z` are brought into scope.

  To better visualize the structure view it as a multiline literal:
  ```
  x
    .a = 1
    .b = 2
  y
    .c
      .d = 3
  z = 4
  ```
  It's clear which scopes are shared viewing it this way.
* Series of Expressions
  ```
  x, y = 1, z.a.b = 2
  ```

  This is read as a series of expressions. Rewriting it makes it even more clear:
  ```
  x
  y = 1
  z.a.b = 2
  ```
  It's clear now that `x, y, z` are brought into scope.

## Composing (In Progress)
------------------------------------------------------------------------------------------------------------

* VIA
  * Parent-child
  * Siblings
  * Subexp

## Identifier (in progress)
------------------------------------------------------------------------------------------------------------

In order to model concepts like operators, prefix/suffix fixity, relators, literals, etc. it's important that:
* it's very clear what are allowed identifiers for each type
* concepts which combine into single words use orthogonal characters
* deliniation?
* 

* cases
  * relator
  * relator name
  * identifier
  * reserved
  * canonical
  * upper case
* 




## Reserved (in progress)
------------------------------------------------------------------------------------------------------------

Since the syntax must be usable for both data and execution we reserve some some identifiers for future use.

We reserve some identifiers for future use. TODO...

* comptime `#`
  * `#do`
* `do`
* 


## Codegen (in progress)
------------------------------------------------------------------------------------------------------------

### Comptime
### Codegen emit where in subexp
### Deterministic Patterns

## Sugar (in progress)
------------------------------------------------------------------------------------------------------------

### Operators act-like expanded tree node
Now that we know how relators are modeled; we can now describe an additional syntactical tool. Most operators can become a tree node when in the expanded syntax form.

Since we've already discussed it; let's use `=` which is usually a binary, infix operator.

Equals can also act-like a relator in it's expanded form. It works the same as a `= \n` as discussed above and does what you'd expect.
```
x
  =
    a
    b
    c
  .
    x
    y
    z
```

is equivalent to:
```
x = (a, b, c)
  .
    x
    y
    z
```

This works for most syntactical operators since they all support a tree structure.

### Defaulting (or space relator?)

because, by default (explained in more depth at `Defaulting`), a `INDENT` signifies a parent-child relation, this is equivalent to:
```
x
  a
  b
  c
```

### Anonymous Item
There are cases where we want to represent a series of elements without a meaningful name.

There are a few notes:
* The only valid identifiers are `-` and `*`
* Each sibling (items on the same indentation level) must consistently use the same syntax
* The identifier for a sibling must only be at the start of a line in a multiline form
* The identifier acts like a tag which means it can be added just to make items more clear

Consider the psuedo code:
```
my-elems = [
  { a, b, c = 3 },
  { t, u, v = 6 },
  { x, y, x = 9 }
]
```

Using our current syntax we'd have  ...

With our current syntax there isn't a way to represent this without weird syntax and visual noise. To avoid issues like this we'll introduce new syntax using `-` and `*` which:
* work like most markup languages use them as list-item identifiers at the start of a line. If they're used immediately after the leading whitespace then it doesn't indicate an item.
* only mean 

Rules:
* must use consistently for siblings
  * can't mix identifier or add identifier or not
* 

## Schema
------------------------------------------------------------------------------------------------------------

TODO
* Look at other solutions: StrictYAML, OGDL, InternetObject, GraphQL, other schema definition systems?
* 

## Not Representable
------------------------------------------------------------------------------------------------------------

Graphs which can't be represented should be multiline b/c too complex inline.

Can be a feature, as well, because forces inline to be simple graph.

## Precedence
------------------------------------------------------------------------------------------------------------

### Global Precedence
### Relative Precedence


### Parentheses Reduction

* Notes
  * When to know when () is for structural group and when for: precedence, sugar, etc.
    * b/c () only describes when must be grouped together and in many cases is alone
  * 2 types of ()
    * wrapping
    * start/stop
  * When grouping one thing does it always drop?
    * x vs (x) vs ((x))
      * grouping 1 item does nothing b/c it's not dissambiguating or grouping anything
      * cannot be it's own anonymous list b/c it's not data container
  * Clear grouping and not:
    * Clear, implicit group by syntax:
      * notes
        * notice it's unambiguous where these start and stop
        * they always expand to a sequence of expressions
      * examples
        * chain: `x.(a = 1)`
          * sugar -> value
        * select: `x.(a, b, c):d`
          * sugar -> seq of exp
        * explicit fn-call: `f(1, 2, 3)`
          * exp -> Any
    * Operator, infix-like (which will drop surrounding parentheses):
      * notes
        * like an infix operator so it's either:
          * unclear where it stops like mutliassign, destructure, fn-call juxtaposition
          * want to specify precedence
        * 
      * examples
        * multi-assign: `x.(a, b, c) = 1, 2, 3`
          * exp -> seq of exp
        * destucture: `x, y, z == 1, 2, 3`
          * exp -> seq of exp
        * exp: `x + y`
          * exp -> value
        * fn-call juxtaposition: `f a, b, c`
          * exp -> Any
    * Not grouping:
      * notes
        * the common sibling structure of Elder syntax
      * examples
        * seq of exp: `x = 0, y, z = 2`
  * ideas
    * non-composable types? this realisitic?
  * Cases to test:
    * same siblings
    * nested
    * with parentheses
    * in context of variuos types like:
      * siblings
      * operators
      * 
  * contexts used in:
    * evaluation: will reduce to a single value and `()` are dropped
      * eg
        * precedence
          ```
          (1 + 2)            => 3
          x = (1, 2, 3)      => same
          x = (1, (2), 3)    => x = (1, 2, 3)
          x = (1, (2, 3), 4) => x = (1, (2, 3), 4)
          ```
    * data definition: reduce to seq of exp and `()` are meaningful
      * eg
        * `1, (2, 3), 4`
        * 
    * assigning names: reduce to a seq of exp (l-hand) and values (r-hand)
      * always uses `=` or `==`
        * always on the L-hand to select name and structure which desugars into a sequence of names
      * maybe just a subset of data defn?
      * eg
        * `a = 0, (b, c == 1, 2), d = 3 => a = 0, b = 1, c = 2, d = 3`
        * `a = 0, (b, c, d == f()), e   => a = 0, b = f():0, c = f():1, d = f():2, e`
        * `(x, y, z) = f()              => x = f():0, y = f():1, z = f():2`
        * `x, y, z == f()               => x = f():0, y = f():1, z = f():2`
    * unevaluated code block: `()` are preserved until processed
      * eg
        * `for ((i:Int = 0, j:Int = 0), (i < 10, j < 100), (i += 1, j += 10)) (...body...)`
        * `if (x > 0 and y < 4) then (log x; x) else (log y; y)
    * sugar
      * all reduce to sequence of expressions?
      * eg
        * selector
          * `x.(a, b, c):(d)        => x.a, x.b, x.c, x:d`
          * `x.(a, y:(b, c), d):(e) => x.a, x.y:b, x.y:c, x.d, x:e`
        * chain
          * `o.(a, b == 1, 2) = 0 => o.(a = 1, b = 2) = 0 => o = 0, o.a = 1, o.b = 2`
            * notice `()` not dropped b/c of prefix?
          * `o.(a = 0, (b, c == 1, 2), d = 3) = 0 => o.(a = 0, b = 1, c = 2, d = 3) = 0`
            * notice `()` dropped within b/c no prefix?
          * `o.(a = 0, p:(b, c == 1, 2), d = 3) = 0 => o.(a = 0, p:(b = 1, c = 2), d = 3) = 0`
            * notice `()` not dropped b/c of prefix?

examples:



Single item always reduces:
```
x     => x
(x)   => x
((x)) => x
```

Expressions reduce to single value:
```
1 + 2     => 3
(1 + 2)   => 3
((1 + 2)) => 3
```

Literal definition is only head name:
```
x .(a = 1)     => x
(x .(a = 1))   => x
((x .(a = 1))) => x
```

Chain definition is on head name:
```
x.(a = 1) = 0     => x
(x.(a = 1) = 0)   => x
((x.(a = 1) = 0)) => x
```

Selection has natural start/stop:
```
x.(a, b, c):(d)     => x.a, x.b, x.c, x:d
(x.(a, b, c):(d))   => (x.a, x.b, x.c, x:d)
((x.(a, b, c):(d))) => (x.a, x.b, x.c, x:d)
```

Multiple assign is a infix operator (so must have parens for expression):
- but isn't this decidable? b/c we know exactly how many elements will be assigned? and `=` is lower precedence than `,`?
```
???
x.(a, b, c) = 1, 2, 3     => x.a = 1, x.b = 2, x.c = 3
(x.(a, b, c) = 1, 2, 3)   => x.a = 1, x.b = 2, x.c = 3
((x.(a, b, c) = 1, 2, 3)) => (x.a = 1, x.b = 2, x.c = 3)
```

Destruture is a infix operator (so must have parens for expression):
```
???
x, y, z == 1, 2, 3     => x, y, z
(x, y, z == 1, 2, 3)   => x, y, z
((x, y, z == 1, 2, 3)) => (x, y, z)
```

Sequence of expressions are the basic syntax:
```
x = 0, y, z = 2     => x, y, z
(x = 0, y, z = 2)   => (x, y, z)
((x = 0, y, z = 2)) => (x, y, z)
```

used in:

a = (1, ?, 3)
b = (1, (?), 3)


if these rules apply how to deal with complex forms like `for`?
```
for ((i:Int = 0, j:Int = 0), (i < 10, j < 100), (i += 1, j += 10))
  ...body...
```
note that:
* these are code blocks which aren't evaluated
  * it doesn't matter if it's an expression or not (b/c it's uneval)!
    * this means our rules about inner expression really isn't the case b/c only if processing code will `do` it?


scratch:
* what about wrapping a function return? b/c it's dynamic?
  ```
  f = () -> (2, 3)

  a = (1, f(), 4)           => 1, 2, 3, 4
  b = (1, (f()), 4)         => 1, (2, 3), 4 or 1, 2, 3, 4 // depending if wrapping return is meaningful when not literal
  c = (1, (x, y) = f(), 4)  => 1, x = 2, y = 3, 4
  d = (1, (x, y == f()), 4) => 1, x = 2, y = 3, 4
  ```
  * May be useful to have different syntax for wrapping vs precedence?
    * We know if there's multiple `()` then there will never be more than one once complete?
    * Could have wrap-like syntax which must use spaces or multiple?
      * eg
        ```
        a = (1, f(),       4)
        b = (1, (f()),     4)
        c = (1, ((f())),   4)
        d = (1, ( f() ),   4)
        e = (1, (( f() )), 4)
        f =
          1
          f()
          4
        ```
      * Differences between:
        ```
        ?       // natural value
        (?)     // precedence, sugar, other?
        ( ? )   // precedence, sugar, other?
        (( ? )) // group
        ```
        * this would drastically affect things like the usage of `for`?
          ```
          for ((i:Int = 0, j:Int = 0), (i < 10, j < 100), (i += 1, j += 10))

          for ( (( i:Int = 0, j:Int = 0 )), (( i < 10, j < 100 )), (( i += 1, j += 10 )) )

          for | (i:Int = 0, j:Int = 0), (i < 10, j < 100), (i += 1, j += 10) |

          for ( i:Int = 0, j:Int = 0 | i < 10, j < 100 | i += 1, j += 10 )
          ```

## Types of grouping
------------------------------------------------------------------------------------------------------------

```
`
'
"
~
^
$
%
@
```

* syntax options
  * open/close
    ```
    (
    ((
    [
    [[
    {
    {{
    |
    ||
    <
    <<
    ```
  * delimiter
    ```
    |
    ||
    ,
    ,,
    ;
    ;;
    .
    ..
    ...
    ```
* 

## Captures
-------------------------------
asign results of capture:
```
// Get results of block
a, b, c = do
  ...

// Use results of prev block as capture to new block
// Get results of block
x, y, z = do (a, b, c)
  ...

log x, y, z
```

vs do in steps/chain:
```
|>
  do
    ...
  do (prev/0, prev/1, prev/2)
    ...
  log (prev/0, prev/1, prev/2)
```
