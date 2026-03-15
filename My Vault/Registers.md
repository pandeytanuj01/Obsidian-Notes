#malwareanalysis #x86architecture

Registers are CPUs storage medium. The CPU can access registers faster than any other storage medium, however, they are small in size.

There are 4 types of registers:

- Instruction Pointer
- General Purpose Registers
- Status Flag Registers
- Segment Registers

## The Instruction Pointer

This register contains address of the next instruction which is to be executed by the CPU. It is also called Program Counter. 

## General Purpose Register

In an x86 system, general purpose registers are all 32 bit registers. These are used during the general execution of instructions by the CPU. In 64 bit systems, these registers are extended as 64 bit registers.

### EAX or RAX

Accumulator Register. Results of arithmetic operations are often stored in this register. The last 16 bits of this register can be accessed by addressing AX. It can also be addressed in 8 bits by using AL for the lower 8 bits and AH for the higher 8 bits.

### EBX or RBX

Base Register. It is often used to store the base address for referencing an offset. Similar to previous register, it can be addressed as 64 - bit RBX, 32 - bit EBX, 16 - bit BX and 8 - bit BH and BL register.

### ECX or RCX

Counter Register. It is often used in counting operations such as loops, etc. Similar to previous registers, it can be addressed as 64-bit RCX, 32-bit ECX, 16-bit CX, and 8-bit CH and CL registers.

### EDX or RDX

Data Register. It is often used in multiplication/division operations. Similar to the above registers, it can be addressed as 64-bit RDX, 32-bit EDX, 16-bit DX, and 8-bit DH and DL registers.

### ESP or RSP

Stack Register. It points to the top of the stack and is used in conjunctions with Stack Segment Register. It is a 32-bit register called ESP in 32-bit systems and a 64-bit register called RSP in 64-bit systems. It can not be addressed as smaller registers.

### EBP or RBP

Base Pointer. It used to access the parameters passed by the stack. It is also used in conjunction with Stack Segment Register. It is a 32-bit register called EBP in 32-bit systems and a 64-bit register called RBP in 64-bit systems.

### ESI or RSI 

Source Index Register. It is used for string operations. It is used with Extra Segment register as an offset. It is a 32-bit register called ESI in 32-bit systems and a 64-bit register called RSI in 64-bit systems.

### EDI or RDI

Destination Index Register. It is also used for string operations. It is used with the Extra Segment (ES) register as an offset. It is a 32-bit register called EDI in 32-bit systems and a 64-bit register called RDI in 64-bit systems.

### R8-R15

These 64 bit general purpose registers are not present in 32 bit systems. They were introduced in 64 bits systems. They are also addressable in 32-bit, 16-bit, and 8-bit modes. For example, for the R8 register, we can use R8D for lower 32-bit addressing, R8W for lower 16-bit addressing, and R8B for lower 8-bit addressing. Here, the suffix D stands for Double-word, W stands for Word, and B stands for Byte.
![[Pasted image 20251230222805.png]]

## Status Flag Register

When performing execution, some kind of status for execution is sometimes required. This is where the status flag comes in.

It consists of individual single bit flags that can be either 1 or 0. 

### Zero Flag

It indicates when the result of the last executed instruction was zero. For e.g. If an instruction is executed that subtracts RAX from itself, the result will be 0. Hence, the Zero flag would be 1. 

### Carry Flag

It indicates when the last executed instruction resulted in a number too big or small for the destination. For example, if we add 0xFFFFFFFF and 0x00000001 and store the result in a 32-bit register, the result will be too big for the register. In this case, CF will be set to 1.

### Sign Flag

If the results of an operation is negative or the most significant bit is set to 1. The SF is set to 0.

### Trap Flag

It indicates if the processor is in debugging mode. When the TF is set, the CPU will execute one instruction at a time for debugging purposes. This can be used by malware to identify if they are being run in a debugger.

| **General Registers** | **Segment Registers** | **Status Registers** | **Instruction Pointer** |
| --------------------- | --------------------- | -------------------- | ----------------------- |
| RAX, EAX, AX, AH, AL  | CS                    | EFLAGS               | EIP, RIP                |
| RBX, EBX, BX, BH, BL  | SS                    |                      |                         |
| RCX, ECX, CX, CH, CL  | DS                    |                      |                         |
| RDX, EDX, DX, DH, DL  | ES                    |                      |                         |
| RBP, EBP, BP          | FS                    |                      |                         |
| RSP, ESP, SP          | GS                    |                      |                         |
| RSI, ESI, SI          |                       |                      |                         |
| RDI, EDI, DI          |                       |                      |                         |
| R8-R15                |                       |                      |                         |

## Segment Registers

These are 16 - bit registers that convert the flat memory space into different segments for easier addressing. 

#### Code Segments: Points to the code section of the memory

#### Data Segments: Points to the program's data section in the memory

#### Stack Segment: Points to program's stack in the memory

#### Extra Segment (ES, FS and GS): These registers point to different data sections. These and the DS register divide the program's memory into four different data sections