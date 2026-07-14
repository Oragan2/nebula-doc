# Address Types

These types were create to prevent mistake where a virtual address get mixed with a physical one

## The Types:
* `vaddr` - Virtual addresses.
* `paddr` - Physical addresses.

<div style="background: #2e3440; padding: 10px; border-left: 4px solid #88c0d0;">
    <strong>Hardware Note:</strong> Direct writes to physical addresses bypass the MMU.
</div>

## Details:

### Size : 

These types are unsigned 64 bit `uint64` variables when compiled

### Casting info : 

These types can be casted like normal unsigned 64 bit int, though casting one type to the other will cause a compiler warning

### Variants : 

Each address type can have additional information tied to them which are added using the `@` symbole the variants are : 
- uncached -> make the write and read to the address not cache

```nebula
paddr@uncached screen = 0xB8000;

write16(screen, (char << 8) | color);
```
- wc -> write_combine, buffer writes and apply them in quick successions

```nebula
vaddr@wc addr = 0x1000;

write64(addr, 15);
write64(addr, 16);
write64(addr, 17);
applyWrites(addr);
// all writes are applied
```

- wt -> write_through, write to cache and RAM at the same time
- readonly -> doesn't allow writes to be made

```nebula
vaddr@readonly addr = 0x1000;

int32 x = read32(addr);
write(addr, 15); // compilation error
```

- nx -> doesn't allow execution from this address 
- device -> tells the cpu this is a device address to not make it change order