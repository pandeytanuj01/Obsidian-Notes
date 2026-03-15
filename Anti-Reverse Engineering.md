#malwareanalysis 
### Anti-Debugging 

Debugging is the process of examining software to understand its inner workings and identify potential vulnerabilities or issues. It involves software tools called debuggers that allow analysts to step through the code and monitor its execution.

Common debuggers used:

- x64dbg
- Ollydbg
- Ida Pro
- Ghidra

#### Anti-Debugging Techniques

- **Checking for the presence of debuggers**: Malware code looks for processes or files associated with debugging tools or hardware-based techniques, such as detecting hardware breakpoints. For example, a common anti-debugging technique uses the Windows API function `IsDebuggerPresent` to check if a debugger is running.

- **Tampering with debug registers**: Malware may try to modify or corrupt debug logs, which debuggers use to control the execution of code. This prevents debugger from functioning correctly

- **Using self-modifying code**: Malware modifies itself while running, making it difficult for a debugger to follow the code flow.

Suspend Thread is a Windows API function that is used to pause the execution of a thread in a running process. This function has legitimate uses but can also be called from within a malware process to suspend any threads attempting to debug or analyze it. 

#### Patching:  [[Advanced Dynamic Analysis#Patching|Explained in this Page]]

- Right-click anywhere on the main screen, highlight "Search for > Current Module", then select "Intermodular calls".
- On the search field at the bottom, type "SuspendThread". This will filter the list in the main window showing us one entry. Double-click on this entry.
- You are now on the part of the code that calls the SuspendThread API. To skip this function, right-click on the Suspendthread line and select Binary > Fill with NOPs. Don't change anything here, and press "OK".
- You will see that memory locations `0004011AB` up to `004011B0` are now all set to the `90` hex value or `nop` (No Operation). If execution reaches this instruction, it will do nothing and proceed with the following line.


### VM Detection

Virtual Machines are software platforms that simulate a computer environment inside another computer system. These are useful in reverse engineering because they provide cost-effective, controlled and isolated environment for monitoring and analyzing suspicious software or malware.

VMs also allow the creation of a snapshots and checkpoints that can be used to restore the system to a previous state.

When a malware identifies that it is running on a VM, it may decide to respond differently. For e.g., it may try to change its behaviour by:

- Executing only minimal subset of its functionality
- Self-destructing by deleting itself or overwriting part of its code.
- Cause damage to system by deleting or encrypting files 
- Not run at all

#### VM Detection Techniques

- **Checking running processes**: VMWare runs a process called `vmtools`, while VirtualBox has `vboxservice`. Malware can use the `EnumProcess` windows API to list all the processes running on the machine and look for the presence of these tools.

- **Checking installed software**: Malware can look in the Windows Registry for a list of installed software under the `SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Uninstall` Registry key. It can check for installed programs like debuggers, decompilers, forensic tools, etc.

- **Network fingerprinting**: Malware can look for specific MAC and network address unique to VMs. For e.g., VMs autogenerate MAC addresses to start with any of the following numbers: `00-05-69`, `00-0c-29`, `00-1c-14` or `00-50-56`. These numbers are unique and are specifically assigned to a VM vendor called the OUI (Organizationally Unique Identifier).

- **Checking machine resources**: Malware can look into a machine's resources like RAM and CPU utilization percentages. For e.g., a machine with RAM amounting to less than 8 GB can indicate a virtual machine, as they are typically not assigned a significant amount.

- **Detecting peripherals**: Some malware checks for connected printers because this is rarely configured properly on VMs, sometimes not even configured at all.

- **Checking for domain membership**: Corporate networks are a usual target for malware. An easy way to determine this is by checking if the current machine is part of an Active Directory domain. This can quickly be done without the use of API calls by checking the `LoggonServer` and `ComputerName` environment variables.

- **Timing-based attacks**: Malware can measure the time it takes to execute specific instructions or access a particular machine resources. For example, some instructions can be faster on a physical machine compared to a virtual machine.

#### Anti-VM Detection

To prevent malware from using some of the techniques above, we can apply several changes to the system that will remove VM-related artefacts making the VM look less like a VM. 

For example, we can remove or modify the Registry entries that malware checks for installed programs to hide our debuggers, change the MAC addresses, or configure the VM to appear connected to a printer.

But you can see how tedious this becomes. With the myriad of options available to malware, it would be challenging to cover all of them.

Some researchers have made scripts (See [VMwareCloak](https://github.com/d4rksystem/VMwareCloak) and [VBoxCloak](https://github.com/d4rksystem/VBoxCloak)) to help automate this process.

### VM Detection by checking the Temperature

`Win32_TemperatureProbe` is a Windows Management Instrumentation class that contains real-time temperature readings from the hardware through the SMBIOS data structure. In a virtualized environment, the value returned is `Not Supported`, which is what malware looks for.

#### Preventing Temperature Checking

- Manipulating memory directly
- Changing the execution flow with EIP


### Packers

Obfuscation is a technique that aims to intentionally obscure data and code so that it is harder to understand or analyze.

Common obfuscation techniques used by malware authors:

- **Using encoding techniques**: This involves encoding data using popular encoding techniques like `XOR` or `Base64`. 

- **Using encryption techniques**: Encrypting data such as communications to a C2 server, file formats and network traffic. Most common type are symmetric and public key encryption.

- **Code obfuscation**: Involves various techniques such as manipulation the code to alter its syntax and structure, renaming functions, or splitting code across multiple files or code segments.

Packers are tools that compress and encrypt executable files. It compresses the target executable and embeds it within a new executable file that serves as a wrapper or container. This reduces the size of the file making it ideal for distribution and installation. 

Some packers offer additional features, such as code obfuscation, runtime packing, and anti debugging techniques.

- [Alternate EXE Packer](https://www.alternate-tools.com/pages/c_exepacker.php?lang=ENG)
- [ASPack](http://www.aspack.com/) 
- [ExeStealth](https://unprotect.it/technique/exestealth/)
- [hXOR-Packer](https://github.com/akuafif/hXOR-Packer)
- [Milfuscator](https://github.com/nelfo/Milfuscator)
- [MPress](https://www.autohotkey.com/mpress/mpress_web.htm)
- [PELock](https://www.pelock.com/products/pelock)
- [Themida](https://www.oreans.com/Themida.php)
- [UPX: the Ultimate Packer for eXecutables](https://upx.github.io/)
- [VMProtect](https://vmpsoft.com/)

Because Packers encrypts and obfuscates a program, it would be impossible to know the malware's capabilities without running it. Because of this, we cannot reliably depend on static analysis and signature-based detection techniques to determine its capabilities. The only information we could glean from a malware sample at this state is the Packer tool used.


### Identifying and Unpacking

We can use tools like `DetectItEasy` and `PEStudio` to identify the Packer used.

After identifying the Packer, we can use unpacker scripts to unpack the packed file.

Scripts we can use for specific Unpacker tools:

- [Themida](https://github.com/Hendi48/Magicmida)
- [Enigma Protector](https://github.com/ThomasThelen/OllyDbg-Scripts/blob/master/Enigma/Enigma%20Protector%201.90%20-%203.xx%20Alternativ%20Unpacker%20v1.0.txt)
- [Mpress unpacker](https://github.com/avast/retdec/blob/master/src/unpackertool/plugins/mpress/mpress.cpp)

unpac.me - **Automated malware unpacking and artifact extraction**

#### Manual Unpacking and Dumping

The best way to unpack a malware is to execute it. When a packed malware is executed, the wrapper or container code performs decryption and deobfuscation. 

Once fully unpacked, the malware can proceed with its true intentions. At this point, the Packer is useless, and we can thoroughly analyze the malware by debugging it while its in memory using a debugger.

