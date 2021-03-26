---
layout: default
title: Base
description: A minimal, data-oriented, and expressive syntax
permalink: syntax
---

The elements in this document form the base of Elder syntax.

Must how S-expressions are separate from any LISP implementation but is the most commonly used syntax; the Elder syntax can be used alone but is the preferred syntax of the Elder language.

## Sequence
------------------------------------------------------------------------------------------------------------

A ***Sequence*** (much like LISP, Rebol) is a core syntactical structure.

In Elder, a sequence is:
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
To write more compact code we have to introduce new syntax. The comma `,`:
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
* each item is a sequence of 2 items

Inline siblings (like `a, b`) are higher precedence than `\n` siblings which allows us a intuitive way to represent a sequence of multiple sequences.
This is consistent b/c we don't allow mixing `,` and `\n` sibling syntaxes for the same siblings.

Multiline precedence is done this way as it follows the natural English reading order of top-to-bottom then left-to-right.
We want our syntax to follow what one would expect so it's easier to predict.

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

2 spaces are used b/c it's the minimal amount to make it clear there's indentation. No tabs, no variable spaces, nothing but 2 spaces per indentation.

This can be nested arbitrarily and the structure matches what you expect as the visible layout matches the code layout:
```
x
  a
  b
    1
    2
    3
  c
```

### Multiple Parents

Combining our syntaxes thus far we are also able to model some simple graphs:
```
a
b, c
  x
  y
  z
d
```

Notice that the syntax is still structurally a tree but the data models a simple graph b/c `b, c` are both the parents of `x, y, z`.

## Equals
------------------------------------------------------------------------------------------------------------

Equals (`=`) is a syntax which captures the pattern of relating ***L-hand*** to a ***R-hand***.

The precise relationship between ***L-hand*** and ***R-hand*** varies with the context it's defined in. Commonly it means definition of mapping a name to a value but it can also mean assignment, key-value entry, among others. The meaning of `=` is made clear by the context it's used in.

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
* the 1st child is `a = 1` where name `a` relates to value `1`
* the 2nd child is `b = 2` where name `b` relates to value `2`
* the 3rd child is `c = 3` where name `c` relates to value `3`

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
  * This is equivalent to `x = (a, b, c)`
  * Often useful when you want to store a literal, multi-valued, container like `List`, `Sequence`, `Map`

## Relator
------------------------------------------------------------------------------------------------------------

Since Elder syntax can be used for various use-cases (from serializing data like JSON to imperative programming like Java and more) it must be able to represent many ways to define, format, and structure data.

For example, consider other languages which are often use hard coded syntax:
* `C++`
  * accessing a `Namespace` uses `::` syntax like `my_namespace::my_val`
  * accessing a `Struct` field uses `.` syntax like `my_stuct.field` or `my_struct.field = 1`
* `Clojure`
  * accessing a `Namespace` uses `/` syntax like `my_namespace/my_val`
  * interop with `Java` or `JavaScript` uses `.` syntax like `(.toUpperCase "jane")`
* `XML`
  * accessing a child element uses `/` syntax like `car/wheel/rim`
  * accessing a attribute uses `.` syntax like `car.weight`

Elder syntax takes a different approach by allowing the developer to create their own syntax in a limited way. Customizing the syntax allows developers to add structure to the syntax without the need of macros.

To accomplish this, we introduce a new syntax we've termed a ***Relator***. Specifically a ***Relator*** is defined as a syntactical tool which describes the ***relation*** between a ***object*** and a ***descriptor***.

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

Although developers can define their own relators, there are 3 relators which must have a common meaning:
* `/` the child relator for parent-child relation like:
  * for HTML, a DOM child element
  * for namespaces, a child namespace
  * for files, a directory to it's contents
* `.` the dot relator for object-property relation like:
  * for OOP (and many other) languages, accessing the fields of a instance
  * for HTML, accessing a attribute of a DOM element
* `:` the meta relator for data-metadata relation
  * used to represent metadata of any data (and since it's homoiconic it can target almost anything)
  * does the work of describing concepts like types, constraints, traits, interfaces, rules, etc.

### Literal
In order to model data without additional syntactical noise, data can be written as literal trees using relators. Like most syntax, there's a few rules:
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
* add `.` relation to `obj` with name `a` then define as `0`
* add `.` relation to `obj` with name `b`
* add `.` relation to `obj` with name `c` then define as `2`

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
* add `.` relation to `obj`
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

### Terse Data Description

Paths also expand how we can define data; often more tersely. Consider an alternative way to define the above example:
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
Notice that there's lots of extra structure to get to the real work of setting `width` and `height` which is the "real work" of this block.

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

Using our existing syntax we can represent this inline as a literal but it's rather noisy and requires manually keeping the structure in our head:
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

Parentheses are only used for representing precedence and the start/stop of something. If want to represent the above inline we would need to use the `/` syntax for a parent-child relator:
```
x = ( /(a, b, c), /(d, e, f), /(j, k, l) )
```

Syntactical sugar will be introduces later which remove more of the visual noise.

### Example - HTML Generation

Parentheses especially help when combining multiple relators to describe more complex structure. Consider the template psuedo code:
```
div .(id = "intro", class = "spashscreen"), :visible-if = "is-first-view"
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

This syntax is much closer to what we see in languages that model trees directly such as: SDLang, YAML, Pug, SASS. To compact the syntax further syntax sugar and macros should be used although they outside the scope of the introduction to the syntax.

### Example - Terse HTML inline CSS

Consider a use-case where we want to model a HTML element w/ inline styles.

Implicitly create `.style` structure as they're necessary steps for valid HTML:
```
// As a multiline literal
my-element
  .style.
    width  = 100%
    height = 20px

// or, as a multiline literal w/ '.' ending
my-element
  .style
    .width  = 100%
    .height = 20px

// or, as a multiline literal w/ fully collapsed context
my-element.style.
  width  = 100%
  height = 20px

// or, as inline literal (notice space between head and relator '.')
my-element .style.width = 100%, .style.height = 20px

// or, as assignment (no space)
my-element.style.width  = 100%
my-element.style.height = 20px

// or, as assignment w/ selection
my-element.style.(width, height) = 100%, 20px
```
These variations are made available to fit various use-cases. The form which best models the data it's describing and terse should be preferred.

## Destructure
------------------------------------------------------------------------------------------------------------

Destructuring is useful as it allows us to deal directly with the structure of data and is generally more terse.

With our current syntax the problem is that `=` is a higher precedence than `,`. This means code like:
```
x, y = 1, 2
```

is intepreted as:
```
x
y = 1
2
```

not:
```
x = 1
y = 2
```

Since destructuring is very common we introduce a new syntax `==` which makes it easier to express with minimal visual noise. This is nearly identical to `=` except that:
* It's relative precedence is less than `,`
  * Note: We don't support a global precedence table like many C-style languages, instead all precedence is either relative or not-defined and you must use `()`
* It groups the L-hand and R-hand into `()`. This means `a, b == x, y` is basically `(a, b) = (x, y)`

Using the new syntax, there's a few ways to make this example parse as a destructure:
* Use `()` to group children together
  ```
  (x, y) = (1, 2)
  ```
  It's really only essential to group the L-hand is the R-hand can be inferred as a sequence. So, in this case, this would work as well:
  ```
  (x, y) = 1, 2
  ```
  It's clear now that we mean we can to assign both values `x, y`.
* Use `==` syntax
  ```
  x, y == 1, 2
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

for clarity, the original expression expands to explicit precedence:
```
(a, ((b, c) = (1, 2))) = (((x, y) = (3, 4)), z)
```

## 6 Common Patterns
------------------------------------------------------------------------------------------------------------

At this point we've introduced all the basic syntax needed to describe the most common syntactical patterns that arise. These aren't exhaustive but they cover most of the common use-cases.

### Multiline Literal

Useful to model complex data it is most explicit and verbose. 

Multiline literals can only be embedded within other multiline literals as it's the only pattern which uses multiple lines.

```
x = 0
  .
    a = 1
    b = 2
    c
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
* select `x.a` which is then bound to value `2`.

This is equivalent to the much more clear:
```
x = 0
y
z.a = 2
```

The inline form is more appropriate when describing the internal structure of a name. Consider if the above example is a child of relator like:
```
o.(x = 0, y, z.a = 2)
```

This is more clear as the series of expressions describe the properties of `o` and syntactically looks like the attributes of `o`.


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

## Anonymous Children
------------------------------------------------------------------------------------------------------------

There are cases where we want to represent a series of elements without a meaningful name.

For example, consider the JavaScript code:
```
let myElems = [
  [ a, [ b, c ], d ],
  [ i, [ j, k ], l ],
  [ w, [ x, y ], z ]
```

With our existing syntax we can model this in a similar way:
```
my-elems =
  a, /( b, c ), d
  i, /( j, k ), l
  w, /( x, y ), z
```

However we can do better as this is starting to get noisy and harder to visually parse.

To do so, we'll introduce a new syntax which allows us to define an anonymous child. Here's the rules:
* The syntax is either `-` or `*` or the child relator `/`
  * At this point it's the developer's choice as to which syntax to use (although they must do so consistently).
* The same syntax must be used consistently for children at the same level
* The syntax must be at the start of a line (ie after whitespace)
* The anonymous item indicator is syntax only (just like parentheses) to inform the developer and compiler

Using this we can expand our previous syntax to better model the structure of the data:
```
my-elems =
  - a, 
    b, c
    d
  - i
    j, k
    l
  - w
    x, y
    z
```

and even more expanding:
```
my-elems =
  - a, 
    * b
      c
    d
  - i
    * j
      k
    l
  - w
    * x
      y
    z
```

Notice that we keep the same syntax for children at the same level. This is enfoced b/c it's much more clear and easier to visually track.

### Layout

To assure the grouping is clear and terse, we require the children of the anonymous item to immediately follow it and align to that position like:
```
my-elems =
  - a
    b
    c
  - x
    y
    z
```

and not:
```
my-elems =
  -
    a
    b
    c
  -
    x
    y
    z
```

### Nesting

Items can be nested arbitrarily as long as they're consistent. Here's one example:
```
my-elems =
  - - a
      b
    - c
  - - i
      j
    - k
  - - w
      x
    - z
```

This is read as:
* a sequence of 3 elements
* each contains a sequence of 2 elements
* the first contains a sequence of 2 elements
* the second constains a sequence of 1 element

This can be rewritten to other forms as well which work well for non-deeply nested structures:
```
my-elems =
  - a,b
    c
  - i, j
    k
  - w, x
    z
```

## Issues, Edge-Cases, and Quirks
------------------------------------------------------------------------------------------------------------

Instead of an unfortunate developer discovering a quirk of the syntax in the middle of their work; we've done our best to discover and make them explicit.

As the syntax grows more complex there will be more quirks which will be summarized at the end of each document.

### Sequence of One

Since Sequences are syntax, not data, a sequence of a single element is just that element.

Sequences represent an ordered, one-to-many syntax container but doesn't represent data itself.

For example, these are equivalent:
```
x.a = 1

x.(a) = 1
```

### Inline Children vs Parentheses

In order to dissambiguate grouping with inline children we use different syntaxes. Consider the example:
```
my-elems =
  a
  b, c
  d
```

Although multiline data definition is often preferred, if we wanted to rewrite this inline you'd expect this to work:
```
my-elems = (a, (b, c), d)
```

However this results in a different grouping where the parentheses are dropped like:
```
my-elems =
  a
  b
  c
  d
```

Instead you'd have to use the parent-child relator like:
```
my-elems = (a, /(b, c), d)
```

This may seem an ackward choice but there are a few reasons this design was chosen:
* In a future document, computation will be introduced. If we use `()` for both there are issues with what is and isn't dropped
  * Consider the differences between the following psuedo-code and how evaluation (which uses parentheses for precedence) compares with parentheses for children:
    ```
    a = (1, (2, 3), 4)
    b = (1 + 2)
    c = (1, 2 + 3, 4)
    d = (1, (2 + 3), 4)
    e = (1, ((2 + 3)), 4)
    f = (1, (fn 2, 3), 4)
    g = (1, ((fn 2, 3) + 4), 5)
    ```
    Although it's possible to make deterministic rules it does grow in complexity quickly and makes it harder to visually parse.
  * This has an added benefit in that dev are free to use parentheses and nest as deeply as they like and know they will be dropped.
* It's not desirable to make computation and data definition orthogonal. Often we want to mix them together without having to do something like create a template and then bind values.
* Parentheses stay somewhat pure in their purpose and usage. They can stay just syntax (not data) and their purpose is to group things together.
* It's somewhat rare to nest inline children when there's many more visually clear ways to declare data.
* It keeps the syntax for inline children `/()` orthogonal to precedence/grouping `()`. The purpose and effect is clear.
* Often inline children will be wrapped as a data-literal (like `List []` or `Map {}`) or some other type of container like a function, Type, etc.
* Parentheses work the way most developers are accustomed for computation.

### Inline Overwrite

Consider the simple example:
```
(x = 0) = 1
```

This is read as:
* Assign `x` to `0`
* Get result of `x = 0` expression
* Assign result to `1`

Elder doesn't allow this syntax and will emit a syntax error.

The reasoning is that code like this will lead to more bugs than use-cases.

Instead to assign multiple values to the same name you need to do so using multiple statements:
```
x = 0
x = 1
```

This makes the intent more clear.

It has the added benefit of a user doesn't have to visually track which names are used multiple times as they can be confident they're not redefined.
Even in complex statements it makes it slighly easier to mentally parse.

### Orthogonal Syntax

Elder syntax, thus far, generally falls into two purposes:
* selecting names within some structure (often to be used to get or set values):
  ```
  x.(a, y.(b, c)) = 1, 2, 3
  ```
* defining data:
  ```
  x.(a = 1, y.(b = 2, c = 3))
  ```

These examples end up with the same data but by slightly different mechanisms.

The former selects names `a` in path `x.` and selects `b, c` in path `x.y.`.
The latter defines names `a` in path `x.` and defines names `b, c` in path `x.y.`.

However, this isn't the extent of how we can use these patterns. For example, if we want to set the values for `x` and `y` we use different syntax.

For updating the former it's straight forward by adding `x` and `y` to selected names:
```
(x.(a, y.(b, c)), x, y) = 0, 1, 2, 3, 4, 5
```

or using destructure which allows us to drop some of the parentheses:
```
x.(a, y.(b, c)), x, y == 0, 1, 2, 3, 4, 5
```

It's less clear how we assign values for `x` and `y` when defining data. Jumping ahead for clarity, the way Elder requires to assign values for `x` and `y` when defining data is:
```
x.(a = 1, y.(b = 2, c = 3) = 5) = 4
```

To explain why, let's start with a simpler example:
```
x.(a = 1) = 0
```

This can be read as steps:
* Assigning a propery `a` to `1` within `x`
* Get the result of the expression `x.(a = 1)`
* Assign the result to `0`.

The key question is what is the result of the expression `x.(a = 1)`? There are a few options:
* `nothing`
* `x.a`
* `x`

If the result is `nothing` there are some downsides:
* `x.(a = 1) = 0` would be a syntax error b/c you can't assign nothing to `0`.
* If we wanted to assign a value to `x` we would need to rework into a few statements like:
  ```
  x = 0
  x.(a = 1)
  ```
  or a selection
  ```
  (x, x.a) = 0, 1
  ```
  or a destructure
  ```
  x, x.a == 0, 1
  ```

If the result is `x.a` the downside is we will be assigning the value to `x.a` twice. This is b/c the statement is equivalent to:
```
x.(a = 1) // The inner expression which may result in `x.a`
x.a = 0   // Assigning a value to the result
```
Redefining values inline is rarely, if ever, what is desired and a likely source of errors.

Instead Elder interprets the result of the expression as `x`. It avoids the issues above and has a few benefits:
* It forces selection syntax and definition syntax to be orthogonal by:
  * Selection syntax ignores the paths used to select the child names
  * Definition syntax ignores the child definitions and selects the parent paths
* Allows us to assign values to every name inline without potential of overwriting any of them
* It composes more clearly
  * Composing selection syntax in definition syntax like:
    ```
    x.(a, b, c == 1, 2, 3) = 0

    // Equivalent to
    (x, x.a, x.b, x.c) = 0, 1, 2, 3
    ```
  * Composing definition syntax in selection syntax like:
    ```
    x.(a = 4), y.(b = 5), z.(c = 6) == 1, 2, 3

    // Equivalent to
    (x, y, z, x.a, x.b, x.c) = 1, 2, 3, 4, 5, 6
    ```
* Definititions can represent arbitrarily nested and each value can be assigned without additional statements
  * eg consider a nested structure like:
    ```
    x.y.(a = 2, b = 3, c = 4)
    ```
    we want to assign values to both `x` and `y` we can do so inline by:
    ```
    x.(y.(a = 2, b = 3, c = 4) = 1) = 0
    ```
    which is equivalent to the selection form:
    ```
    (x, y, x.y.a, x.y.b, x.y.c) = 0, 1, 2, 3, 4
    ```

### Nested Assignment

As the syntax becomes more complex it also becomes harder to visually track the structure:
```
x, y.(a, b:(t = 1), c == 1, 2, 3), z == 4, 5, 6
```

Although not recommended, this syntax is valid.

Our rules above provide a few guarantees:
* No name is overwritten in the same line
* Definition and selection syntax are orthogonal

Using these guarantees we can simplify this somewhat and read it as 2 expressions:
* Ignore the internal structure of `b` b/c it's definition syntax
  ```
  a, b:(...), c == 1, 2, 3
  ```
* Ignore the internal structure of `y` b/c it's definition syntax
  ```
  x, y.(...), z == 4, 5, 6
  ```
Althought not perfect, ignoring the internal structure makes it easier to read which names are mapped to which values.

If we instead wanted to model using destructuring it is a bit more verbose but at least it's using a flat list which may be preferable in some cases:
```
x, y, y.a, y.b, y.b:t, y.c, z == 4, 5, 1, 2, 1, 3, 6
```

or equivalently (which mimics the natural data structure):
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

### Unbalanced Syntax

Nearly all syntax in Elder is terminated at the end of the line.

This allows us to use a start or open character but are not required to close it.

It may feel ackward to use initially but there are some benefits:
* Later on we will be introducing how Elder interprets syntax. The order of interpretation is from left-to-right (like everything else). Unbalanced syntax works naturally with the order of interpretation.
* Closing is only required when we want to terminate inline with more after it. When this isn't the case, we save a few steps causing less syntactical noise.
* The entire syntax uses the offside spacing rules. Unbalanced syntax jives with that decision as can use opening syntax element as a new start of a tree. This matches how we visually structure the code.
* Many syntax constructs have a open/close pattern. Not having to close them all can reduce visual noise and manual checkwork:
  * functions like `sum(1, 2, 3`
  * container literals like `my-map = { x = 1, y = 2, z = 3`
  * string literal like `name = "Billy Bane`
  * types like `my-map = Map(x = 1, y = 2, z = 3`

### Prioritizing Names

When designing a language there are many different conventions that can be selected from. One commonly chosen is to start declarations using modifiers (eg `const`, `let`, `var`) or keywords (eg `volatile`, `auto`, `static`) at the start of a line.

Put simply, we disagree. Names starting the line is easier for understanding the structure, shape, and flow of code. Modifiers, constraints, and other syntax for declarations are significant as well but we've created alternative syntax to describe them.

This choice has a few effects like:
* Names almost always start a line
  * This make data-definition and L-hand assignment consistent which is very common
  * It makes it easier to browse and grok the shape of the data or block the developer is looking at
  * It's easier to align elements into logical units where names are on the left, values are on the right, and how they relate to one another is in the middle (often assignment `=`)
  * The syntax is more regular between data declaration, definition, usage, and description
* We don't have special syntax, modifiers, or operators before or surrounding names. Instead the operators, relators, and values are used to specify these.
  * Generally names and relators describe where, values describe what, and relators describe how.
* Names act like their own namespace where their attributes, metadata, and other details are described within it.
  * Defining a context and then it's details within it will become a common pattern. Names first make this even more consistent.

## Summary
------------------------------------------------------------------------------------------------------------

This is all the syntax we need to express the base of the Elder syntax which is required for all implementations.

The base syntax is comparable to S-expressions and doesn't really have a concept of literals, types, evaluation, etc. instead it's all about the structure and syntax.
