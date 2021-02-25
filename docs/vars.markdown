---
layout: default
title: Variables
permalink: vars
---

// TODO: Intro

## Declaration
------------------------------------------------------------------------------------------------------------

## Inference
------------------------------------------------------------------------------------------------------------

To minimize visual noise, type inference is available.

//     * Numeric
//       * `Number` represents any number
//       * `Integral` represents any integer
//       * `Decimal` represents any floating point
//       * `Ratio` represents any fixed point
//     * Container
//       * `List` represents a ordered series of items
//       * `Set` represents a unique series of items
//       * `Map` represents a series of key-value pairs
//       * `String` represents a series of characters
//       * `Optional` represents a value which may not be present

Here's some simple examples
```
a = 1     // infers as default Integral implementation
b = 2.0   // infers as default Decimal implementation
c = 3 / 4 // infers as default Ratio implementation
```


```
let zoo = [new Rhino(), new Elephant(), new Snake()];           // let zoo: (Rhino | Elephant | Snake)[]
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()]; // let zoo: Animal[]
```

### Inference Issues

Type inference can make code more terse and readable but in some cases it can make the code less readable and have unexpected results. 

In cases where Elder isn't able to infer in a decidable way, there are a few ways to direct how it should react including:
* Find a common subtype
* Use conjunction type
* Throw a compile time error

### Inference Failure

TODO
* How to differentiate between using `Any` and failing at compiletime?
  * Based on environment? based on what trying to represent? must specify?
    * eg JavaScript just assumes all is `Object` even if actually all `Integer`? or emulate `TypeScript`?
* Counterpoint
  ```
  var fieldName = "BuildingCode";                                                       // Easy.
  var entries = new Dictionary<String, String>();                                       // Easy.
  var currentID = searchLog(fieldName)[0];                                              // I can make a good guess.
  var site = repo.GetSites().Where(x => x.SiteType == SiteTypes.Server);                // I can make a decent guess.
  var pdfMap = new Lazy<T>( () => { MapEngine.CreateMap(this.x, this.y, this.zoom) } ); // Not what I expected.
  ```

mixed-list = [
  -1
  1
  2.3 
  "my-string"

### Complex Declaration

## Schema
------------------------------------------------------------------------------------------------------------

TODO:
* How to make Schemas and Type definitions jive so that more information is a natural extension
  * not only need type but also needs identifier for name
  * 
* 

InternetObject:
```
name, age:{int, min:20 }, address:{street, city, state}, active?:bool, tags?:[string]
---
~ Spiderman, 25, {Bond Street, New York, NY}, T, [agile, swift]
~ Ironman, 48, {Malibu Point 10880, Malibu, CA}
```
* Notice how the schema specifies the names w/ optional children names or types
  * looks like if it's not a reserved type (eg `int`) it's assumed to be a name
* 

Elder:
```
Entry = Schema
  name:String
  age:(Int, min:20)
  address
    street:String
    city:String
    state:String
  active:Optional(Bool)
  tags:Optional([String])

// This doesn't work b/c /() is a child when they're actually properties!
entries = [ Entry
  - Spiderman, 25, /(Bond Street, New York, NY), T, [agile, swift]
  - Spiderman, 25, .(Bond Street, New York, NY), T, [agile, swift]
  - Spiderman, 25, _/(Bond Street, New York, NY), T, [agile, swift]
  - Spiderman, 25, _.(Bond Street, New York, NY), T, [agile, swift]
  - Ironman, 48, /(Malibu Point 10880, Malibu, CA)
```

```
Table
  - name
    age:(Int, min = 20)
    address/(street, city, state)
    active:Optional(Bool)
    tag:Optional([String])
  - Spiderman, 25, /(Bond Street, New York, NY), T, [agile, swift]
  - Ironman, 48, /(Malibu Point 10880, Malibu, CA)
```
