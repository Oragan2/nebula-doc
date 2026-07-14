# Variable Ordering

To maximize both security and memory efficiency, the Nebula compiler does not preserve the order in which you declare variables in your source code. Instead, it analyzes and intelligently reorders variables within each memory scope according to a strict set of rules.

This automatic reordering serves two major purposes:
1. **Prevent Buffer Overflowing**: Placing vulnerable buffers where they cannot overwrite controle data
2. **Eliminating Padding Waste**: Packing variables to minimize the physical memory footprint on the stack or global memory

## Reordering rules

### 1. Scalare Variables(Zone 1)
* **Who** : standard primitive types shuch as int8/16/32/64, uint8/16/32/64, float32/64, enums, ioports and vaddr/paddr
* **Ordering** : they are ordered from smallest to biggest
* **Alignment** : minimal padding is placed to allow for variables are still aligned to their size

### 2. Structs(Zone 2)
* **Who** : the structs declared
* **Ordering** : Placed after the scalares and also sorted from smallest to biggest
* **Other info** : They are internally rearranged from smallest to biggest

### 3. Arrays & Buffers(Zone 3)
* **Who** : the different arrays and buffers
* **Ordering** : Always placed at the far end of the memory block
* **Security benefit** : Arrays that overflows will not spill onto structs or variables, they'll either spill onto empty memory or other scopes (see [Safety Bits](safety-bits.md) for more info)

## Memory reordering exemple

Modern CPU architectures require variables to be aligned to memory addresses that are multiples of their own size (e.g., a 64-bit/8-byte integer must be stored at an address divisible by 8).

Without reordering, the compiler has to insert "dead" padding bytes to satisfy these CPU alignment rules.

### Naive Declaration

Consider this un-optimized layout of alternating 16-bit `uint16` and 64-bit `uint64` variables:

```nebula
uint16 a;
uint64 b;
uint16 c;
uint64 d;
uint16 e;
uint64 f;
uint16 g;
uint64 h;
```
To keep the 64-bit variables aligned on 8-byte boundaries, the compiler must pad every single 16-bit variable up to 8 bytes so 2+6(padding)+8+2+6(padding)+8+2+6(padding)+8+2+6(padding)+8 = 64 bytes

### Reordered layout

The compiler will reorder the variables in this way : 
```nebula
uint16 a;
uint16 c;
uint16 e;
uint16 g;
uint64 b;
uint64 d;
uint64 f;
uint64 h;
```
Because the four 16-bit (2-byte) variables are packed together first, they perfectly combine into exactly 8 bytes (2 + 2 + 2 + 2 = 8) which mean that no padding is needed and so the full size is
2+2+2+2+8+8+8+8 = 40 bytes