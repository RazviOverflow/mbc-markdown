<table>
<tr>
<td><b>ID</b></td>
<td><b>E1014</b></td>
</tr>
<tr>
<td><b>Objective(s)</b></td>
<td><b><a href="../defense-evasion">Defense Evasion</a></b></td>
</tr>
<tr>
<td><b>Related ATT&CK Techniques</b></td>
<td><b>Rootkit (<a href="https://attack.mitre.org/techniques/T1014">T1014</a>)</b></td>
</tr>
<tr>
<td><b>Version</b></td>
<td><b>3.2</b></td>
</tr>
<tr>
<td><b>Created</b></td>
<td><b>1 August 2019</b></td>
</tr>
<tr>
<td><b>Last Modified</b></td>
<td><b>28 April 2024</b></td>
</tr>
</table>


# Rootkit

Behaviors of a rootkit: "A rootkit is a collection of computer software, typically malicious, designed to enable access to a computer or areas of its software that is not otherwise allowed and often masks its existence or the existence of other software." [[1]](#1)


See ATT&CK: **Rootkit ([T1014](https://attack.mitre.org/techniques/T1014/))**.

Rootkits may hide artifacts (kernel modules, services, threads, userspace libraries), prevent actions, API unhooking (prevents API hooks installed by the malware instance from being removed), file access (prevents access to the file system, including specific files and/or directories associated with the malware instance), file deletion (prevents files and/or directories associated with the malware instance from being deleted), memory access (prevents access to system memory where the malware instance stores code or data), native API hooking (prevents other software from hooking native system APIs), registry access (prevents access to the Windows registry, either entire registry or particular registry keys/values), and registry deletion (prevents deletion of registry keys and/or values associated with the malware instance).

## Methods

|Name|ID|Description|
|---|---|---|
|**Application Rootkit**|E1014.m12|Application rootkits operate by exchanging standard application files with rootkit files, or changing applications by injecting code or patching.|
|**Bootloader**|E1014.m13|A bootloader rootkit modifies the bootloader, enabling activation before the operating system is started, also known as a Bootkit. See ATT&CK: [Bootkit](https://attack.mitre.org/techniques/T1542/003/).|
|**Hardware/Firmware Rootkit**|E1014.m14|A firmware rootkit compromises hardware (e.g. network card, hard drive), system BIOS, UEFI firmware. LoJack is the first in-the-wild UEFI rootkit. See ATT&CK: [System Firmware](https://attack.mitre.org/techniques/T1542/001/).|
|**Hypervisor/Virtualized Rootkit**|E1014.m15|A hypervisor (virtualized) rootkit hosts the target operating system as a virtual machine, enabling interception of all hardware calls, also called a virtual-machine-based rootkit (VMBR).|
|**Kernel Mode Rootkit**|E1014.m16|Rootkit operates by adding or replacing code in OS, device drivers, loadable kernel modules (LKM). Related to ATT&CK: [Kernel Modules and Extensions](https://attack.mitre.org/techniques/T1547/006/)|
|**Memory Rootkit**|E1014.m17|A memory rootkit hids in RAM. Behaviors may include methods to prevent memory access. The lifespan of a memory rootkit is short because it disappears after a system reboot.|

## Use in Malware

|Name|Date|Method|Description|
|---|---|---|---|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|--|Hupigon has certain variants that may have rootkit functionality. [[3]](#3)|
|[**Stuxnet**](../xample-malware/stuxnet.md)|2010|E1014.m16|Stuxnet registers custom resource drives signed with a legitimate Realtek digital certificate. [[4]](#4)|

## Detection

Rootkits can be detected by detecting primary rootkit behaviors: Hide Artifacts, Impair Defenses, and Highjack Execution Flow. Hidden artifacts include kernel modules (hides use of kernel modules used by the malware instance), services (hides any system services that the malware instance creates or injects itself into), threads (hides one or more threads that belong to the malware instance), and userspace libraries (hides use of userspace libraries used by the malware instance). 

Rootkits can also be detected via memory dump analysis or virtual machine introspection.

|Tool: CAPE|Mapping|APIs|
|---|---|---|
|[spicyhotpot_behavior](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/rootkit_spicyhotpot.py)|Rootkit (E1014)|--|
|[accesses_primary_patition](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/bootkit.py)|Rootkit (E1014)|--|
|[direct_hdd_access](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/bootkit.py)|Rootkit (E1014)|--|
|[enumerates_physical_drives](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/bootkit.py)|Rootkit (E1014)|--|
|[physical_drive_access](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/bootkit.py)|Rootkit (E1014)|--|

## References

<a name="1">[1]</a> https://en.wikipedia.org/wiki/Rootkit

<a name="2">[2]</a> https://www.cyber.nj.gov/threat-center/threat-profiles/trojan-variants/poison-ivy

<a name="3">[3]</a> https://www.f-secure.com/v-descs/backdoor_w32_hupigon.shtml

<a name="4">[4]</a> https://docs.broadcom.com/doc/security-response-w32-stuxnet-dossier-11-en

