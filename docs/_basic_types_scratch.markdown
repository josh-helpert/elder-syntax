TODO:
* Not just types but also type-like: interafce, traits, duck, nominal, etc.
* built-in
  * start w/ these?
  * how to deal w/ not representable in runtime
  * not always
* logical vs storage
  * defaults to logical which is sometimes storage
  * logical are what we treat something as
  * storage is what it's actual memory is
  * maybe others as well like aggregate which is a layer between logical and actual memory b/c we often want to pool or statically allocate or whatever...
* Copy good ideas from Julia, Zig, Ada/Agda, ...
* Implicit casts?
* Support coefficients?
  * eg 4x, 3 + 5i, ...
* 

Reserved TODO:
* symbol literals
* singletons and universal
* names
* meta
* types, traits, interfaces, ...


## Reserved and Builtin
------------------------------------------------------------------------------------------------------------

### Primitives

Primitives are directly represented by the environment they run on. Often this is LLVM but can also be another target like JavaScript, Java, the HTML DOM, etc.

Additionally, higher level types are often built using these primitives.

* Types
  * Numeric
    * Integral
      * `Int` is a environment default signed, integer
      * `UInt` is a environment default unsigned, integer
      * `I8, I16, I32, I64, I128` are sized, signed integers
      * `U8, U16, U32, U64, U128` are sized, unsigned integers
      * `IPtr-Size` works like `intptr_t`
      * `UPtr-Size` works like `uintptr_t`
    * Decimal or Floating Point
      * `Float` is a environment default floating point
      * `F8, F16, F32, F64` are sized, signed floating point
    * Ratio or Fixed Point
  * Logic
    * `Bool` is a environment default boolean
* Values
  * Constants
    * `Pi` represents mathematical constant
  * Logic
    * Only possible values of type `Bool` are `True` or `False`
  * Reference
    * `Null` is used to set optional value to not present
    * `Undefined` is value of variable if unspecified
      * For native environments this often means it points to garbage memory
  * Limits and Errors
    * `Inf` represents infinity
    * `-Inf` represents negative infinity
    * `DivByZero` represents divide by zero error

### Builtin Types

Each types has different aspects like:
* which features each offers
* what properties each have
* how the implementation details of each differ
* which each guarantees
* rules of use (input, output, invariants)
* what constraints are required and provided

* Interface
  * List
  * Map
  * 
* Type
  * ADT
  * String
  * Struct/Record/Tuple
  * And/Or Type
  * Optional/Maybe
  * 
* Maybe?
  * Atom
  * Enum
  * Unique
  * Type heirarchy
  * 

* Maybe?
  * Struct (finite and heterogeneous?)
  * TaggedUnion
  * 

### Types

There are reserved types names which are used to represent common types. They are reserved to assure they have a common meaning across implementations.


TODO: Consider meta name for including 0 or not as is a common pattern across different sets?
* Positive
* Negative
* Positive union Zero
* Negative union Zero

* Numeric
  * Sets
    * Integral
      * Negative
      * Natural
        * Positive
    * Decimal/Floating Point
    * Ratio/Fixed Point
    * Complex
  * Modifiers
    * Positive/Negative
    * Real/Imaginary
* Constants
  * Zero
  * pi
  * e (Euler's constant)
  * Inf, -Inf
  * 
* Singleton
* Meta
  * Ident
  * Empty
  * Noop
  * Min/Max
  * Inc
  * 
* Container
  * List (Array, Set, LinkedList, ...)
  * Map (HashMap, Dictionary, HashTable, ...)
  * Table
  * Tagged Union
  * ADT
  * Or/And Types
  * String
  * Block
  * Expressions
  * Escape
  * Record/Struct/Tuple
  * Optional, Maybe
  * Inductive
  * 

TODO:
* Reserve heirachy
* Explain how to handle if not implemented
* 

### Type Sugar

It's still possible to use something that looks like a type as a key under the metadata relator you just have to make clear it's a key:
```
// Is translated to x:(type = Integral) = 4 like you expect
x:Integral = 4

// Is acceptable but not preferred as usually key names are lowercase
x:(Min = 0, Max = 10) = 4

// Is translated to x:(type = Integral, Min = 0, Max = 10) = 4
x:(Integral, Min = 0, Max = 10) = 4
```

### Parameterized Types

```
// TypeScript
function loggingIdentity<T> (arg: Array<T>): Array<T>
{
  console.log(arg.length); // Array has a .length, so no more error
  return arg;
}

// Elder
// - Can't be so ad-hoc as to be meaningless? must be guessable
loggingIdentity = Fn(T:Type)         arg:Array(T) -> Array(T)
loggingIdentity = Fn:(generic = (T)) arg:Array(T) -> Array(T)
  console.log(arg.length);
  arg
```

```
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

```
GenericNumber = Type(
```

## Types are a Interpretation Context (In Progress)
------------------------------------------------------------------------------------------------------------

Since everything in Elder syntax is some type of sequence, there needs to be a way to determine how a sequence is interpreted. Files, Types, Blocks, along with many other constructs define the default way their children (which comprise a tree) are interpreted. For convenience, we term the default way of interpreting a tree as a new interpretation context.

A simple example is the difference between the interpretation between a `List` and a `Map`. Consider the block which will be the body of both containers:
```
a = 1
b
c = 3
```
By itself, this is interpreted as a sequence of 3 items `a, b, c` with `a` assigned to `1` and `c` assigned to `3`.

When used within a type it provides context to how interpret the sequence:
```
my-list = [
  a = 1
  b
  c = 3

my-map = {
  a = 1
  b
  c = 3
```

JS-like?
```
let myList = [ 1, null, 3 ];

let myMap = { a: 1, b: null, c: 3 };
```

My like before, `my-list` interprets the body as a list of 3 items. The only difference is it's stored within a shared data container of a `List`.
However, the `my-map` interprets the body as 3 items each of which is a key-value entry.

## Parentheses Reduction (In Progress)
------------------------------------------------------------------------------------------------------------

notes:
* parentheses reduce if a single item
* multiple sets of parentheses, w/o additional data, will always reduce to a single set
  * can feel free to add additional parentheses in order to assure that whatever is inside is wrapped as a sequence
* 

group: precedence or siblings

Parentheses are used for multiple purposes:
* they describe the start and stop
* 

Parentheses are used for multiple purposes which are generally divided into 2 syntax patterns:
* describing the start and stop
* 

## Literals
------------------------------------------------------------------------------------------------------------

Literals represent the most common data and types used by many languages which:
* are the default interpretation of data
  * The interpretation can change in different contexts like specific file types, within a Type, etc.
* are sugar which must delegate to a specific implementation
  * For example, a list literal may be implemented as LinkedList, ArrayList, C-like array, or many other choices

TODO:
* Different interpretation based on count?
  * eg arity of 1, 2, 3, ... of chars:
    * `[`
    * `{`
    * `|`
    * `"`
      * `"` inline
        * what about if EOL reached?
      * `""` operator like? ignore single leading space until first chars
        * eg `"" Some sample words` becomes `"Some sample words"`
      * `"""` block
    * `` ` ``
* Unbalaned which terminate at end of line or allow children
  * Valid for containers
    * Including parens?
  * 
* Empty concepts are stored as a meta concept which adapts to each type?
  * eg `EMPTY` identifier is type specific? for `[` it's `[]` while for `{` it's `{}` while for `"` it's empty string?
  * really concepts like EMPTY
* 

Literals are divided into a few major categories:
* Constants
  * Zero is all of these `ZERO, 0, -0`
  * PI
* Meta ???
  * IDENT ???
    * Type and operator specific?
  * NONE or EMPTY ???
    * Type and operator specific?
  * NIL ???
    * Type and operator specific?
* Error/Invalid?
  * `-0`
  * `--#`
  * `# / 0`
* Primitives
  * Numeric contains all the differerent types of number literals
    * Consistent across types
      * Positive and negative are differentiated by a `-` in front of the numeric value.
      * Commas are allowed to indicate large numbers as long as no spaces like `1000000` and `1,000,000` are read as 1 million
    * Categories (only positive examples for clarity)
      * Integer like `0,  3, 1024, 1,000,000`
      * Floating Point like `0.25, 7.18, 4.5182155354352`
      * Fixed Point
        * TODO: range + (delta or digits)
      * Ratio like `3 / 8`, `9 / 2', '1027 / 982`, `3.289 / 7.12465`
        * Ratios are not automatically reduced
        * Decimal ratios will be made into Integer values like `3.289 / 7.12465` becomes `328900 / 712465`
      * Radix
      * Scientific
        * TODO: 10e6
      * 
* Containers
  * List `[]` interprets it's children as series of entries
    * `[ a = 1, b = 2, c = 3 ]` is interpreted as 3 entries of expressions
  * Map `{}` interprets it's children as a series of entries which contain a key and optional value
    * `{a = 1, b = 2, c = 3}` is interpreted as 3 entries of expressions of keys `(a, b, c)` and values `(1, 2, 3)`
    * ??? names w/o values are interpreted as just keys? w/ what value? Depends on type and DSL???
  * Inline String `"` interprets it's content as a series of characters
    * `"a = 1, b = 2, c = 3"` is interpreted as a series of characters
    * Doesn't span multiple lines
  * Inline code `` ` `` interprets it's content as unevaluated code
    * `` `a = 1, b = 2, c = 3` `` is interpreted as unevaluated code of a sequence `(a, b, c)`
    * Doesn't span multiple lines
  * Block String `"""` interprets it's children as a String separated by `\n`
    * The leading whitespace to the first child is ignored as leading space for each child to keep the formatting clean
    * Consider the block String:
      ```
      """
        a = 1
        b = 2
        c = 3
      ```
      this is equivalent to the inline form:
      ```
      "a = 1\nb = 2\nc = 3"
      ```
      Notice that the elements are separated by newlines and the leading space for each child is ignored.
  * Block code ```` ``` ```` interprets it's children as unevaluated code

## Types
------------------------------------------------------------------------------------------------------------

### Refinement

Refinements has been carried over from Rebol and is idiomatic in Elder syntax. Refinements reuse identifiers not only to refer to the value itself but acts as a namespace.

Consider a heirarchy for describing positive, natural numbers:
```
Number         // All numbers
  Integral     // Whole numbers
    Natural    // Positive whole numbers including 0
      Positive // Positive whole numbers
```

Here all types `Numeric`, `Integral`, `Natural`, and `Positive` are all valid types and it's common to use them separately or in order to disambiguate using as a refinement:
```
a:Numeric/Integral/Natural/Positive = 4
b:Positive                          = 4
c:Positive                          = 4.0
d                                   = 4
```
A few notes:
* `a` is fully qualified 
* `b` is inferred as both `Positive` from the type and the value is `Int`. Combining both it is decidable as the same types as `a:type`
* `c` is inferred as both `Positive` from the type and the value is `Float`. Combining both it is decidable as the type is `Numeric/Float/Positive`
* `d` doesn't have a type specifier but it's value is `Int`. Since we don't have any more information it's type is as inferred as `Int`

### Type Input

Some types accept input to further specify the data they represent.

A simple example is using the `List` type, consider the difference between:
```
List          1, 2, 3
List Integral 1, 2, 3
List Decimal  1, 2, 3
List Ratio    1, 2, 3
List String   1, 2, 3
List Type     Integral, Decimal, Ratio
```
Notes:
* Read from L -> R so that we can nest functions and will invoke to right?
  * Like Rebol does and then has tools for changing grouping?
  * eg f g String 4 === f(g(String 4))
  * or really pattern matches on f input? based on it's arity and type can dispatch appropriately?
  * it must be decidable and predictable!!!
* Just error on non-deterministic? if we can't decide we error?
* 

TODO: How to represent a list of types which would be used to specify?
* Related to homogeneous and heterogeneous?
  * Should we just allow for both and then have different data structures?
  * Or have different specifications which infer what's expected?
* List(Type) Integral, Decimal, Ratio, String
* List Type Integral, Decimal, Ratio, String
* List (Type) (Integral, Decimal, Ratio, String)

### Complex Declarations

TODO:
* Schema is after the equals?
  * and the body/children is the contents
* if all inline, then needs way to determine type vs value vs infer
  * all needs to be clear which are type arguments and which is body
    * eg List of Integers
      ```
      List Integer 1, 2, 3

      List(Integer) 1, 2, 3

      List(Integer) (1, 2, 3)

      List Integer /(1, 2, 3)
      ```


my-string-list = [
my-string-list = List
my-string-list = [ String
my-string-list = List String

my-map-list = [
my-map-list = List
my-map-list = [ {
my-map-list = List Map
my-map-list = [ { String, Int
my-map-list = List Map String, Int

my-table = [ [ Int

## Defaulting
------------------------------------------------------------------------------------------------------------

A concept that has been hinted at, but not yet explained, is how default choices are made. For example, consider which type of list is selected:
```
my-list = [ 1, 2, 3]
```

Depending on the environment and context this runs in determines which type of list implementation is used like:
* If targeting a serialization language like JSON then selecting a implementation that is similar to the JSON list literal makes sense
* If targeting the JVM runtime then selecting an implementation that is similar to `java.util.ArrayList` makes sense
* If targeting the JavaScript runtime then selecting an implementation that is similar to the JavaScript builtin `Array` makes sense

Defaulting is used to:
* Reduce the syntactical noise
* Like the philosophy of ***progressive disclosure*** with UI/UX design, hide unncessary complexity until it's needed for more rare, complex, and nuanced choices are needed
* Allow the developers to specify the most sensible choices for a given environment. Often this means different defaults for different environments.

### Inference 

Although this is used throughout Elder, as it relates to types this is often how type inference is done.
