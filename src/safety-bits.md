# Safety Bits

To protect against memory corruption, the Nebula compiler automatically injects Safety Bits (canaries) at the boundary of every memory layout (such as globals, file scopes, and function stack frames).

These safety bits are placed directly before your variables in memory. If a buffer overflow or wild write occurs, it will overwrite the safety bit first, allowing you to detect the corruption before trusting or executing sensitive operations.

## How they work:

For every memory scope, the compiler generates a random 64-bit unsigned value `uint64` at compile time and places it at the very start of that scope's memory layout.

To allow developers to verify memory integrity, the compiler automatically exposes two special variables:

`s{scope}Bits`: A pointer to the physical safety bit stored in memory.

`s{scope}Expected`: A compile-time const containing the original, correct random value.

Because `s{scope}Bits` is a pointer, checking its value forces the CPU to read the actual, live state of that memory boundary. If the value pointed to does not match the expected constant, corruption has occurred.

## How they can be used:

Imagine a function that verifies if a user is an administrator before executing a high-privilege command:
```nebula
void executeAdminCommand(uint64 userID, Command cmd) {
    if (users[userID].isAdmin)
        executeCommand(cmd);
}
``` 
If a buffer overflow occurs somewhere in the system, the memory storing the userID parameter, the users array, or the local stack frame could be modified, tricking the system into running a command with forged privileges.

By checking the function and global safety bits first, you can guarantee memory integrity before running sensitive code:
```nebula
void executeAdminCommand(uint64 userID, Command cmd) {
    if (*sFunctionBits != sFunctionExpected || *sUsersBits != sUsersExpected) {
        showError("Safety bit changed, cannot guarantee proper verification of user rights");
        return;
    }
    if (users[userID].isAdmin)
        executeCommand(cmd);
}
```