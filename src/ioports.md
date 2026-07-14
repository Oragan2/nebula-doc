# I/O ports

The ioport type maps directly to the x86 I/O port address space. This specialized namespace is used to communicate directly with legacy and core hardware peripherals, such as system timers, keyboard controllers, serial ports, and disk controllers.

## Technical Details:

### Representation & Size: 

This types are compiled into unsigned 16 bits `uint16` variables, to match the 16 bit limits of the x86 address bus
```nebula
ioport@[const] COM1 = 0x3F8;
```


### Type Casting Info:

Because ioport behaves exactly like a 16-bit unsigned integer under the hood, it can be cast to and from standard uint16 values. This is can be useful when calculating register offsets.

```nebula
ioport@[const] COM1 = 0x3F8;

uint16 baseNumeric = uint16(COM1);
ioport lineStatusReport = ioport(baseNumeric + 5);
```