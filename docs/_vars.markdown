
TODO:
* Name oriented
  * keywords here or to come?
  * we shouldn't add special significance to names b/c they will quickly compile?
    * we already have relators (`., /, :`) and comptime `#`
    * can use special suffix for mutations like `x' = x + 2`?
      * this means we lose usefulness of representing multiple compound values (like `f = x'' + 3x' + x`) which may be useful
* Inference
  * Benefits and downsides
  * Complex and mixed values
    * When to default and when to error?
* Abstraction vs Logical vs Memory type?
* Types of constness
  * completely const
  * const subtree
  * const name/symbol
  * const value
    * targeted const far w/n defn
* Other modifiers?
  * eg public, private
  * need a way to relate modifiers to relators and data?
    * modifiers are just sugar for relator data?
* constness compared to comptime known
  * eg could be used like
    ```
    #a = 1          // comptime known name and runtime const. Fully const
    b #= 1          // const symbol reference
    c  = #List Int  // const List
    d  = List #Int  // const entry Int
    e  = #List #Int // const List and entry Int
    ```
  * what about comptime and const if we need to model as well?
  * how do comptime and constness interplay?
    * eg this is invalid `#a = f()` if `f` isn't runnable at comptime

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

## Issues, Edge-Cases, and Quirks
------------------------------------------------------------------------------------------------------------

### Declarations on the Left, Values on the Right

A related concept is that Elder syntax tries to keep declaration info like name, metadata, etc. on the left and the value(s) on the right. Like many languages the left is used for declaration while the right is used for definition.

Elder has a few differences:
* Names are always first on the line after whitespace
* Can declare multiple types of metadata after the name. Often just the type is used.
* The right is automatically inferred. Specifying a type will inform the compiler how to interpret must like a cast.
* This can fail for various reasons. By default, the compiler won't allow you to do something unsafe and requires more syntax to specify you know what you're doing.

To demonstrate, let's vary an example to see the different results:
```
// Using simple inference
x = 1       // Infer to default system Integer represention. Often I32 or I64.
x = Float 1 // Float informs compiler how to interpret value. Infer to default system Floating point representation.

// Using explicit declaration
x:Float = 1                              // Infer to system Integer but type metadata is system Float. x is system Float representation.
x :Float = 1                             // Same as above but optional spacing is sometimes more visually clear.
x:(type = Float) = 1                     // Same as above but explicit type
x :(Float, min = 0, max = 255) = 1       // x is a Floating point w/ boundry conditions.
x .(num-bytes = 8) :(Float, min = 0) = 1 // A extension with more relators. `.` for object-property and `:` for data-metadata.

// Using multiple lines for structure
x = 1
  .offset    = 2
  .num-bytes = 8
  :type      = Float
  :min       = 0

x
  = 1
  .
    offset    = 2
    num-bytes = 8
  :
    type = Float
    min  = 0
```

// TODO: Casts, types of casts, conflicts
