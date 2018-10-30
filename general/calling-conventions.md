# Calling Convention Quick Reference

## i386

i386 uses the stack to pass all arguments. The arguments can be found relative starting at 8($ebp). (The old $ebp and return value are stored at $ebp and 4($ebp), respectively.)

## ARMv6

General Purpose Registers: r0-r3

In general, the first four scalar arguments occupy the next available general-purpose register and any remaining arguments are passed on the stack.

## ARM 64-bit

General Purpose Registers: x0-x7  
SIMD and Floating-Point Registers: v0-v7

Floating point arguments occupy the next SIMD and floating-point register if one is available; otherwise, they are allocated on the stack (note that half and single precision floating point types occupy 8 bytes on the stack).  
Integral and pointer types of size 8 bytes or less occupy the next general-purpose register if one is available; otherwise they are allocated on the stack.

## x86-64

General Purpose Registers: %rdi, %rsi, %rdx, %rcx, %r8 and %r9  
Floating-Point Registers: %xmm0 to %xmm7  
Integer Return Value Registers: %rax, %rdx  
Floating-Point Return Value Registers: %xmm0, %xmm1

Floating point arguments, except those of type long double, occupy the next floating-point register if one is available; otherwise, they are allocated on the stack.  
Integral and pointers types of size 8 bytes or less occupy the next general-purpose register if one is available; otherwise they are allocated on the stack.
