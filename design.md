# Design Decisions

## Register translation

| i386   | arm      |
| ------ | -------- |
| eax    | r0       |
| ebx    | r1       |
| ecx    | r2       |
| edx    | r3       |
| esi    | r4       |
| edi    | r5       |
| esp    | sp (r13) |
| ebp    | r6       |
| eip    | pc (r15) |
| eflags | cpsr     |

- r7 apparently holds syscall numbers
- r11 is fp (ebp)
- r12 apparently is used for IPC
- r14 is lr

This leaves r6 / r7 / r8 / r9 / r10 / r12 usable.

- In Thumb, only `mov`, `add`, and `cmp` can access r8+
- In Thumb, only `add` and `sub` can update SP. Only `add`, `ldr`, and `str` can use the value of SP.
- In Thumb, only `pop` (ARMv4+), `b`, `bl`, `blx` (ARMv5+), `cb[n]z` (ARMv6T2+) can update PC. Only `adr` and `ldr` can use the value of PC.
- In Thumb, only `bl` and `blx` (ARMv5+) can update LR. Only `push` can use the value of LR.

|      | r8/9/10/12 | fp (r11) | sp (r13) | lr (r14) | pc (r15) |
| ---- | ---------- | -------- | -------- | -------- | -------- |
| mov  | both       | both     | both     | both     | both     |
| add  | both       | both     | both     | both     | both     |
| cmp  | both       | both     | both     | both     | both     |
| sub  |            |          | set      |          |          |
| ldr  |            |          | read     |          | read     |
| str  |            |          | read     |          |          |
| pop  |            |          | set      |          | set      |
| b    |            |          |          |          | set      |
| bl   |            |          |          | set      | set      |
| blx  |            |          |          | set      | set      |
| cbz  |            |          |          |          | set      |
| adr  |            |          |          |          | read     |
| push |            |          | set      | read     |          |

- Okay, after some trial-and-error, I don't think I can use fp for the return stack in Thumb.
