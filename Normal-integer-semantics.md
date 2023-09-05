Normal integers are defined as the signed integer types Int8, Int16, Int32, and Int64 + their unsigned counterparts UInt8, UInt16, UInt32, and UInt64.
Additionally there are two special intermediate representation types - Int128 and UInt128 - defined to make these semantics work. These additional types may only result from a calculation.

## Overflow/Underflow

These types are defined as automatically sizing up and down to the next type on calculations that may underflow or overflow, maintaining signedness. The programmer may circumvent this by assigning the result back to the original width type, causing the generation of a new value for the type's overflow Bool member that can be checked up until the next mathematical operation. This will be illustrated further below.

## Addition

Addition of two N-bit types results in the generation of a new N*2-bit result. This allows cheap overflow-safe mathematics that scales appropriately. If the addition is between two types of different width, the compiler promotes the addition to the widest type used, preserving sign, and performs the addition at that new width.

The effect of this is illustrated by the following code:

```mangrove
UInt8 a = 10
UInt8 b = 250
auto c = a + b // Generates a UInt16 value of 260

----

Int8 a = -127
Int16 b = 255
Int16 c = a + b /* Promotes a to an Int16, performs the calculation and generates a Int32 value of 128.
    The assignment then truncates this back down to an Int16 (but this cannot be to an Int8, as this truncation rule only allows the stepping down to the next width down)
    and generates c.overflow = false as no overflows occurred. */
```