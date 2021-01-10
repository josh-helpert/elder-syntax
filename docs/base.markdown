---
layout: default
title: Base
description: A minimal, data-oriented, and expressive syntax
permalink: syntax
---

Base syntax

## Sequence
------------------------------------------------------------------------------------------------------------

A ***Sequence*** (much like LISP, Rebol) is a core syntactical structure. A sequence is:
* A ordered, series of elements
* A syntactical concept
* Not a List, Array, Series, Set, et al. b/c these all make more and different guarantees

There are multiple forms (syntactical layout) that are used and we will slowly build them up over this document.

The simplest is the multiline form:
```
x
y
z
```

### Inline Siblings
To write more compact code we have to introduce new syntax `,`
* Represents a sibling w/n a sequence
* Internally `,` translates into a `\n` so the two forms are equivalent
* For consistency and clarity, you can't mix inline and multiline syntax `,` for the same sequence

Using this, we can compact the sequence to:
```
x, y, z
```

### Sequences of Sequences
We can express sequences of sequences by combining the syntaxes:
```
a, b
c, d
e, f
```
This is read as:
* a sequence of 3 items
* each item is a sequence
* each item contains 2 elements

Inline siblings (like `a, b`) are higher precedence than `\n` siblings this represents a sequence of multiple sequences.
This is consistent b/c we don't allow mixing `,` and `\n` sibling syntaxes for the same siblings.

Multiline precedence is done this way as it follows the natural English reading order top-to-bottom then left-to-right.
We want our syntax to follow what one would expect so it's easier to predict.

How to model more complex data will be introduced later.

## Tree
------------------------------------------------------------------------------------------------------------

A natural extension of a ***Sequence*** are ***Nested Sequences***. This is a ***Tree***.

A ***Tree*** is a primitive structure b/c:
* It's how code is naturally structured
* AST are modeled as trees which makes it easier for our code layout to match the expected syntax tree which make it more guessable and predictable

The multiline form (2 space `INDENT` represents a child):
```
x
  a
  b
  c
```
This code tells us that:
* `a, b, c` are children of `x` b/c they're nested under x w/ a 2 space `INDENT`
* `a, b, c` are siblings b/c they're at the same indendation level under the same parent

2 spaces are used b/c it's the minimal amount to make it clear there's indentation. No tabs, no variable spaces, nothing but 2 spaces per indentation. Later in this document there are ways to compact the syntax.

This can be nested arbitrarily and the structure matches what you expect as the layout matches the code layout:
```
x
  a
  b
    1
    2
    3
  c
```

### Multiple parents

When we say tree syntax it's really about describing the natural structure is tree like.

It's possible to describe data which isn't a tree like a some graphs:
```
a
b, c
  x
  y
  z
d
```

Notice that the syntax is still structurally a tree but we've described a simple graph as `b, c` are both the parents of `x, y, z`.

## Equals
------------------------------------------------------------------------------------------------------------

Equals (`=`) is a syntax which captures the pattern of relating ***L-hand*** to a ***R-hand***.

The precise relationship between ***L-hand*** and ***R-hand*** varies with the context it's defined in (eg assignment, mapping between names and values, key-value entry)

We can model definition or assignment:
```
x = 1
```
This is read as:
* name `x` relates to value `1`

We can model a map:
```
obj
  a = 1
  b = 2
  c = 3
```
This is read as:
* name `obj` has 3 children
* the 1st child is `a = 1`
  * name `a` relates to value `1`
* the 2nd child is `b = 2`
  * name `b` relates to value `2`
* the 3rd child is `c = 3`
  * name `c` relates to value `3`

### Equals Newline
Sometimes it makes sense to not have the value on the same line as the `=`.

Essentially the child or children become the value of the R-hand of the expression.

There's a few cases where the values is:
* A single value
  ```
  x =
    a
  ```
  * This is equivalent to `x = a` since `a` is the only immediate child of `x`
  * Often useful when the value is a self-executing expression
* A single, complex value
  ```
  x =
    a
      b
      c
  ```
  * This associates `x` name to the structure starting with `a` since `a` is the only immediate child of `x`
  * Often useful when the value is a complex data-structure
* Multiple values
  ```
  x =
    a
    b
    c
  ```
  and equivalently:
  ```
  x
    =
      a
      b
      c
  ```
  * This is equivalent to `x = (a, b, c)`. This is b/c the sequence `a, b, c` are all immediate children of `x`
  * Often useful when you want to store a literal, multi-valued, container-like (eg `List`, `Sequence`, `Map`)

## Relator
------------------------------------------------------------------------------------------------------------

A ***Relator*** is a syntactical tool which models the ***relation*** between a ***target*** and a ***descriptor***. Customizing the syntax allows developers to add structure to the syntax without the need of macros (although more constrained than what macros provide).

To understand this concept better consider other languages which are often hard coded:
* `C++`
  * accessing a `Namespace` uses `::` and is used like `my_namespace::my_val`
  * accessing a `Struct` field uses `.` and is used like `my_stuct.field` or `my_struct.field = 1`
* `Clojure`
  * accessing a `Namespace` uses `/` and is used like `my_namespace/my_val`
  * interop with `Java` or `JavaScript` uses `.` and is used like `(.toUpperCase "jane")`

Functionally it works much like a **namespace** but:
* characters can only be specific punctuation
  * Unicode will eventually be supported, but for now only ASCII printable characters (32 - 126) minus whatever is reserved
* identifier characters and relator characters must not overlap b/c relators are used as delimiters between identifiers (like `a/b/c` where `/` is a relator)
* is more general concept than a namespace and can be used to model more patterns
* can be syntactically used in more ways
* it is a tree node
* If multiple relators have the same syntax, the longest match wins
  * For example, if both `:` and `::` are defined `::` will always win
  * This also means multiple characters can be combined to make new relators.
    * For example, if `:`, `.`, `/` are all relators then `:./` would form a new relator distinct from the others.

Although developers can define their own, there are 3 relators are required to mean:
* `/` the child relator for parent-child relation
  * Often used to represent child inline
  * Replaces indentation
* `.` the dot relator for object-property relation
  * Often used to represent properties or attributes of an object
* `:` the meta relator for data-metadata relation
  * used to represent metadata of any data (and since it's homoiconic it can target almost anything)
  * does the work of describing conceptsa like types, constraints, relative types, rules, etc.

### Literal
In order to model data without additional syntactical noise, data can be written as literal trees. For relators, there's a few rules:
* When multiline, it must match the layout of a tree as described above.
* When inline, there must be a space between the target and the first relator.

To keep the examples simple, let's focus on the object-property relation `.` syntax.

With a example the rules become obvious:
```
obj
  .a = 0
  .b
  .c = 2
```

This is read as:
* declare tree literal with name `obj`
* add child to `obj` of `.` relation with name `a` then define as `0`
* add child to `obj` of `.` relation with name `b`
* add child to `obj` of `.` relation with name `c` then define as `2`

is equivalent to when grouped under a relator:
```
obj
  .
    a = 0
    b
    c = 2
```

This is read as:
* declare tree literal with name `obj`
* add child to `obj` of `.`
* add child to `.` with name `a` and define as `0`
* add child to `.` with name `b`
* add child to `.` with name `c` and define as `2`

is equivalent to inline:
```
obj .a = 0, .b, .c = 2
```

### Path
One aspect that's unique to relators is that they're used to create paths in order to:
* define data
* abstacts over concepts like file and network paths
* traverse data structure

Consider a slightly more complex example which uses both the `.` (object-property) and `:` (data-metadata) relators:
```
o
  .a = 0
  .b
    :x = 1
    :y = 2
  .c = 3
```

Using paths we can access and define this data in multiple ways. Syntactically paths acts more like other languages and uses the relator as a delimiter.

For example, to select `x` in `o` we write:
```
o.b:x
```
which is read as:
* access `o` name
* access `.` relation in `o`
* access `b` name in `.` relation
* access `:` relation in `b`
* access `x` name in `:` relation
* select name `b`

By default, selecting a value will return it's result.

It's also useful in when paired with equals syntax:
```
o.b:x = 3
```
This is read the same as the previous example however the final step will assign `3` to `x`.

Using paths we can also change how we define data. Consider an alternative way to define the above example:
```
o.
  a = 0
  b:
    x = 1
    y = 2
  c = 3
```

This is slightly more terse than the previous, structural representation as each of the common paths (`o.` and `b:`) are used. Although there's not much difference in this case; the usefulness is more pronounced in data which have deeply nested structure.

### Example - Deeply Nested Element
A more realistic example is to browse to HTML element we're concerned with and work w/n that scope:
```
html
  body
    header
      .
        style
        .
          css
            .
              width  = 100%
              height = 20px
```
Notice that there's lots of extra structure to get to the real work of setting `width` and `height` which is the purpose of this block.

Using paths we can do better and remove most of the syntactical noise:
```
html/body/header.style.css.
  width  = 100%
  height = 20px
```
We use paths to collapse the heirarchy from above to what we really are concerned about.

## Parentheses
------------------------------------------------------------------------------------------------------------

Although we strive for tersness, parentheses are essential. Parentheses are unique in that they are used within the syntax and grammar but they are not data. Instead, they are to be used to provide information to a developer, compiler, and structure data/code but are not data themselves. This makes them very useful to be a zero cost way to group data.

Let's start with a simple example where we want to relate `x` and the sequence `a, b, c` you may expect something like this to work:
```
x = a, b, c
```
This doesn't work like you expect because the precedence of `=` is higher than `,`.

This means it is interpreted as:
```
x = a
b
c
```

To get what we want we have to add parentheses to alter the precedence:
```
x = (a, b, c)
```
An important point is that the `()` don't make `a, b, c` a sequence, the `,` does. Instead, the `()` alters the precedence my making `a, b, c` into a group.

Parentheses perform multiple duties depending on how they're used using our existing syntax:
* Like in most languages they're the highest precedence:
  * infix operator `x = (1, 2, 3)`
* Is the start `(` and stop `)` of an operator, relator, function, etc.:
  * relator data definition `o.(a, b = 1, c)` or `o .(a, b = 1, c)`
  * relator selection `o.(a, b, c)`

Regardless of how they're used, the underlying pattern of parentheses is that they group their contents together.

### Example - Common Structure

Consider an example which has common structure:
```
o
  .
    a = 1
    b = 2
    c = 3
  :
    x = 4
    y = 5
    z = 6
```

Using our existing syntax we can represent this inline as a literal but it's rather noisy:
```
o .a = 1, .b = 2, .c = 3, :x = 4, :y = 5, :z = 6
```

Using parentheses we can express this in multiple forms which more clearly expresses the natural structure of the data. As a multiline literal:
```
o
  .(a = 1, b = 2, c = 3)
  :(x = 4, y = 5, z = 6)
```

as a inline literal:
```
o .(a = 1, b = 2, c = 3), :(x = 4, y = 5, z = 6)
```

or as a inline chain:
```
o.(a = 1, b = 2, c = 3):(x = 4, y = 5, z = 6)
```

### Example - Sequence of Sequences

With our current syntax we don't have a clear way to represent something like a sequence of sequences inline.

Consider the mutiline example:
```
x =
  a, b, c
  d, e, f
  j, k, l
```

Parentheses are only used for representing precedence and the start/stop of something. If want to represent this inline we would need to use the `/` syntax for a parent-child relator:
```
x = ( /(a, b, c), /(d, e, f), /(j, k, l) )
```

Syntactical sugar will be introduces later which remove more of the visual noise.

### Example - HTML Generation

Parentheses especially help when combining multiple relators to describe more complex structure. Consider the template psuedo code:
```
div .(id = "intro", class = "spashscreen", style.css.(width = 100%, height = 20px)), :visible-if = "is-first-view"
  h1 .class = "title"
  p .class = "description"
```

using a theoretical HTML generator this would translate to:
```
<div id = "intro" class = "spashscreen" data-visible-if = "is-first-view">
  <h1 class = "title"></h1>
  <p class = "description"></p>
</div>
```
Notice that `:visible-if` is an example of a field used for logic in a template. It could either be rendered w/n the HTML (as above) or erased at comptime depending on the use-case.

This syntax is much closer to what we see in languages that model trees directly such as: SDLang, YAML, Pug, SASS. To compact the syntax further syntax sugar and macros should be used although they outside the scope of this document.

### Example - Terse HTML inline CSS

Consider a use-case where we want to model a HTML element w/ inline CSS.

Implicitly create `.style.css` structure as they're necessary steps for valid HTML:
```
// As a multiline literal
my-element
  .style.css.
    width  = 100%
    height = 20px

// or, as a multiline literal w/ '.' ending
my-element
  .style.css
    .width  = 100%
    .height = 20px

// or, as a multiline literal w/ fully collapsed context
my-element.style.css.
  width  = 100%
  height = 20px

// or, as inline literal (notice space between head and relator '.')
my-element .style.css.width = 100%, .style.css.height = 20px

// or, as assignment (no space)
my-element.style.css.width  = 100%
my-element.style.css.height = 20px

// or, as assignment w/ selection
my-element.style.css.(width, height) = 100%, 20px
```
These variations are made available to fit various use-cases. The form which best models the data it's describing and terse should be preferred.

## Destructure
------------------------------------------------------------------------------------------------------------

Destructuring is useful as it allows us to deal directly with the structure of data and is generally more terse.

With our current syntax the problem is that `=` is a higher precedence than `,`. This means code like:
```
x, y = 1, z
```

is intepreted as:
```
x
y = 1
z
```

not:
```
x = 1
y = z
```

Since destructuring is very common we introduce a new syntax `==` which makes it easier to express with minimal visual noise. This is nearly identical to `=` except that:
* It's relative precedence is less than `,`
  * Note: We don't support a global precedence table like many C-style languages, instead all precedence is either relative or not-defined and you must use `()`
* It groups the L-hand and R-hand into `()`. This means `a, b == x, y` is basically `(a, b) = (x, y)`

Using the new syntax, there's a few ways to make this example parse as a destructure:
* Use `()` to group children together
  ```
  (x, y) = (1, z)
  ```
  It's really only essential to group the L-hand is the R-hand can be inferred as a sequence. So, in this case, this would work as well:
  ```
  (x, y) = 1, z
  ```
  It's clear now that we mean we can to assign both values `x, y`.
* Use `==` syntax
  ```
  x, y == 1, z
  ```

### Nested Destructure

Consider a slightly more complex example with nested destructuring:
```
a, (b, c == 1, 2) == (x, y == 3, 4), z
```

which after 1 step of evaluation is:
```
a, (b = 1, c = 2) == (x = 3, y = 4), z
```

after another step:
```
(a, b = 1, c = 2) = (x = 3, y = 4, z)
```

finally evaluates to:
```
(a, b = 1, c = 2) = (x = 3, y = 4, z)
```

## 6 Common Patterns
------------------------------------------------------------------------------------------------------------

At this point we've introduced all the basic syntax needed to describe the most common syntactical patterns that arise. These aren't exhaustive but they cover most of the common use-cases.

### Multiline Literal

Useful to model complex data it is most explicit and verbose. 

Multiline literals can only be embedded within other multiline literals as it's the only pattern which uses multiple lines.

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

The example reads as:
* `x, y, z` are siblings
* `x` has a property relator `.` with children `a, b, c`
* `x` has a metadata relator `:` with child `d`
* `z` has a property relator `.` with child `e`

### Inline Literal

Useful to model moderately complex data which is tree-like elements like HTML, CSS, etc.

Technically it's possible to have multiple inline elements on the same line but it comes much harder to understand and thus not recommended.

```
x .a = 1, .b = 2, .c, :d = 3
  y .a, .b, :c = 1
```

or equivalently:
```
x .(a = 1, b = 2, c), :(d = 3)
  y .(a, b), :(c = 1)
```

The example reads as:
* `x` has a property relator `.` with children `a, b, c`
* `x` has a metadata relator `:` with child `d`
* `y` is the child of `x`
* `y` has a property relator `.` with children `a, b`
* `y` has a metadata relator `:` with child `c`

### Chain

Useful to model nested structure as this more easily composes within other expressions.

For example, can used to mimic syntax like `x:Int = 4` which we approximate w/ our current syntax as `x:(type = Int) = 4`

```
x.(a = 1, b = 2, c):(d = 3) = 0
```

The example reads as:
* `x` has a property relator `.` with children `a, b, c`
* `x` has a metadata relator `:` with child `d`
* `x` is bound to value `0`

### Selection

Useful to traverse and select multiple names which are then often used in other patterns.

```
x.(a, b, c):(d)
```

The example reads as:
* under `x` name and then property relator `.` select names `a, b, c`
* under `x` name and then metadata relator `:` select name `d`

This selection is translated to `(x.a, x.b, x.c, x:d)`.

Selection only returned the deepest names of a structure. If you want to pass higher level names (like `x` in the above example) they need to be listed as well. For example `x, x.(a, b, c):(d)` would translate to `(x, x.a, x.b, x.c, x:d)`.

### Destructure

Destructure is a combination of selection on the L-hand and values on the R-hand. The L-hand is interpreted as a sequence of names and the R-hand as a sequence of values.

Useful to model multiple return values, build data in a specific structure, define multiple names at once, handle multiple return values, etc.

```
x.(a, b, c):(d) == 0, 1, 2, 3
```

The example reads as:
* under `x` name and then property relator `.` select names `a, b, c`
* under `x` name and then metadata relator `:` select name `d`
* in order of selected, assign sequence of values `0, 1, 2, 3`

For clarity, this is translates to:
```
(x.a, x.b, x.c, x:d) = (0, 1, 2, 3)
```

### Sequence of Expressions

Useful to model internals of an expression, relator, etc. as it describes multiple names and their values.

Consider this valid, but overly confusing, example:
```
x = 0, y, z.a = 2
```

The example reads as:
* `x, y, z` are siblings
* `x` is bound to value `0`
* `z` has a property relator `.` with child `a`. `a` is bound to value `2`.

This is equivalent to the much more clear:
```
x = 0
y
z.a = 2
```

The inline form is more clear when describing the internal structure of a name. Consider if the above example is a child of relator like:
```
o.(x = 0, y, z.a = 2)
```

This is more clear as the series of expressions describe the properties of `o` and syntactically looks like the attributes of `o`.

### Orthogonal Syntax

The above syntax patterns generally fall into one of two goals:
* Describing internal structure of a name (multiline literal, inline literal, chain)
* Describing multiple names regardless if they belong to a common parent (selection, destructure, sequence of expressions)

The goals are separate so that it's easier to compose data without worrying about the internal structure of a name.

To keep them mostly orthogonal, we introduce a rule which we'll explore with examples below. The rule is: ***if assigning values to names within a parent name, only the parent will be exposed to the outside.***

Here's a simple example:
```
x.(a = 1) = 0
```
If the L-hand expression `x.(a = 1)` returned `x.a` this would result in `x.a = 1` then `x.a = 0`. `x.a` being overwritten on the R-hand value is rarely, if ever, what is desired. Often we want to ignore the internal structure of `x`.

Instead this expands to:
```
x = 0
  .a = 1
```

The previous example may be simple to reason about but it becomes harder to visually track as the syntax becomes more complex:
```
x, y.(a, b:(t = 1), c == 1, 2, 3), z == 4, 5, 6
```

Although not recommended, this syntax is valid. Due to our rule above, we can simplify this somewhat and read it as 2 expressions:
* Ignore the internal structure of `b`
  ```
  a, b:(...), c == 1, 2, 3
  ```
* Ignore the internal structure of `y`
  ```
  x, y.(...), z == 4, 5, 6
  ```
Althought not perfect, ignoring the internal structure makes it easier to read which names are mapped to which values.

If we instead wanted to model using destructuring it is a bit more verbose but at least it's using a flat list which may be preferable in some cases:
```
x, y, y.a, y.b, y.b:t, y.c, z == 4, 5, 1, 2, 1, 3, 6
```

or equivalently (which keeps more of the structure):
```
x, y, y.(a, b, b:t, c), z == 4, 5, 1, 2, 1, 3, 6
```

Even better is representing the data as a multiline literal (which should preferred for complex syntax):
```
x = 4
y = 5
  .a = 1
  .b = 2
    :t = 1
  .c = 3
z = 6
```

### Selection ignores parents

When selection syntax is used, notice that only the bottom children names are selected. Consider the example:
```
x.(a, b:(t, u, v), c)
```

this will expand to:
```
(x.a, x.b:t, x.b:u, x.b:v, x.c)
```

This should be read as a sequence of names `(a, t, u, v, c)` and the paths required to get to them.

You can pass the names which are used for structure they have to be passed as well. Let's edit this example to pass `x` as well:
```
(x, x.(a, b:(t, u, v), c))
```

this will expand to:
```
(x, x.a, x.b:t, x.b:u, x.b:v, x.c)
```

To change this example to focus on data definition instead of data selection; simply assign any name within the structure a value:
```
x.(a, b:(t, u = 1, v), c)
```

This changes the pattern from **selection** to **chain** b/c we're defining internal structure which hides all the interals of `x` until after this expression.

## Comments
------------------------------------------------------------------------------------------------------------

There is only one syntax for comments `//`.

Like most design choices, this syntax serves multiple purposes and is done for consistency across codebases to avoid inconsequential variants.

Comments are data and are parsed. This means they are machine parsable and can be used for generating documentation.

Comments function in multiple ways:
* Comments a line
  ```
  // My header
  div .class = header main
  ```
* Inline comment
  ```
  div
    .class = header //( My header ) main
  ```
  Notice how parentheses follow the pattern of start/stop throughout the syntax including comments
* Comments until the end of a line
  ```
  div 
    .class       = header main // My main header
    .style.width = 100%        // Full width
  ```
* Block comments
  ```
  // My Notes
    Notes both ignore the end of a line and it's children.
    So these are part of the comment as well.
  ```

Comments are data just like the rest of the syntax and makes them machine parsable.

## Escape
------------------------------------------------------------------------------------------------------------

The only escape character is `\`.

It can be used in multiple ways:
* Escapes a single character after it
  ```
  Hello\tWorld\n
  ```
* Including escaping whitespace
  ```
  my\ complex\ name = 1
  ```
* Using parentheses will escape the contents of the parentheses. It can be used to interpolate:
  ```
  name = Jane

  "Hello \(name)!"
  ```
  and expressions:
  ```
  x = 1
  y = 2

  "x + y is \(x + y)"
  ```

## Sugar
------------------------------------------------------------------------------------------------------------

* TODO
  * Anon items
    * and nesting
    * use bullet * or dah - or both?
  * | delimiter
  * complex assignment
    * always reduce to a sequence of expressions
    * eg
      * multi-assign `(x, y, z) = 1, 2, 3`
      * destructure `x, y, z == 1, 2, 3`
      * select `x.(a, b, c) = 1, 2, 3`
  * 
    

## Summary
------------------------------------------------------------------------------------------------------------

This is all the syntax we need to express the base of the Elder syntax which is required for all implementations.

The base syntax is at the level of S-expressions but doesn't really have a concept of literals, types, evaluation, etc. Instead it's all about the structure and requires implementors to have their own concepts if they use this level.

If they want to rely on Elder syntax more and model languages like JSON, YAML, and many others we'll need to add more concepts. The first we use to extend the syntax is literals.
