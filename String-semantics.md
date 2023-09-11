# String semantics

Basic strings within the language, just like source files, are defined to be encoded in UTF-8.
Care has been taken to ensure that the code units for a code point cannot wind up sliced when slicing strings.
There are additional stdlib types for UTF-16 and UTF-32 strings. Filesystem paths are handled by their own type
due to not being representable as a specific flavour of string with a given encoding - filesystem APIs do not
care about what the bytes within a path "string" mean, and generally do absolutely no code point checking or decoding.

## Indexing

## Slicing

## Conversion

## Helper member functions

## Construction from other encodings

## UTF-16

## UTF-32
