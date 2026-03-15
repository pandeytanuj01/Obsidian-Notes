#malwareanalysis #x86architecture 

## CPU Architecture Overview

The CPU architecture that is most widely used in Von Neumann architecture. 

![[Pasted image 20251220193550.png]]

CPU has three components: ALU, Control Unit and Registers. The CPU interacts with memory and I/O devices. 

### Control Unit

The control unit gets the instructions from the main memory. The address to the next instruction to execute is stored in a register called the Instruction Pointer. In 32-bit systems, this register is called EIP, and in 64 bits systems, it is RIP.

### Arithmetic Logic Unit

The arithmetic logic unit executes the instructions fetched from the memory. The result of the executed instruction are then stored in either Register or the Memory.

### Registers

The memory, contains all the code and data for a program to run. When user executes a program, its code and data are loaded into the memory, from where the CPU access it one instruction at a time.

### I/O Devices

I/O devices are all other devices that interact with a computer. These devices include Keyboard, mouse, Displays, Printers, Mas storage devices like Hard disks or USBs, etc.

So, When a program has to be executed, it is loaded into the memory. From there, the Control Unit fetches the instruction one at a time using the Instruction Pointer Register, and the Arithmetic Logic units executes it. The results are stored in either register or memory.