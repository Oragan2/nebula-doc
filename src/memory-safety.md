# Memory Safety &amp; Layout

Low-level systems programming shouldn't mean sacrificing safety. Nebula introduces robust compile-time checks and smart memory organization to eliminate entire classes of critical bugs—such as dangling pointers, buffer overflows, and stack tampering—before your kernel even boots.

## Compile-Time scope tracking : 

In C and C++, it is incredibly easy to accidentally store a pointer to a variable that has gone out of scope. When you later try to read or write to that pointer, you get undefined behavior, silent memory corruption, or a sudden crash.

Nebula’s compiler strictly tracks the lifetime of every stack-allocated variable. If you attempt to access a pointer to a variable whose scope has ended, the compiler will refuse to build your code.

```nebula
int32* a;
{
    int32 b = 32;
    a = &b;
    // ...
}
*a = 12; // Compilation error
int32 c = *a; // Compilation error
```

## Memory Layout Safeguards

To protect the stack and vital kernel structures from malicious exploits or accidental out-of-bounds writes, Nebula implements two distinct physical memory safety techniques under the hood:

### 1. [Variable Ordering](variable-ordering.md)

Traditionally, compilers lay out stack variables in the order they were declared, or in whatever order the optimizer chooses. Nebula intelligently reorders variables in memory (for example, placing arrays and buffers above raw scalar variables) to drastically reduce the likelihood that a buffer overflow can overwrite adjacent, critical control data.

### 2. [Safety Bit](safety-bits.md)

Nebula automatically injects lightweight compiler-managed checks—often called "safety bits" or "canaries"—at critical junctions in memory. If a buffer overflow or wild pointer write does occur, these safety bits can detect the corruption of the incoming variables and give an easy way for the developper to either halt CPU or a function to handle the possible corruption