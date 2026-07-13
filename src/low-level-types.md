# Low-Level Types

Because Nebula is built from the ground up for bare-metal applications and kernel development, it introduces several specialized built-in types. These types are specifically designed to catch hardware-level logic errors at compile-time and make direct memory manipulation highly readable.
In this chapter, we will explore these low-level types, unpacking their unique properties, hardware use-cases, and how they function under the hood.

## In this chapter:
- [Addresses](addresses.md)(vaddr & paddr) -- Safely separating virtual and physical memory.
- [I/OPort](ioports.md) -- Interacting directly with CPU peripheral buses.