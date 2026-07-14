# Memory Access

In bare-metal systems programming, communicating with hardware and managing system resources requires direct access to physical memory, virtual memory, and I/O devices.

Nebula splits hardware communication into two distinct pathways depending on how the target peripheral is wired to the CPU:

## 1. Memory-Mapped Access (`vaddr`&`paddr`)

Most modern hardware devices and system tables (like the ACPI tables, framebuffers, or APIC interrupt controllers) are mapped directly into the CPU's standard memory address space.
* You access these using the specialized vaddr (virtual address) and paddr (physical address) types.
* Read and write operations on these addresses translate to standard assembly instructions (like mov).

## 2. Port-Mapped Access (`ioport`)

Legacy and core motherboard peripherals (such as the system timer, keyboard controller, and serial ports) often reside in a completely separate, dedicated 16-bit address space.
* You access these using the specialized ioport type.
* These bypass standard memory operations entirely and require dedicated CPU instructions (such as in and out).

## Chapter Directory

- [**Memory Operations**](memory-operations.md) this chapter will cover everything from what ways there is to access memory, how they work and how the compiler treats them
- [**I/O operations**](io-operations.md) this chapter will cover how the I/O operations are handled and how the compiler treats them