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
| ebp    | fp (r11) |
| eip    | pc (r15) |
| eflags | cpsr     |
