# Normal integer semantics

Normal integers are defined as the signed integer types Int8, Int16, Int32, and Int64 + their unsigned counterparts UInt8, UInt16, UInt32, and UInt64.

Additionally there are two special intermediate representation types - Int128 and UInt128 - defined to make these semantics work. These additional types may only result from a calculation.

## Overflow/Underflow

These types are defined as automatically sizing up and down to the next type on calculations that may underflow or overflow, maintaining signedness.
The programmer may circumvent this by assigning the result back to the original width type, causing the generation of a new value for the type's
overflow `Bool` member that can be checked up until the next mathematical operation. This will be illustrated further below.

## Addition and Subtraction

Addition or subtraction of two N-bit types results in the generation of a new N*2-bit result.
This allows cheap overflow-safe mathematics that scales appropriately.
If the addition is between two types of different width, the compiler promotes the addition to the widest type used,
preserving sign, and performs the addition at that new width.

The effect of this is illustrated by the following code:

```mangrove
UInt8 a = 10
UInt8 b = 250
auto c = a + b // Generates a UInt16 value of 260

----

Int8 a = -127
Int16 b = 255
Int16 c = a + b /* Promotes a to an Int16, performs the calculation and generates a Int32 value of 128.
    The assignment then truncates this back down to an Int16 (but this cannot be to an Int8, as this
    truncation rule only allows the stepping down to the next width down)
    and generates c.overflow = false as no overflows occurred. */
```

To keep things manageable, chained operations do not further widen the intermediary unless the type of one of the
other operands is wider itself. The compiler must maintain the overflow state of the intermediary in case any overflows
do occur so they remain detectable. This is done so calculations such as allocation size requests can be implemented
in a safe manner. This may mean that, even when assigning to a wide enough result variable, the overflow `Bool` may
be set if the stack of intermediary calculations does result in an overflow somewhere.
If the programmer ignores this, the compiler is allowed to optimise out the overflow tracking.

Operations across type signedness first promotes the unsigned type to its next largest signed type, to preserve sign
and magnitude, and the calculation proceeds as before. If an intermediary or result type ends up as Int128 or UInt128,
then the normal promotion to wider types no longer occurs as this is already the widest type in the system. In this
instance all the other rules apply and we do our best to keep the calculation correct. Programs may not use these
special intermediary types to store (in classes) or return results and must narrow the result to one of the 64-bit
integer types. They exist purely for the purposes of correct handling of computational intermediates.

## Multiplication

Multiplication follows the same widening rules as addition and subtraction, and all the same intermediate result rules.
It is intended to be as safe as possible within the bounds of sanity.

On narrowing due to truncating assignment, the overflow flag is generated from checking the high half of the
result for being non-zero combined with the existing overflow flag of the intermediary.

## Division

The result of division is the same width as the input numerator, as the operation can only either narrow or stay
the same width dependant on the denominator. This rule is however different for divisions involving an unsigned
numerator and a signed denominator as this may change the sign of the result so must widen to the next widest
signed type from the numerator to represent the result.

This considers the effect of this example:

```mangrove
UInt8 a = 255
auto b = a / -1
```

In this instance the answer must be -255 and so must be represented by a Int16 to preserve correctness. If the
result is instead assigned back to an Int8 (it would be illegal to assign it back to a UInt8), truncation to -128 would
occur and b.overflow set to true. This allows the detection of this occuring. If the programmer wishes to convert
this back to a UInt8 ignoring sign, they may instead call the member function .asUnsigned() and truncate. This would
result in the round-trip value of 1 as this is done assuming twos complement maths. Without truncation the user would
see the answer 65281 (0xff01) in a UInt16.
