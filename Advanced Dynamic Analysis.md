#malwareanalysis 

## Evasion of Static Analysis:

- Changing the hash
- Defeating AV signatures by changing patterns to evade signatures
- Obfuscation of strings
- Runtime loading of DLLs
- Packing and Obfuscation: Packers pack the malware in a wrapper by writing code that decodes the malware at runtime.

## Evasion of Basic Dynamic Analysis

- Identification of VMs: Checks for drivers and registry keys associated with virtualization software.
- Timing attacks: Timeout the automated sandbox analysis. 
- Traces of user activity: No mouse or keyboard movement, browser history, no recently opened files, system uptime, etc.
- Identification of analysis tools

## Debugging

Debuggers are software allowing malware analysts to control and monitor the sample execution at runtime.

### Types of Debuggers

#### Source-Level Debuggers

These debuggers work on the source code level. It is a high level debugger where we can see the variables and their values.

#### Assembly-Level Debuggers

When a program is compiled, its source code is lost and cannot be recovered. To analyze the compile binary, we use assembly level debuggers. It attaches to the program that has to be debugged and executes it as per the analyst's desire.

#### Kernel-Level Debuggers

These debuggers debug a program at the Kernel level. For this level of debugging, two systems are required, one for debugging the code running on other system. This is because if the kernel is stopped using a breakpoint, the whole system will stop.

Debuggers Software: Windbg, Ollydbg, IDA and Ghidra

## Patching

We can see that the execution has been restarted and brought back to the branch of code where we identified a detection evasion, that is, at the TLS callback breakpoint.

 We can change the execution path on runtime by manipulating the registers. However, if we execute the sample again, it will again go on to the evasion path unless we change the registers. If we wish to defang the binary, we will have to change it so that the evasion path is not an option if it is just run through double-clicking. This is called patching. This way, after being defanged, we can perform dynamic analysis to reach our desired conclusion. Let's see how we can do that.

We can change this in the following ways.

1. Change the jne to je. This way, the jump will be taken without us changing the ZF.
2. Change the conditional jump to an unconditional jump. This way, the jump will always be taken regardless of the condition above.

However, there is one more way we can edit the binary for it to take our preferred execution path. That is, by filling the call instruction at address D1169 with NOPs. NOPs are instructions that don't do anything. 

