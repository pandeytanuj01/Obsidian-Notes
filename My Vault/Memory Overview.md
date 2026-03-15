#malwareanalysis #x86architecture

When a program is loaded into the memory, it sees only the abstracted view of the memory. This means that program only has access to its assigned memory.

Memory is divided into different sections mainly Stack, Heap, Code and Data.

## Code

It contains the program's code. This section refers to the text section in a Portable Executable file, which includes instructions executed by the CPU.

This section of the memory has execute permissions, meaning CPU can execute the data present in this section of the memory.

## Data

It contains initialized data that is not a variable and remains constant. It refers to the data section in a Portable Executable File. It often contains Global variable and other data that are not supposed to change during program's execution.

## Heap

Also known as Dynamic Memory, contains variables and data created and destroyed during program's execution. When a variable is created, memory is allocated for that variable in runtime, and when the variable is deleted, the memory is freed. 

## Stack

Its one of the important parts of the memory. This sections contains local variables, arguments passed on to the program, and return address of the parent process that called the program. Since the return address is related to control flow of the CPU's instructions, the stack is often targeted by malware to hijack the control flow.

![[Pasted image 20260101221138.png]]

### Stack Layout

The stack is a LIFO memory (Last In First Out). The elements pushed onto the stack is the first one to be popped out. The CPU uses 2 registers to keep track of the stack. One is Stack Pointer (ESP or RSP) and Base Pointer (EBP or RBP).

#### Stack Pointer

It points to the top of the stack. When any new elements is pushed on the stack, the location of the stack pointer changes to consider the new element that was pushed onto the stack.

Similarly, when an element is popped, the stack pointer adjusts itself.

#### The Base Pointer

It remains constant for any program. This is the reference address where the stack tracks its local variables and arguments.

#### Old Base Pointer and Return Address

Below Base pointer lies the Old Base pointer and below the old base pointer lies the return address, where the Instruction Pointer will return once the current program's execution ends.

A common technique to hijack control flow is to overflow the local variable on the stack so that it ovewrites the Return Address with an address of the malware author's choice. This is called Stack Buffer Overflow.

#### Arguments

The arguments being passed to a function are pushed onto the stack before the functions starts the execution. These arguments are present right below the Return Address.

#### Function Prologue and Epilogue

When a function is called, the stack is prepared for a function to execute. This means that the arguments are pushed to the stack before the function starts the execution.

After that, the Return Address and the Old Base Pointer are pushed onto the stack. Once these elements are pushed, the Base Pointer address is changed to the top of the stack (which will the Stack Pointer of the caller function at that time).

As the function executes, the Stack Pointer moves as per the requirements of the function.

This portion of code is called Function Prologue.

Similarly, the Old Base Pointer is popped off the stack and onto the Base Pointer when the function exits. the Return Address is popped off to the Instruction Pointer, and the Stack Pointer is rearranged to point to the top of the stack. This part of code is called Function Epilogue.

![[Pasted image 20260101222610.png]]