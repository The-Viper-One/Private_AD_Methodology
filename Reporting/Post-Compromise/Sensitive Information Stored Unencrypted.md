**Description**

A number of sensitive information sources were found to be stored in an unencrypted and insecure manner. However, the circumstances surrounding it are generally not intentional and are default design of Windows configuration. There were two particular instances where this was found to be the case.

**Notepad / Notepad ++**

Notepad++ was found to be installed on a number of systems. Notepad++ automatically saves files that are not explicitly discarded. These files are stored as .bin files within the user AppData directoryunder the following location:

```C:\Users\(UserName)\AppData\Roaming\Notepad++\backup\*```
These files can be trivially read using applications such as strings.exe. The same behaviour is now found on Windows 11 and Windows Server 2025 systems with the default Windows notepad application under the following directory:

```C:\Users\<UserName>\AppData\Local\Packages\Microsoft.WindowsNotepad_<RandomValue>\LocalState\TabState```
Administrators and users may often paste sensitive information that could be required shortly later without realising this information will be stored on disk in an unencrypted format. **It was found during the engagement that Notepad / Notepad++ files were found to contain a number of credentials in plaintext such as MySQL , MSSQL and Wazuh credentials**.

**Console History**

PowerShell console history commands are automatically written to the following location:

```%USERPROFILE%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt```
As this file automatically captures command history it can often contain sensitive command line credentials invoked by users and administrators. **During the engagement it was found that sensitive information such as Azure secret keys and Domain administrator credentials were found in plaintext within these files.**

**Remediation**

There are limited technical controls that can be applied to prevent exposure of sensitive information within Notepad and PowerShell console history. At the time of writing, no Group Policy or registry control exists to prevent this behaviour. The best known advice is for organisations to deploy scripts that run at user logon / logoff or regular intervals to wipe the contents of these files. When doing so, the following locations should be purged:

**Notepad++**

```C:\Users\<UserName>\AppData\Roaming\Notepad++\backup\*```

**Notepad**

```C:\Users\<UserName>\AppData\Local\Packages\Microsoft.WindowsNotepad_<RandomValue>\LocalState\TabState```

**Console Host History**

```%USERPROFILE%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt```

Outside of technical controls, advice should be given to end users and administrators about data being stored in text editors to reduce sensitive data exposure.

**References**
* https://www.bulletproof.co.uk/blog/fantastic-notes-and-where-to-find-them
