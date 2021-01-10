---
layout: default
title: Basic Types, Literals, and Schema
permalink: basic-types
---

It's useful to describe constraints about a program; arguably the most common way is using Types. Most languages differentiate between concepts like types, interfaces, abstract classes, traits, etc. In Elder, they're all just different ways to describe data using metadata.

The metadata relator `:` allows the developer to define whatever metadata constructs they find useful outside of what's defined within Elder syntax. It's used for both common concepts (like types) but can be extended to more nuanced constructs like constant-ness, capabilities-secure, or even business logic for a certain domain. The goal is to allow the developer to extend their model to whatever they need to solve their problem. Since often this changes for different domains and use-cases, the model must be able to be customized.

This doesn't mean that every type can be represented in every environment. If an implementation doesn't natively support the types there's a few ways to configure how the environment responds:
* When possible, emulate the type
* Default to a more general type
* Throw a compile time error on use

All that Elder syntax guarantees is that if it's defined, it must mean what is specified in Elder syntax standard. Not that it must be defined.

## Reserved and Builtin
------------------------------------------------------------------------------------------------------------

Elder reserves and specifies various types, literals, primitives, and constants as the use is generally so common that defying the conventions would lead to confusion.

Since we're starting with the most common concepts, notice that these are abstractions and don't represent the actual implementation. To keep it simple, more concepts will be introduced incrementally.

### Literals

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
  * Every character after starting quote is it's content
  * The escape character `\` works as expected
  * Terminates
    * Is terminated by closing `"` or at end of line
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
    * Is terminated by dedent
  * eg these are equivalent
    ```
    str = """
      my
      multil
      name

    str = "my\nlong\nname"
    ```
  * TODO: what about?
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

## Types
------------------------------------------------------------------------------------------------------------

Types get special treatement as they're so widely used and useful.

Syntactically a type must start with a alphabetic, upper-case character and each new word of it's name should be upper-case like `MyTypeName`.

### Type Sugar

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
