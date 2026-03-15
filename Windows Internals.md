#malwareanalysis #windows

## Processes

Processes are created from execution of a program. They maintain and represent a program execution. An application can have one or more processes.

Processes have many components:

- **Private Virtual Address Space**: Virtual memory address that the process is allocated.
- **Executable Program**: Defines code and data stored in the virtual address space.
- **Open Handles**: Defines handles to system resources accessible to the process.
- **Security context**: The access token defines the user, privileges, security groups, and other security information.
- **Process ID**: Unique numerical identifier of the process.
- **Threads**: Section of the process scheduled for execution.

In memory:

- Code: Code to be executed by the process.
- Global Variables: Stored variables
- Process Heap: Defines the heap where the data is stored.
- Process Resources: Defines further resources of the process.
- Environment Block: Data structure to define the process.

![[Pasted image 20251216202446.png]]


## Threads

Thread is an executable unit employed by the process and is scheduled based on device factors. Device factors can vary based on CPU and memory specifications, priority and logical factors.

Since thread controls the execution, it is usually targeted by the attackers.

Threads share the same details as their parent process, however they also have their unique values and data, as follows:

- Stack: All data relevant and specific to the thread. (procedure calls, exceptions, etc.)
- Thread Local Storage: Pointer for allocating storage to a unique data environment
- Stack Argument: Unique value assigned to each thread
- Context Structure: Holds machine register values maintained by the kernel.

## Virtual Memory

Virtual memory allows other internal components with the memory as if it was a physical memory without the risk of collisions between the application. 

It provides each process with a private virtual address space. A memory manager is used to translate the virtual address to physical addresses. This decreases of the risk of a process causing damage to physical memory.

The memory manager will use pages or transfers to handle memory. As application may need more virtual memory than physical memory allocated, the memory manager transfers or pages the virtual memory to disk to solve the problem.

The theoretical maximum virtual address space is 4 GB on a 32-bit x86 system.

This address space is split in half, the lower half (_0x00000000 - 0x7FFFFFFF_) is allocated to processes as mentioned above. The upper half (_0x80000000 - 0xFFFFFFFF_) is allocated to OS memory utilization. Administrators can alter this allocation layout for applications that require a larger address space through settings (_increaseUserVA_) or the AWE (**A**ddress **W**indowing **E**xtensions).

The theoretical maximum virtual address space is 256 TB on a 64-bit modern system.

The exact address layout ratio from the 32-bit system is allocated to the 64-bit system.

## Dynamic Link Libraries


A library that contains code and data that can be used by more than one program at the same time.

It is used for modularization of code, reusability and efficient memory and disk usage. This results in faster loading and execution with less disk space.

When a DLL is loaded as a function in a program, the program is dependent on the DLL for some specific functionality, attackers target these DLLs rather than the application to control some aspect of the execution or functionality.

### DLL Example:

```cpp
#include "stdafx.h"
#define EXPORTING_DLL
#include "sampleDLL.h"
BOOL APIENTRY DllMain( HANDLE hModule, DWORD ul_reason_for_call, LPVOID lpReserved
)
{
    return TRUE;
}

void HelloWorld()
{
    MessageBox( NULL, TEXT("Hello World"), TEXT("In a DLL"), MB_OK);
}
```

Header file which indicates what functions are being imported and exported.

```cpp
#ifndef INDLL_H
    #define INDLL_H
    #ifdef EXPORTING_DLL
        extern __declspec(dllexport) void HelloWorld();
    #else
        extern __declspec(dllimport) void HelloWorld();
    #endif

#endif
```

DLLs are usually loaded in a program using a **load-time dynamic linking** or **run-time dynamic linking**.

When loaded using **load-time dynamic linking**, explicit calls to the DLL functions are made from the application. This can only be achieved by providing the header and library file. For e.g.: 

```cpp
#include "stdafx.h"
#include "sampleDLL.h"
int APIENTRY WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow)
{
    HelloWorld();
    return 0;
}
```

When loaded using **run-time dynamic linking**, a separate function is used to load the DLL at  runtime. Once loaded, you need to use `GetProcAddress` to identify the exported DLL function to call. For e.g.:

```cpp
...
typedef VOID (*DLLPROC) (LPTSTR);
...
HINSTANCE hinstDLL;
DLLPROC HelloWorld;
BOOL fFreeDLL;

hinstDLL = LoadLibrary("sampleDLL.dll");
if (hinstDLL != NULL)
{
    HelloWorld = (DLLPROC) GetProcAddress(hinstDLL, "HelloWorld");
    if (HelloWorld != NULL)
        (HelloWorld);
    fFreeDLL = FreeLibrary(hinstDLL);
}
...
```

Threat actors usually use run-time dynamic linking as they may need to transfer files between memory regions and transferring a single DLL is more manageable than importing using other file requirements.

## Portable Executable Format

The portable executable format defines the information about the executable and stored data. It also defines the structure of how the data components are stored.

Its made up of PE (Portable Executable) and COFF (Common Object File Format).

It is most commonly seen in a hex dump of an executable file. 

The structure of PE data is broken up into seven components:

![[Pasted image 20251217003224.png]]

- **DOS Header**: It defines the type of file. 
- **PE File Header**: It provides header information of the binary. It defines the format of a file, contains the signature and image file header and other information headers. The start of PE header is identified from the `PE` stub
- **Image Optional Header**: It is an important part of PE File Header. Data Dictionaries are part of Optional Header and points to the Image Data Directory structure.
- **Section Table**: It will define the available sections and information in the image. It stores the contents of the file such as code, imports, and data.

| **Section  <br>** | **Purpose**                                          |
| ----------------- | ---------------------------------------------------- |
| .text             | Contains executable code and entry point             |
| .data             | Contains initialized data (strings, variables, etc.) |
| .rdata or .idata  | Contains imports (Windows API) and DLLs.             |
| .reloc            | Contains relocation information                      |
| .rsrc             | Contains application resources (images, etc.)        |
| .debug            | Contains debug information                           |

## Windows API

It provides a native functionality to interact with Windows operating system. The API contains Win32 API and Win64 API.

Windows kernel controls all programs and processes and bridge all software and hardware interactions.

An application by default cannot interact with kernel or modify physical hardware and require an interface. 

A windows processor has a user and kernel mode. The processor will switch between these modes depending on access and requested mode.

The switch between the user and kernel mode is often facilitated by system and API calls.

| **User mode**                                        | **Kernel Mode**                              |
| ---------------------------------------------------- | -------------------------------------------- |
| No direct hardware access                            | Direct hardware access                       |
| Creates a process in a private virtual address space | Ran in a single shared virtual address space |
| Access to "owned memory locations"                   | Access to entire physical memory             |

![[Pasted image 20251217010958.png]]

Applications start in user mode and will stay in that mode until a system call is made or interfaced through an API. When a system call is made, the application will switch modes.