<table>
<tr>
<td><b>ID</b></td>
<td><b>B0001</b></td>
</tr>
<tr>
<td><b>Objective(s)</b></td>
<td><b><a href="../anti-behavioral-analysis">Anti-Behavioral Analysis</a></b></td>
</tr>
<tr>
<td><b>Related ATT&CK Techniques</b></td>
<td><b>None</b></td>
</tr>
<tr>
<td><b>Anti-Analysis Type</b></td>
<td><b>Detection</b></td>
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


# Debugger Detection

Malware detects whether it's being executed inside a debugger by checking for artifacts such as DLLs, processes, and registry keys [[1]](#1). If malware detects a debugger,  it may change its execution path or change its code to initiate a crash [[2]](#2).

While many methods are listed in the table below, among the most commonly used are:
- Using APIs such as IsDebuggerPresent, CheckRemoteDebuggerPresent, and OutputDebugString
- Reading the BeingDebugged bit (is it a 1 or 0) in the Process Environment Block (PEB)
- Checking whether a software breakpoint instruction is used (INT3; 0xCC opcode)

Details on detecting debuggers can be found in the references.

## Methods

|Name|ID|Description|
|---|---|---|
|**API Hook Detection**|B0001.001|Module bounds based [[7]](#7).|
|**Anti-debugging Instructions**|B0001.034|Malware code contains mnemonics related to anti-debugging (e.g., rdtsc, icebp).|
|**CheckRemoteDebuggerPresent**|B0001.002|The kernel32!CheckRemoteDebuggerPresent function calls NtQueryInformationProcess with ProcessInformationClass parameter set to 7 (ProcessDebugPort constant).This method is related to Unprotect technique U0121.|
|**Check Processes**|B0001.038|The malware may check running processes for specific strings such as "malw" to detect a analysis environment.|
|**CloseHandle**|B0001.003|(NtClose); If an invalid handle is passed to the CloseHandle function and a debugger is present, then an EXCEPTION_INVALID_HANDLE (0xC0000008) exception will be raised. [[7]](#7) This method is related to Unprotect technique U0114.|
|**Debugger Artifacts**|B0001.004|Malware may detect a debugger by its artifact (window title, device driver, exports, etc.).|
|**Hardware Breakpoints**|B0001.005|(SEH/GetThreadContext); Debug registers will indicate the presence of a debugger. See [[7]](#7) for details. This method is related to Unprotect technique U0127.|
|**Interruption**|B0001.006|If an interruption is mishandled by the debugger, it can cause a single-byte instruction to be inadvertently skipped, which can be detected by malware. Examples include Interrupt 0x2d and Interrupt 1 [[7]](#7). This method is related to Unprotect technique U0129.|
|**IsDebuggerPresent**|B0001.008|The kernel32!IsDebuggerPresent API function call checks the PEB BeingDebugged flag to see if the calling process is being debugged. It returns 1 if the process is being debugged, 0 otherwise. This is one of the most common ways of debugger detection.This method is related to Unprotect technique U0122.|
|**Memory Breakpoints**|B0001.009|(PAGE_GUARD); Guard pages trigger an exception the first time they are accessed and can be used to detect a debugger. See [[7]](#7) for details. This method is related to Unprotect technique U0102.|
|**Memory Write Watching**|B0001.010|[[7]](#7)|
|**Monitoring Thread**|B0001.011|Malware may spawn a monitoring thread to detect tampering, breakpoints, etc.|
|**NtQueryInformationProcess**|B0001.012|Calling NtQueryInformationProcess with its ProcessInformationClass parameter set to 0x07 (ProcessDebugPort constant) will cause the system to set ProcessInformation to -1 if the process is being debugged. Calling with ProcessInformationClass set to 0x0E (ProcessDebugFlags) or 0x11 (ProcessDebugObject) are used similarly. Testing "ProcessDebugPort" is equivalent to using the kernel32!CheckRemoteDebuggerPresent API call (see next method). This method is related to Unprotect technique U0120.|
|**NtQueryObject**|B0001.013|The ObjectTypeInformation and ObjectAllTypesInformation flags are checked for debugger detection. This method is related to Unprotect technique U0118.|
|**NtSetInformationThread**|B0001.014|Calling this API with a fake class length or thread handle can indicate whether it is hooked. After calling NtSetInformationThread properly, the HideThreadFromDebugger flag is checked with the NtQueryInformationThread API. [[7]](#7)This method is related to Unprotect technique U0119.|
|**NtYieldExecution/SwitchToThread**|B0001.015|[[7]](#7)|
|**OutputDebugString**|B0001.016|(GetLastError); The OutputDebugString function will demonstrate different behavior depending whether or not a debugger is present. See [[7]](#7) for details. This method is related to Unprotect technique U0117.|
|**Page Exception Breakpoint Detection**|B0001.017|[[7]](#7)|
|**Parent Process**|B0001.018|(Explorer.exe); Executing an application by a debugger will result in the parent process being the debugger process rather than the shell process (Explorer.exe) or the command line. Malware checks its parent process; if it's not explorer.exe, it's assumed to be a debugger. [[7]](#7)|
|**Process Environment Block**|B0001.019|The Process Environment Block (PEB) is a Windows data structure associated with each process that contains several fields, such as "BeingDebugged," "NtGlobalFlag," and "IsDebugged". Testing the value of this PEB field of a particular process can indicate whether the process is being debugged. Testing "BeingDebugged" is equivalent to using the kernel32!IsDebuggerPresent API call (see separate method). This method is related to Unprotect technique U0113.|
|**Process Environment Block BeingDebugged**|B0001.035|The BeingDebugged field is tested to determine whether the process is being debugged.|
|**Process Environment Block IsDebugged**|B0001.037|The IsDebugged field is tested to determine whether the process is being debugged.|
|**Process Environment Block NtGlobalFlag**|B0001.036|The NtGlobalFlag field is tested to determine whether the process is being debugged. This method is related to Unprotect technique U0111.|
|**Process Jobs**|B0001.020|[[7]](#7)|
|**ProcessHeap**|B0001.021|Process heaps are affected by debuggers. Malware can detect a debugger by checking heap header fields such as Flags (debugger present if value greater than 2) or ForceFlags (debugger present if value greater than 0).This method is related to Unprotect technique U0112.|
|**RtlAdjustPrivilege**|B0001.022|Malware may call RtlAdjustPrivilege to detect if a debugger is attached (or to prevent a debugger from attaching).|
|**SeDebugPrivilege**|B0001.023|(Csrss.exe); Using the OpenProcess function on the csrss.exe process can detect a debugger. [[7]](#7)|
|**SetHandleInformation**|B0001.024|(Protected Handle)|
|**Software Breakpoints**|B0001.025|(INT3/0xCC) This method is related to Unprotect technique U0105.|
|**Stack Canary**|B0001.026|Similar to the anti-exploitation method of the same name, malware may try to detect mucking with values on the stack.|
|**TIB Aware**|B0001.027|Malware may access information in the Thread Information Block (TIB) for debug detection or process obfuscation detection. The TIB can be accessed as an offset of the segment register (e.g., fs:[20h]).|
|**TLS Callbacks**|B0001.029|[[7]](#7)|
|**Timing/Delay Check**|B0001.028|Malware may compare time between two points to detect unusual execution, such as the (relative) massive delays introduced by debugging. This method is related to Unprotect techniques U110 and U1308.|
|**Timing/Delay Check GetTickCount**|B0001.032|Malware uses GetTickCount function in a timing/delay check. This method is related to Unprotect technique U0125.|
|**Timing/Delay Check QueryPerformanceCounter**|B0001.033|Malware uses QueryPerformanceCounter in a timing/delay check. This method is related to Unprotect techniques U110 and U1309.|
|**UnhandledExceptionFilter**|B0001.030|The UnhandledExceptionFilter function is called if no registered exception handlers exist, but it will not be reached if a debugger is present. See [[7]](#7) for details. Row 11 This method is related to Unprotect technique U0108.|
|**WudfIsAnyDebuggerPresent**|B0001.031|Includes use of WudfIsAnyDebuggerPresent, WudfIsKernelDebuggerPresent, WudfIsUserDebuggerPresent.|

## Use in Malware

|Name|Date|Method|Description|
|---|---|---|---|
|[**Redhip**](../xample-malware/redhip.md)|2011|--|Redhip uses general approaches to detecting user level debuggers (e.g., Process Environment Block 'Being Debugged' field), as well as specific checks for kernel level debuggers like SOFTICE. [[4]](#4)|
|[**Redhip**](../xample-malware/redhip.md)|2011|B0001.032|Redhip checks for a time delay using GetTickCount. [[15]](#15)|
|[**Redhip**](../xample-malware/redhip.md)|2011|B0001.035|Redhip checks for PEB BeingDebugged flag. [[15]](#15)|
|[**Gamut**](../xample-malware/gamut.md)|2014|B0001.006|The malware detects debuggers using an INT 03h trap. [[8]](#8)|
|[**Gamut**](../xample-malware/gamut.md)|2014|B0001.008|The malware detects debuggers using IsDebuggerPresent. [[8]](#8)|
|[**Rombertik**](../xample-malware/rombertik.md)|2015|B0001.016|The malware calls the Windows API OutputDebugString function 335,000 times. [[9]](#9)|
|[**Rombertik**](../xample-malware/rombertik.md)|2015|B0001.032|The malware checks for a time delay via GetTickCount. [[15]](#15)|
|[**Rombertik**](../xample-malware/rombertik.md)|2015|B0001.038|An anti-analysis function within the packer is called to check the username and filename of the executing process for strings like “malwar”, “sampl”, “viru”, and “sandb”. [[9]](#9)|
|[**Poison Ivy**](../xample-malware/poison-ivy.md)|2005|B0001.005|Poison Ivy Variant checks for breakpoints and exits immediately if found. [[13]](#13)|
|[**Poison Ivy**](../xample-malware/poison-ivy.md)|2005|B0001.008|Poison Ivy uses the IsDebuggerPresent API function call to check if the process is running in a debugger. [[13]](#13)|
|[**Matanbuchus**](../xample-malware/matanbuchus.md)|2021|B0001.032|The malware calls GetTickCount64 to retrieve timestamp. Malware executes Sleep and Beep in a repeated loop for 10 times. [[11]](#11) [[12]](#12)|
|[**Ursnif**](../xample-malware/ursnif.md)|2016|B0001.028|The malware manipulates TLS Callbacks while injecting to a child process. [[12]](#12)|
|[**Dark Comet**](../xample-malware/dark-comet.md)|2008|B0001.032|The malware checks for a time delay via GetTickCount. [[15]](#15)|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|B0001.025|The malware checks for software breakpoints. [[15]](#15)|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|B0001.032|The malware checks for a time delay via GetTickCount. [[15]](#15)|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|B0001.034|The malware executes anti-debugging instructions. [[15]](#15)|
|[**UP007**](../xample-malware/up007.md)|2016|B0001.032|The malware checks for a time delay via GetTickCount. [[15]](#15)|

## Detection

|Tool: capa|Mapping|APIs|
|---|---|---|
|[check for trap flag exception](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-trap-flag-exception.yml)|Debugger Detection (B0001)|--|
|[check for software breakpoints](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-software-breakpoints.yml)|Debugger Detection::Software Breakpoints (B0001.025)|--|
|[check process job object](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-process-job-object.yml)|Debugger Detection (B0001)|kernel32.QueryInformationJobObject, kernel32.OpenProcess|
|[check for PEB BeingDebugged flag](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-peb-beingdebugged-flag.yml)|Debugger Detection::Process Environment Block BeingDebugged (B0001.035)|--|
|[check for time delay via GetTickCount](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-time-delay-via-gettickcount.yml)|Debugger Detection::Timing/Delay Check GetTickCount (B0001.032)|--|
|[check for protected handle exception](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-protected-handle-exception.yml)|Debugger Detection::SetHandleInformation (B0001.024)|SetHandleInformation, CloseHandle|
|[check for OutputDebugString error](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-outputdebugstring-error.yml)|Debugger Detection::OutputDebugString (B0001.016)|kernel32.SetLastError, kernel32.GetLastError, kernel32.OutputDebugString|
|[check for unexpected memory writes](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-unexpected-memory-writes.yml)|Debugger Detection::Memory Write Watching (B0001.010)|kernel32.GetWriteWatch|
|[check for kernel debugger via shared user data structure](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-kernel-debugger-via-shared-user-data-structure.yml)|Debugger Detection (B0001)|--|
|[check for time delay via QueryPerformanceCounter](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-time-delay-via-queryperformancecounter.yml)|Debugger Detection::Timing/Delay Check QueryPerformanceCounter (B0001.033)|--|
|[check for hardware breakpoints](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-hardware-breakpoints.yml)|Debugger Detection::Hardware Breakpoints (B0001.005)|kernel32.GetThreadContext|
|[check ProcessDebugPort](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-processdebugport.yml)|Debugger Detection::NtQueryInformationProcess (B0001.012)|NtQueryInformationProcess|
|[check for debugger via API](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-debugger-via-api.yml)|Debugger Detection::CheckRemoteDebuggerPresent (B0001.002)|kernel32.CheckRemoteDebuggerPresent, WUDFPlatform.WudfIsAnyDebuggerPresent, WUDFPlatform.WudfIsKernelDebuggerPresent, WUDFPlatform.WudfIsUserDebuggerPresent|
|[check for debugger via API](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-debugger-via-api.yml)|Debugger Detection::WudfIsAnyDebuggerPresent (B0001.031)|kernel32.CheckRemoteDebuggerPresent, WUDFPlatform.WudfIsAnyDebuggerPresent, WUDFPlatform.WudfIsKernelDebuggerPresent, WUDFPlatform.WudfIsUserDebuggerPresent|
|[check for PEB NtGlobalFlag flag](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/check-for-peb-ntglobalflag-flag.yml)|Debugger Detection::Process Environment Block NtGlobalFlag (B0001.036)|--|
|[execute anti-debugging instructions](https://github.com/mandiant/capa-rules/blob/master/anti-analysis/anti-debugging/debugger-detection/execute-anti-debugging-instructions.yml)|Debugger Detection::Anti-debugging Instructions (B0001.034)|--|
|[PEB access](https://github.com/mandiant/capa-rules/blob/master/lib/peb-access.yml)|Debugger Detection::Process Environment Block (B0001.019)|--|

|Tool: CAPE|Mapping|APIs|
|---|---|---|
|[antidebug_checkremotedebuggerpresent](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_checkremotedebuggerpresent.py)|Debugger Detection (B0001)|CheckRemoteDebuggerPresent, NtQueryInformationProcess|
|[antiav_nthookengine_libs](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_nthookengine_libs.py)|Debugger Detection (B0001)|LdrGetDllHandle, LdrLoadDll|
|[antiav_nthookengine_libs](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_nthookengine_libs.py)|Debugger Detection::API Hook Detection (B0001.001)|LdrGetDllHandle, LdrLoadDll|
|[antidebug_setunhandledexceptionfilter](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_setunhandledexceptionfilter.py)|Debugger Detection (B0001)|SetUnhandledExceptionFilter|
|[antidebug_setunhandledexceptionfilter](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_setunhandledexceptionfilter.py)|Debugger Detection::UnhandledExceptionFilter (B0001.030)|SetUnhandledExceptionFilter|
|[antidebug_addvectoredexceptionhandler](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_addvectoredexceptionhandler.py)|Debugger Detection (B0001)|AddVectoredExceptionHandler|
|[antidebug_outputdebugstring](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_outputdebugstring.py)|Debugger Detection (B0001)|GetLastError, SetLastError, OutputDebugStringW, OutputDebugStringA|
|[antidebug_outputdebugstring](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_outputdebugstring.py)|Debugger Detection::OutputDebugString (B0001.016)|GetLastError, SetLastError, OutputDebugStringW, OutputDebugStringA|
|[antidebug_gettickcount](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_gettickcount.py)|Debugger Detection (B0001)|GetTickCount|
|[antidebug_gettickcount](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_gettickcount.py)|Debugger Detection::Timing/Delay Check GetTickCount (B0001.032)|GetTickCount|
|[antidebug_guardpages](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_guardpages.py)|Debugger Detection (B0001)|VirtualProtectEx, NtAllocateVirtualMemory, NtProtectVirtualMemory|
|[antidebug_guardpages](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_guardpages.py)|Debugger Detection::Memory Breakpoints (B0001.009)|VirtualProtectEx, NtAllocateVirtualMemory, NtProtectVirtualMemory|
|[antidebug_ntsetinformationthread](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_ntsetinformationthread.py)|Debugger Detection (B0001)|NtSetInformationThread|
|[antidebug_ntsetinformationthread](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_ntsetinformationthread.py)|Debugger Detection::NtSetInformationThread (B0001.014)|NtSetInformationThread|
|[antidebug_debugactiveprocess](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/antidebug_debugactiveprocess.py)|Debugger Detection (B0001)|DebugActiveProcess|

### B0001.019 Snippet
<details>
<summary> Anti-Behavioral Analysis::Debugger Detection::Process Environment Block </summary>
SHA256: e33a713b96b45e2b2e0da350c0fdaaf865139607066aadff3b67b0ced82ca8bc
Location: 0x1800270A2
<pre>
mov     rax, qword ptr GS:[0x60]        ; GS:[0x60] contains a pointer to the Windows Process Environment Block on 64-bit versions of Windows.  This command is copying that pointer into the rax register.
</pre>
</details>

## References

<a name="1">[1]</a> S. Yosef,"RASPBERRY ROBIN: ANTI-EVASION HOW-TO & EXPLOIT ANALYSIS," https://research.checkpoint.com/, 18 Apr 2023. [Online]. Available: https://research.checkpoint.com/2023/raspberry-robin-anti-evasion-how-to-exploit-analysis/.  

<a name="2">[2]</a> M. Sikorski and A. Honig, Practical Malware Analysis: The Hands-On Guide to Dissecting Malicious Software, No Starch Press, 2012. 

<a name="3">[3]</a> Peter Ferrie, "The 'Ultimate' Anti-Debugging Reference," 4 May 2011. https://anti-reversing.com/Downloads/Anti-Reversing/The_Ultimate_Anti-Reversing_Reference.pdf.

<a name="4">[4]</a> https://web.archive.org/web/20200815134441/https://www.fireeye.com/blog/threat-research/2011/01/the-dead-giveaways-of-vm-aware-malware.html

<a name="5">[5]</a> Ayoub Faouzi (LordNoteworthy), Al-Khaser v0.79. https://github.com/LordNoteworthy/al-khaser

<a name="6">[6]</a> Nicolas Falliere, Symantec, "Windows Anti-Debug Reference," 11 September 2007. https://www.symantec.com/connect/articles/windows-anti-debug-reference.

<a name="7">[7]</a> Anti Debugging Tricks, Al-Khaser. https://github.com/LordNoteworthy/al-khaser/wiki/Anti-Debugging-Tricks

<a name="8">[8]</a> https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/gamut-spambot-analysis/

<a name="9">[9]</a> https://blogs.cisco.com/security/talos/rombertik

<a name="10">[10]</a> https://www.mandiant.com/sites/default/files/2021-09/rpt-poison-ivy.pdf

<a name="11">[11]</a> https://www.0ffset.net/reverse-engineering/matanbuchus-loader-analysis/

<a name="12">[12]</a> https://www.cyberark.com/resources/threat-research-blog/inside-matanbuchus-a-quirky-loader

<a name="13">[13]</a> https://www.fortinet.com/blog/threat-research/deep-analysis-of-new-poison-ivy-variant

<a name="14">[14]</a> https://www.fireeye.com/blog/threat-research/2017/11/ursnif-variant-malicious-tls-callback-technique.html

<a name="15">[15]</a> capa v4.0, analyzed at MITRE on 10/12/2022
