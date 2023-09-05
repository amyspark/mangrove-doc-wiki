# Modulo integer semantics

Modulo integers are defined as the signed integer types IntMod8, IntMod16, IntMod32, and IntMod64 +
their unsigned counterparts UIntMod8, UIntMod16, UIntMod32, and UIntMod64.

## Overflow/Underflow

These types are defined to wrap when an under or overflow would occur, giving a continuous looping value space such
as needed for cryptography. These types never implicitly promote or change width unless involved in a calculation
that had a wider type involved, resulting in an answer the width of the widest type.

## Arithmetic

Arithmetic (addition, subtraction, multiplication, and division) results in the same type for the calculation as
the input types. If a calculation is to be performed that would be cross-signedness (that is, an operation between
a signed and unsigned number), the unsigned operand is reinterpreted as if signed, and the result is signed.
This will not widen the operation.

## Bitwise operations

Bitwise operations are defined for both signed and unsigned types and are defined as converting signed numbers
by reinterpetation to their unsigned counterparts, and producing unsigned results. Widening if required is done
before the reinterpretation step, and only occurs when one operand is wider than another.

## Value reinterpretation

The modulo integer types can be reinterpreted between their signed and unsigned represntations by use of the
.toUnsigned() (for signed), and .toSigned() (for unsigned) member functions. These reinterpret the bits and
result in a value of the opposite signedness. That is, an IntMod16 becomes a UIntMod16 through .toUnsigned(),
and a UIntMod64 becomes an IntMod64 through .toSigned().

If the programmer wishes to convert a value to an ordinary integer, they may call the .toInt() member function to
get a normal integer of the same width and signedness.
