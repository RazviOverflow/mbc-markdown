<table>
<tr>
<td><b>ID</b></td>
<td><b>B0002</b></td>
</tr>
<tr>
<td><b>Objective(s)</b></td>
<td><b><a href="../anti-behavioral-analysis">Anti-Behavioral Analysis</a></b></td>
</tr>
<tr>
<td><b>Related ATT&CK Techniques</b></td>
<td><b>Debugger Evasion (<a href="https://attack.mitre.org/techniques/T1622/">T1622</a>)</b></td>
</tr>
<tr>
<td><b>Anti-Analysis Type</b></td>
<td><b>Evasion</b></td>
</tr>
<tr>
<td><b>Version</b></td>
<td><b>2.3</b></td>
</tr>
<tr>
<td><b>Created</b></td>
<td><b>1 August 2019</b></td>
</tr>
<tr>
<td><b>Last Modified</b></td>
<td><b>27 April 2024</b></td>
</tr>
</table>


# Debugger Evasion

Debugger evasion is employed by malware to foil a debugger and avoid analysis. For example, to hinder the malware analyst and debugger, malware may use exception handling to execute non-obvious execution paths (exception misdirection method) or may use several parallel threads (parallel threads method). Additional debugger evasion methods are described below.

A thorough reference for anti-debugging, both detection and evasion, is given in [[1]](#1).

The related **Debugger Evasion ([T1622](https://attack.mitre.org/techniques/T1622/))** ATT&CK technique was defined subsequent to this MBC behavior.

## Methods

|Name|ID|Description|
|---|---|---|
|**Block Interrupts**|B0002.001|Block interrupt (via hooking) 1 and/or 3 to prevent debuggers from working.|
|**Break Point Clearing**|B0002.002|Intentionally clearing software or hardware breakpoints.|
|**Byte Stealing**|B0002.003|Move or copy the first bytes / instructions of the original code elsewhere. AKA stolen bytes or code splicing. For example, a packer may incorporate the first few instructions of the original EntryPoint (EP) into its unpacking stub before the tail transition in order to confuse automated unpackers and novice analysts. This can make it harder for rebuilding and may bypass breakpoints if set prematurely.|
|**Change SizeOfImage**|B0002.004|Changing this value during run time can prevent some debuggers from attaching and also confuses some unpackers and dumpers.|
|**Code Integrity Check**|B0002.005|Check that the unpacking code is unmodified. Variation exists where unpacking code is part of the "key" used to unpack, therefore any Software Breakpoints during debugging causes unpacking to completely fail or result in malformed unpacked code.|
|**Exception Misdirection**|B0002.006|Using exception handling (SEH) to cause flow of program to non-obvious paths.|
|**Get Base Indirectly**|B0002.007|CALL to a POP; finds base of code or data, often the packed version of the code; also used often in obfuscated/packed shellcode.|
|**Guard Pages**|B0002.008|Encrypt blocks of code individually and decrypt temporarily only upon execution. This method is related to Unprotect technique U0102.|
|**Hook Interrupt**|B0002.009|Modification of interrupt vector or descriptor tables.|
|**Import Obfuscation**|B0002.010|Add obfuscation between imports calls and APIs.|
|**Inlining**|B0002.011|Variation of static linking where full API code inserted everywhere it would have been called.|
|**Loop Escapes**|B0002.012|Use SEH or other methods to break out of a loop instead of a conditional jump.|
|**Malloc Use**|B0002.013|Instead of unpacking into a pre-defined section/segment (ex: .text) of the binary, use malloc() / VirtualAlloc() to create a new segment. This makes keeping track of memory locations across different runs more difficult, as there is no guarantee that malloc/VirtualAlloc will assign the same address range each time.|
|**Modify PE Header**|B0002.014|Any part of the header is changed or erased.|
|**Nanomites**|B0002.015|int3 with code replacement table; debugs itself.|
|**Obfuscate Library Use**|B0002.016|LoadLibrary API calls or direct access of kernel32 via PEB (fs[0]) pointers, used to rebuild IAT or just obfuscate library use.|
|**Parallel Threads**|B0002.017|Use several parallel threads to make analysis harder.|
|**Pipeline Misdirection**|B0002.018|Take advantage of pipelining in modern processors to misdirect debugging, emulation, or static analysis tools. An unpacker can assume a certain number of opcodes will be cached and then proceed to overwrite them in memory, causing a debugger/emulator/analyzer to follow different code than is normally executed.|
|**Pre-Debug**|B0002.019|Prevents debugger from attaching to process or to break until after the code of interest has been executed.|
|**Relocate API Code**|B0002.020|Relocate API code in separate buffer (calls don’t lead to imported DLLs).|
|**Return Obfuscation**|B0002.021|Overwrite the RET address on the stack or the code at the RET address. Variation seen that writes to the start-up code or main module that called the malware's WinMain or DllMain.|
|**RtlAdjustPrivilege**|B0002.022|Calling RtlAdjustPrivilege to either prevent a debugger from attaching or to detect if a debugger is attached.|
|**Section Misalignment**|B0002.023|Some analysis tools cannot handle binaries with misaligned sections.|
|**Self-Debugging**|B0002.024|Debug itself to prevent another debugger to be attached.|
|**Self-Unmapping**|B0002.025|UnmapViewOfFile() on itself.|
|**Static Linking**|B0002.026|Copy locally the whole content of API code.|
|**Stolen API Code**|B0002.027|A variation of "byte stealing" where the first few instructions or bytes of an API are executed in user code, allowing the IAT to point into the middle of an API function. This confuses IAT rebuilders such as ImpRec and Scylla and may bypass breakpoints.|
|**Tampering**|B0002.028|Erase or corrupt specific file parts to prevent rebuilding (header, packer stub, etc.).|
|**Thread Timeout**|B0002.029|Setting dwMilliseconds in WaitForSingleObject to a small number will timeout the thread before the analyst can step through and analyze the code executing in the thread. Modifying this via patch, register, or stack to the value `0xFFFFFFFF`, the **INFINITE** constant circumvents this anti-debugging technique.|
|**Use Interrupts**|B0002.030|The unpacking code relies on use of int 1 or int 3, or it uses the interrupt vector table as part of the decryption "key".|

## Use in Malware

|Name|Date|Method|Description|
|---|---|---|---|
|**Fake Adobe Flash Update OS X**|2016|--|Malware contains code that manually detects a debugger. [[2]](#2)|
|**Dridex**|2015|--|[[3]](#3)|
|[**Redhip**](../xample-malware/redhip.md)|2011|--|Redhip uses general approaches to detecting user level debuggers (e.g., Process Environment Block 'Being Debugged' field), as well as specific checks for kernel level debuggers like SOFTICE. [[3]](#3)|
|[**Vobfus**](../xample-malware/vobfus.md)|2016|--|Vobfus uses GetModuleHandle API to check for the presence of a debugger. [[4]](#4)|

## Detection

|Tool: capa|Mapping|APIs|
|---|---|---|
|[hide thread from debugger](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-evasion/hide-thread-from-debugger.yml)|Debugger Evasion (B0002)|NtSetInformationThread, ZwSetInformationThread, GetCurrentThread|
|[switch active desktop](https://github.com/mandiant/capa-rules/blob/master/host-interaction/gui/switch-active-desktop.yml)|Debugger Evasion (B0002)|user32.CreateDesktop, user32.SwitchDesktop|

|Tool: CAPE|Mapping|APIs|
|---|---|---|
|[antidebug_guardpages](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_guardpages.py)|Debugger Evasion (B0002)|VirtualProtectEx, NtAllocateVirtualMemory, NtProtectVirtualMemory|
|[antidebug_guardpages](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_guardpages.py)|Debugger Evasion::Guard Pages (B0002.008)|VirtualProtectEx, NtAllocateVirtualMemory, NtProtectVirtualMemory|
|[antidebug_ntcreatethreadex](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_ntcreatethreadex.py)|Debugger Evasion (B0002)|NtCreateThreadEx|
|[debugs_self](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/debugs_self.py)|Debugger Evasion (B0002)|CreateProcessInternalW|
|[debugs_self](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/debugs_self.py)|Debugger Evasion::Self-Debugging (B0002.024)|CreateProcessInternalW|

## References

<a name="1">[1]</a> https://anti-reversing.com/Downloads/Anti-Reversing/The_Ultimate_Anti-Reversing_Reference.pdf

<a name="2">[2]</a> https://web.archive.org/web/20210225195315/https://www.synack.com/blog/analyzing-the-anti-analysis-logic-of-an-adware-installer/

<a name="3">[3]</a> https://web.archive.org/web/20200815134441/https://www.fireeye.com/blog/threat-research/2011/01/the-dead-giveaways-of-vm-aware-malware.html

<a name="4">[4]</a> https://securitynews.sonicwall.com/xmlpost/revisiting-vobfus-worm-mar-8-2013/
