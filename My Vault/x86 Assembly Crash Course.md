#x86architecture #malwareanalysis 

## Opcodes and Operands

Opcodes are numbers that corresponds to instructions performed by the CPU. 

When using a disassembler, it reads the opcodes. It translates them into assembly instructions to make them human readable. 

For e.g.: the instruction for moving `0x5F` to the `eax` register is:

`mov eax, 0x5f`

When looking at the disassembler:

`040000:     b8 5f 00 00 00       mov eax, 0x5f`

Here, `040000` corresponds to the address where the instruction is located `b8` refers to the opcode of the instruction and `5f 00 00 00` indicates the other operand  `0x5f`.

Similarly there is an opcode for each instruction in the assembly language.

## Type of Operands

1. **Immediate operands:** These are constants. For e.g.: `0x5f`
2. **Registers:** For e.g.: `eax` as a register where the immediate operand is stored.
3. **Memory Operands**: These are denoted by square brackets and they reference memory locations. For e.g.: `[eax]` as an operand will denote the value in `eax` is the memory location on which the operation has to be performed.

## General Instructions

Instructions tell the CPU what operation to perform.

### The MOV Instruction

It moves value from one location to another. 

Syntax: `mov destination, source`

### The LEA Instruction

It stands for load effective address.

Syntax: `lea destination, source`

It moves the address of the source to destination. 

### The NOP Instruction

Its stands for no operation. This instruction exchanges the value in eax with itself resulting in no meaningful operation. Hence, the execution moves to next instruction without changing anything. They are used to consume CPU cycles while waiting for an operation.

Syntax: `nop`

### Shift Instructions

The CPU uses shift instructions to shift each register bit to the adjacent bit. There are two shifting instructions for shifting either right or left. 

Syntax:

`shr destination, count`
`shl destination, count`


The instructions shift the bits in the destination. The count operand decides the number of bits to be shifted. 

The bits which are shifted out of their location are filled with zeros. For e.g. 

`eax = 00000010`

If we shift it to left, `eax` will be `00000100`

The carry Flag is used to augment the destination, as it is filled by the last bit overflowing the destination. 

For e.g.

`eax = 00000101`

If we shift it to right by 1 bit, `eax` will be `00000010`, with `CF = 1`.

Shift instructions are used instead of multiplication or division by 2 or power of 2. This saves execution time by not having to manipulate values in registers before performing multiplication or division. 

For e.g. 

`eax = 00000010`

if we shift it right by 1, `eax` will be `00000001`, which is basically division by 2

now, if we shift it left by 1, `eax` will be `00000010`, which is basically multiplication by 2

### Rotate Instructions

Similar to shift instructions, with only difference the bits are rotated back to the other end of the register instead of moving the overflowing bit to the carry flag or adding zeroes instead of shifted-out bits. 

Syntax:

`ror destination, count`
`rol destination, count`

For e.g.: if we have 10101010 in al, if we rotate to right by 1 bit, result will be 01010101

now if we rotate to left by 1 bit, result will be same as the starting value, 10101010


## Flags

CPU has several flag which indicate the outcome of a certain operations or conditions. These flags are bits in a special register known as the flags register or EFLAGS. 

| Flag             | Abbreviation | Explanation                                                                                                                                       |
| ---------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Carry            | CF           | Set when a carry-out or borrow is required from the most significant bit in an arithmetic operation. Also used for bit-wise shifting operations.  |
| Parity           | PF           | Set if the least significant byte of the result contains an even number of 1 bits.                                                                |
| Auxiliary        | AF           | Set if a carry-out or borrow is required from bit 3 to bit 4 in an arithmetic operation (BCD arithmetic).                                         |
| Zero             | ZF           | Set if the result of the operation is zero.                                                                                                       |
| Sign             | SF           | Set if the result of the operation is negative (i.e., the most significant bit is 1).                                                             |
| Overflow         | OF           | Set if there's a signed arithmetic overflow (e.g., adding two positive numbers and getting a negative result or vice versa).                      |
| Direction        | DF           | Determines the direction for string processing instructions. If DF=0, the string is processed forward; if DF=1, the string is processed backward. |
| Interrupt Enable | IF           | If set (1), it enables maskable hardware interrupts. If cleared (0), interrupts are disabled.                                                     |


## Arithmetic Operations

### Addition and Subtraction Instructions

Syntax: 

`add destination, value
`sub destination, value

### Multiplication and Division Instructions

These instructions use the eax and edx registers. Therefore, we have to look at the last instructions that manipulated these registers for every multiple and divide operation.

For Multiplication, it multiplies the value with eax and stores the result in edx:eax as 64-bit value. Two registers as multiplying 2 32-bit values can be higher than 32-bit. The lower bits of the results are stored in eax and higher 32 bits are stored in the edx register.

`mul value`

The value can be a constant or another register.

For division, it is the opposite, It divides the 64-bit value in edx:eax and saves the result in eax and the remainder in edx.

`div value`

### Increment and Decrement Instructions

Increment or decrement the operand register by 1.

Syntax: 

`inc eax`
`dec eax`

## Logical Instructions

### AND Instruction

It performs a bitwise AND operation on the operands. An AND returns 1 when both operands are 1; otherwise it returns 0. For e.g.:

`and al, 0x7c`

0x7c converts to 01111100 in binary. Suppose al had a value of 0xfc, which is 11111100 in binary. In this case, the output of the above instruction will be 01111100. However, if al has a value of 0x8c, 10001100 in binary, the result of the above instruction will be 00001100 or 0xc.

### OR Instruction

It performs bitwise OR operations. An OR operation returns 1 if at least one operand is 1. It returns 0 if none of the operands are 1.

`or al, 0x7c`

In this example, if al had a value of 0xfc or 11111100 in binary, the output of the above instruction will be 11111100. Similarly, if al has a value of 0x8c or 10001100 in binary, the result will still be 11111100 in binary or 0xfc.

### NOT Instruction

It takes one operand. It inverts the operands bits, replace 1s with 0s and vice versa.

`not al`

### XOR Instruction

It returns 1 if both inputs are opposite. It returns 0 if both input are same.

`xor al, 0x7c`

If al has a value of 0xfc, which is 11111100, the result of this instruction will be 10000000 or 0x80. Similarly, if al has a value of 0x8c, which is 10001100, the result of this instruction will be 11110000 or 0xf0. If al has a value of 0x7c, the result will be 0x00. This shows that XORing a register with itself results in 0. Therefore, the XOR instruction is often used to zero a register, which is more optimized than a MOV instruction.


## Conditionals

A CPU must determine whether 2 values are equal to, greater than or less than each other. To perform such operations, CPU must use come conditional instructions.

### The TEST Instruction

Performs a bitwise AND operation, and instead of storing the result in destination operand as the AND instruction does, it sets the ZF if the result is 0. The instruction is often used to check if an operand has NULL value, for example by testing the operand against itself.

This is done to because it takes fewer bytes to use the test instruction than by comparing it to 0.

Syntax: `test destination, source`

### The CMP Instruction
``
It compares the two operands and sets the ZF or the CF.

`cmp destination, source`

It works similar to subtraction instruction. The only difference is the operands are not modified.

The ZF is set if both are equal. If source is greater than destination, the CF is set, otherwise both ZF and CF are cleared.

| **Condition**                | **Example** | **Flags affected by cmp instruction** | **Flags affected by test instruction** |
| ---------------------------- | ----------- | ------------------------------------- | -------------------------------------- |
| When both values are equal   | eax = ebx   | Parity Flag, Zero Flag                | No flag is impacted                    |
| When eax is greater than ebx | eax > ebx   | No flag is impacted                   | Parity Flag, Zero Flag                 |
| When eax is less than ebx    | eax < ebx   | Carry Flag, Sign Flag                 | Parity Flag, Zero Flag                 |
## Branching

When there is no branching, the instruction pointer moves from one pointer to another based on the order they are placed in the memory.

However, branching changes the value of the instruction pointer and changes the program's control flow.

### The JMP Instruction

Makes the control flow jump to the specified location.

`jmp location`

Location operand will be moved to the instruction pointer, making it the address where the next instruction will be fetched for execution.

### Conditional Jumps

They decide whether to jump to the specific location based on the value of the flag registers.

| **Instruction** | **Explanation**                                                                                                                                       |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| jz              | Jump if the ZF is set (ZF=1).                                                                                                                         |
| jnz             | Jump if the ZF is not set (ZF=0).                                                                                                                     |
| je              | Jump if equal. Often used after a CMP instruction.                                                                                                    |
| jne             | Jump if not equal. Often used after a CMP instruction.                                                                                                |
| jg              | Jump if the destination is greater than the source operand. Performs signed comparison and is often used after a CMP instruction.                     |
| jl              | Jump if the destination is lesser than the source operand. Performs signed comparison and is often used after a CMP instruction.                      |
| jge             | Jump if greater than or equal to. Jumps if the destination operand is greater than or equal to the source operand. Similar to the above instructions. |
| jle             | Jump if lesser than or equal to. Jumps if the destination operand is lesser than or equal to the source operand. Similar to the above instructions.   |
| ja              | Jump if above. Similar to jg, but performs an unsigned comparison.                                                                                    |
| jb              | Jump if below. Similar to jl, but performs an unsigned comparison.                                                                                    |
| jae             | Jump if above or equal to. Similar to the above instructions.                                                                                         |
| jbe             | Jump if below or equal to. Similar to the above instructions.                                                                                         |

## Stack and Function Calls


### The PUSH Instruction

`push source`

It will push the source operand into the stack. The value of the operand is stored at the memory location pointer to by the stack pointer, effectively becoming the new top of the stack. 

The stack pointer is then adjusted to reflect the updated position of the stack. 

**pusha**: Pushes all the 16-bit general purpose registers into the stack.
**pushad**:  Pushes all the 32-bit general purposed registers into the stack.

### The POP Instruction

`pop destination`

Retrieves value from the top of the stack and stores it in the destination operand. The ESP is incremented to reflect the adjustment made after popping the value. 

**popa**: Pops the values sequentially from the top of the stack to general purpose registers, in a specific order: DI, SI, BP, BX, DX, CX, AX.
**popad**: Pops the values sequentially from the top of the stack to general-purpose registers, in a specific order: EDI, ESI, EBP, EBX, EDX, ECX, EAX.

### The CALL Instruction

It is used for performing a function call operation to perform a specific task. 

`call location`

Depending on the calling convention, the arguments are placed on the stack or in the registers in a function call. The function prologue prepares the stack by adjusting the EBP and ESP and pushing the return address on the stack. Similarly, when the function returns, the epilogue restores the stack for the caller function.

