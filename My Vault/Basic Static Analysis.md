#malwareanalysis 
## String Search

A string search looks at the binary data in a malware sample regardless of its file type and identifies sequences of ASCII or Unicode characters followed by a null character.

It also leads to many False positives, as not all character sequences which fall in this criteria are useful value.

It can be used to find IOCs such as:

- Windows Functions, API calls which can provide the functionality of the malware.
- IP Addresses, URLs or Domains can provide information about possible C2 communication.
- Miscellaneous strings such as Bitcoin addresses, text used for Message Boxes, etc.

### Basic String Search

We can use `strings` utility which is pre installed in Linux for a basic string search.

For windows, we can use `strings.exe` which is a part of Windows Sysinternals suite. 

`PEstudio` can be used to check the strings part of a PE file.

### Obfuscated Strings

Malware authors deploy techniques to obfuscate string of their malware which can render string search ineffective.

In such cases, we can use FLOSS (FireEye Labs Obfuscated String Solver). It uses several techniques to deobfuscate and extract strings that would not be otherwise found using a string search.

## Fingerprint Malware

A Hash function is used to identify a malware uniquely. Commonly used methods for calculating File hashes:

- md5sum
- sha1sum
- sha256sum

### Finding similar hashes

Some types of hashes can help identify the similarity among different files.

### Imphash

It stands for Import Hash. Imports are functions that an executable file imports from other files or DLLs. 

The imphash is the hash of the function calls/libraries that a malware sample imports and the order in which these libraries are present in the sample. This helps identify samples from the same threat groups or performing similar activities.

### Fuzzy Hash / SSDEEP

A fuzzy hash is a Context Triggered Piecewise Hash. This hash is calculated by dividing a file into pieces and calculating the hashes of the different pieces. This method creates multiple inputs with similar sequence of bytes, even though the whole file might be different.

We can use utilities like ssdeep to perform fuzzy hashing. 

## Signature-based Detection

### Signatures

Signatures are a way to identify if a particular file has a particular type of content.

This pattern is often a sequence of bytes in a file, with or without any context regarding where it is found.

Signatures are often used to identify patterns in a file, determine whether a file is malicious, and identify suspected behavior and malware family.

### Yara rules

These are a type of signature-based rule. It can identify information based on binary and textual patterns, such as hexadecimal and strings contained within a file. 

### Proprietary Signatures: Antivirus Scans

Antivirus Scanning helps identify if a file is malicious with high confidence. Antivirus software will often mention the signature that the file has hit, which might hint at the file's functionality.
For e.g. VirusTotal

### Capa

It helps identify the capabilities found in a PE file. It reads the file and tries to identify the behavior of the file based on signatures such as imports, strings, mutexes and other artifacts present in the file.

## The PE Header

The PE files consists of a sequence of bits stored on the disk. This sequence is in a specific format. The initial bits define the characteristics of the PE file and explain how to read the rest of the data. The initial part of the PE file is called a PE header.

## Linked Libraries, imports and functions

A PE file does not contain all of its code to perform all the tasks. It often reuses code from libraries, often provided by Microsoft as part of Windows. Often, certain functions from these libraries are imported by the PE file. The PE header contains information about the libraries that a PE file uses and the function it imports from those libraries.

## Identifying Packed Executables

To thwart the analysis, malware authors pack the original sample inside a shell-type code that obfuscates the properties of the actual malware sample. This technique is called packing, and resultant PE file is called a packed PE file. This greatly reduces the effectiveness of analysis techniques.

We can identify packed executables by analyzing the PE header of a malware sample. 

The PE header contains important information such as the number of sections, permissions of different sections, sizes of the sections, etc. This information can give us pointers to help identify if the malware sample is packed and, if so, which type of packer has packed the executable.