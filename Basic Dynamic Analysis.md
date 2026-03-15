#malwareanalysis 

## Procmon

To monitor process activity, registry events, network activity and more.

## API logger and API monitor

The Windows OS abstracts the hardware and provides an Application Programmable Interface (API) for performing all tasks.

### API logger

The API Logger is a simple tool that provides basic information about APIs called by a process. 

### API monitor

The API Monitor provides more advanced information about a process's API calls.

## Process Explorer

Process Explorer is another very useful tool from the Sysinternals Suite. It can be considered a more advanced form of the Windows Task Manager. Process Explorer is a very powerful tool that can help us identify process hollowing and masquerading techniques

### Process Masquerading

Malware authors sometimes use process name similar to Windows processes or commonly used software to hide.

### Process Hollowing

Malware binary hollows an already running legitimate process by removing all its code from its memory and injecting malicious code in place of legitimate code.

So even though it is legitimate process, it is running malicious code behind the scenes.

## Regshot

Regshot is a tool that identifies any changes to the registry (or the file system we select). It can be used to identify what registry keys were created, deleted, or modified during our dynamic analysis by malware. Regshot works by taking snapshots of the registry before and after the execution of malware and then comparing the two snapshots to identify the differences between the two.

