# Types

The Nebula language supports the known types from all language to more dedicated type(see [LowLevelTypes](low-level-types.md) for more info) the types are as follow : 
- int8/16/32/64 -> the usual signed int in the 8/16/32/64 bits variants
- uint8/16/32/64 -> the unsigned int in the 8/16/32/64 bits variants
- float32/64 -> the usual floats

## Additional type parameters:

Type parameters are passed with `@[...]` and the parameters between the brackets the parameters are : 
- const -> make the variable constant and not changeable at runtime
- static -> make the variable private to the file