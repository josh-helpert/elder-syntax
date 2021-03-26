---
layout: default
title: Basic Types and Literals
permalink: basic-types
---

It's useful to describe constraints about a program; arguably the most common way is using Types. Most languages differentiate between concepts like types, interfaces, abstract classes, traits, etc. In Elder, they're all just different ways to describe data using metadata.

The metadata relator `:` allows the developer to define whatever metadata constructs they find useful outside of what's defined within Elder syntax. It's used for both common concepts (like types) but can be extended to more nuanced constructs like constant-ness, object-capability, concurrency, or even business logic for a certain domain. Elder prefers that the problem decides the appropriate model instead of the reverse. This means Elder must allow the developer to extend their model to whatever they need to solve their problem. Since often this changes for different domains and use-cases, the model must be able to be customized.

For now, we'll start with the basics and leave the more complex type-like concepts for when we fully introduce compile time.

## Reserved and Builtin
------------------------------------------------------------------------------------------------------------

Elder reserves and specifies various types, literals, primitives, and constants as the use is generally so common that defying the conventions would lead to confusion.

Since we're starting with the most common concepts, notice that these are abstractions and don't represent the actual implementation. To keep it simple, more concepts will be introduced incrementally.

Not every type can be represented in every environment. If an implementation doesn't natively support the types there's a few ways to configure how the environment responds:
* When possible, emulate the type
* Default to a more general type
* Throw a compile time error on use

All that Elder syntax guarantees is that if it's defined, it must mean what is specified in Elder syntax standard. Not that it must be defined. For example, `I32` (a 32 bit integer) doesn't have a native representation in JavaScript VM but it can be emulated using a bit of extra code the compiler can generate, default to a JavaScript builtin `Number`, or throw a comptime error on use.

### Basic Types

Although there are many builtin types, let's start with the most common:
* Numeric
  * `Number` represents any number
  * `Integral` represents any integer
  * `Decimal` represents any floating point
  * `Ratio` represents any fixed point
* Container
  * `List` represents a ordered series of items
  * `Set` represents a unique series of items
  * `Map` represents a series of key-value pairs
  * `String` represents a series of characters
  * `Optional` represents a value which may not be present
* Logic
  * `Bool` only can be values `True` or `False`

### Literals Types

We follow the conventions of many languages for the most basic literals. Each literal uses the default implementation for the environment it's running in.

The most common are:
* `[` is a `List` literal
  * Often the default implementation is a `Array`
  * Each sequence element is interpreted as a item of the list
  * Terminates
    * If inline, is terminated by closing `]` or at end of line
    * If items are multiline, is implicity terminated at dedent
  * eg these are equivalent
    ```
    list = [ 1, 2, 3 ]

    list = [ 1, 2, 3

    list = [
      1
      2
      3
    ```
* `{` is a `Map` literal
  * Often the default implementation is a `HashMap`
  * Each sequence element is interpreted as a item of the list
    * Key and value are separated by `=`
    * If only key is present then ???
  * Terminates
    * If inline, is terminated by closing `}` or at end of line
    * If items are multiline, is implicity terminated at dedent
  * eg these are equivalent
    ```
    map = { a = 1, b = 2, c = 3 }

    map = { a = 1, b = 2, c = 3

    map = {
      a = 1
      b = 2
      c = 3
    ```
* `"` is a inline `String`
  * Every character after starting quote is it's content including whitespace
  * The escape character `\` works as expected
  * Terminates
    * Since inline, is terminated by closing `"` or at end of line
  * eg these are equivalent
    ```
    str = "my long name"

    str = "my long name
    ```
  * edge-cases
    * sequence of string?
      ```
      str =
        "my"
        "long"
        "name"
      ```
    * concat? w/ ` ` or `\n`
      ```
      str = "
        my
        long
        name
      ```
      * concat w/ `` (empty)
        ```
        str = "mylongname"
        ```
      * concat w/ ` ` (space)
        ```
        str = "my long name"
        ```
      * concat w/ `\n` (newline)
        ```
        str = "my\nlong\nname"
        ```
* `"""` is a block `String`
  * The characters of it's content ignore the leading space of a 2-space indent
  * The escape character `\` works as expected
  * Terminates
    * Since it multiline, is terminated by dedent
  * eg these are equivalent
    ```
    str = """
      my
      multil
      name

    str = "my\nlong\nname"
    ```
  * edge-cases?
    ```
    str = 
      """
        my
        multi
        name
      """
        my
        other
        name
    ```

### Literal Values

By default there are values which are interpreted as known types to avoid unnecessary visual noise like:
* Numeric
  * Decimal  `1.0`
  * Integral `4`
  * Ratio    `3/4`
  * Hex      `0xF00D`
  * Binary   `0b1010`
  * Octal    `0o744`
* Logic
  * True for true, False for false
  * Notice these looks like Types and will be explained later once comptime is introduced

## Types
------------------------------------------------------------------------------------------------------------

Syntactically a type must start with a alphabetic, upper-case character and each new word of it's name should be upper-case like `MyTypeName`.

### Type Sugar

Types get special treatement as they're so widely used and useful.

Lets start simple with an example:
```
x:(type = Integral) = 4
```

Notice that `x`'s type is literally represented by the key `type` under the metadata relator `:`. The key `:type`, among others, has a reserved meaning in Elder syntax to mean the logical type of the name it's describing.

Since defining types is so common, we support sugar to minimize the syntactical noise:
```
x:Integral = 4
```
This only works for identifiers which can be interpreted as a types (meaning starting with upper-case alphabetic character) and is under the metadata relator `:`.

It's still possible to add more metadata:
```
x:(Integral, min = 0, max = 10) = 4
```

or the equivalent expanded form:
```
x:(type = Integral, min = 0, max = 10) = 4
```

It is a syntax error to set both the `type` and use the shorthand together like:
```
x:(Integral, type = Decimal) = 4
```

## Emulate Serialization Languages
------------------------------------------------------------------------------------------------------------

Using the syntax so far we have enough to emulate various serialization languages.

JSON is ubiquitous so let's see the difference.

Consider a somewhat simple JSON example:
```
[
  {
    "department": "History",
    "staff": [
      {
        "name": "Jane Janison",
        "is-tenured": true
      },
      {
        "name": "Billy Bane",
      },
      {
        "name": "Katie Kattson",
        "is-assistant": true
      },
    ]
  },
  {
    "department": "Mathematics",
    "staff": [],
  },
  {
    "department": "Biology",
    "staff": [
      {
        "name": "Dirk Darwin",
        "specialization": "Evolutionary",
        "teacher-assistants": [
        ]
      },
      {
        "name": "Ellen Erickson",
        "teacher-assistants": [
          {
            "name": "John Johnson"
          }
        ]
      },
    ],
  },
```

We can recreate with Elder:
```
[
  - department = History
    staff = [
      - name         = Jane Janison
        is-tenured   = True
      - name         = Billy Bane
      - name         = Katie Kattson
        is-assistant = True
  - department = Mathematics
    staff      = []
  - department = Biology
    staff = [
      - name               = Dirk Darwin
        specialization     = Evolutionary
        teacher-assistants = []
      - name = Ellen Erickson
        teacher-assistants = [
          name = John Johnson
```

It's not perfect as there's still some repetition in the shape and structure of the data. Later on it can be simplified further.
