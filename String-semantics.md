# String semantics

Basic strings within the language, just like source files, are defined to be encoded in UTF-8.
Care has been taken to ensure that the code units for a code point cannot wind up sliced when slicing strings.
There are additional stdlib types for UTF-16 and UTF-32 strings. Filesystem paths are handled by their own type
due to not being representable as a specific flavour of string with a given encoding - filesystem APIs do not
care about what the bytes within a path "string" mean, and generally do absolutely no code point checking or decoding.

## Indexing

Indexing into a string shall yeild a single CodePoint and is equivilant to iterating over the string for the
same number of loops. Negative indexes, or indexes past the end of the string shall result in an invalid CodePoint
being returned from the operation.

## Slicing

Slicing a string works in a similar initial manner to indexing, but results in a `StringView`. The operation is defined
by constructing an interval and truncating to bounds. That is, if a slice `[N:M]` is requested, `N` is first bounds
checked and then `M` adjusted to land not after the end of the string to slice. This is allowed to result in a 0-length
slice - this is to handle when `N` starts out past the end of the source `String`. If `M` would result in an over-long
slice, it shall be adjusted to reference the end of the source string.

The value `N` refers to the starting code point within the string from which to construct the slice, and the value
`M` refers to ending code point using the interval `[N:M)` - that is, `N`-inclusive, `M`-exclusive.

## Conversion

The programmer may freely convert a `StringView` to a `String` (noting this must allocate) by constructive assignment (defining a new variable in scope with type `String` so as to invoke the `StringView` taking constructor for `String`).
The programmer may also freely convert a `String` to a `StringView` when, eg, passing the `String` as a function call
parameter. The `StringView` object acts as a lifetime-bound reference to the originating `String` (or a substring
thereof). It is an error to free-convert a `String` object to a `StringView` such as by passing a `String` result from
one function call to the `StringView` argument of another in an assignment-less chain.

## Helper member functions

## Construction from other encodings

## UTF-16

## UTF-32
